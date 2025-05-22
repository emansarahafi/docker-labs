# TP Docker : Volumes et Journalisation
## A. Création et Montage de Volumes
### A.1 Créer un volume
Créez un volume appelé test1 :
```
docker volume create --name test1
```
Listez les volumes pour vérifier la création :
```
docker volume ls
```
Lancez un conteneur CentOS et montez le volume sur /www/website :
```
docker run -it -v test1:/www/website centos:7 bash
```
Dans le conteneur, accédez au répertoire :
```
cd /www/website
```
Créez un fichier dans le volume :
```
echo 'hello there' > test.txt
```
Sortez du conteneur sans l'arrêter : CTRL+D

### A.2 Créer une image depuis un conteneur avec volume
Identifiez l’ID du conteneur précédent (docker ps -a) puis créez une image :
```
docker commit <container ID> test:1.0
```
Lancez un nouveau conteneur avec cette image :
```
docker run -it test:1.0 bash
```
Vérifiez la présence de /www/website.
Le dossier existe mais le fichier test.txt est absent : les volumes sont externes au système de fichiers de l’image.

📌 Remarque : docker commit ne capture pas les données de volumes, uniquement le point de montage.

### A.3 Localiser le volume sur l’hôte
Inspectez le volume pour identifier le chemin sur l’hôte :
```
docker volume inspect test1
```
Passez en root :
```
sudo su
```
Accédez au chemin du volume :
```
cd /var/lib/docker/volumes/test1/_data
```
Vérifiez que test.txt est là :
```
ls
```
Créez un fichier supplémentaire :
```
touch test2.txt
```
Revenez à l'utilisateur courant :
```
exit
```
Redémarrez le conteneur si nécessaire, puis entrez à nouveau :
```
docker start <container ID>
docker exec -it <container ID> bash
```
Vérifiez que les deux fichiers sont présents :
```
cd /www/website
ls
```
### A.4 Suppression de volumes
Tentez de supprimer le volume (échoue si utilisé) :
```
docker volume rm test1
```
Supprimez le conteneur :
```
docker rm -f <container ID>
```
Vérifiez que le volume est encore là :
```
docker volume ls
```
Vérifiez que les fichiers sont toujours présents :
```
sudo ls /var/lib/docker/volumes/test1/_data
```
Supprimez définitivement le volume :
```
docker volume rm test1
```
Vérifiez la suppression :
```
docker volume ls
```
### A.5 Conclusion
Les volumes permettent de conserver des données au-delà de la durée de vie des conteneurs.

Les données stockées dans un volume ne sont pas intégrées à l’image même avec docker commit.

Les volumes sont montés dans le conteneur mais stockés physiquement sur l’hôte dans /var/lib/docker/volumes.

## B. Cas d'utilisation des volumes : journalisation des logs
### B.1 Configurer une application avec journalisation
Créez un volume pour les logs :
```
docker volume create --name nginx_logs
```
Créez un dossier public_html dans votre workspace (ex : /workspaces/public_html).

Créez un fichier index.html avec du contenu :
```
echo "<h1>Hello from Docker</h1>" > /workspaces/public_html/index.html
```
Lancez un conteneur NGINX en montant les deux volumes :
```
docker run -d -P --name nginx_server \
  -v /workspaces/public_html:/usr/share/nginx/html \
  -v nginx_logs:/var/log/nginx \
  training/nginx:17.06
```
Identifiez le port exposé :
```
docker ps
```
Accédez à l’URL dans un navigateur : http://localhost:<PORT>

Accédez au shell du conteneur :
```
docker exec -it nginx_server bash
```
Modifiez le contenu de index.html depuis le conteneur.

Rafraîchissez la page dans le navigateur et observez le nouveau contenu.

Faites aussi une modification depuis l’hôte dans public_html/index.html, puis rechargez.

### B.2 Visualiser les logs côté hôte
Retournez dans le conteneur :
```
docker exec -it nginx_server bash
```
Allez dans le dossier des logs :
```
cd /var/log/nginx
```
Vérifiez la présence de access.log et error.log:
```
ls
```
Surveillez les accès :
```
tail -f access.log
```
Sur l’hôte, inspectez le volume :
```
docker volume inspect nginx_logs
```
Allez dans le répertoire :
```
cd /var/lib/docker/volumes/nginx_logs/_data
```
Vérifiez et suivez les logs :
```
ls
tail -f access.log
```
Rafraîchissez votre navigateur pour générer des entrées de log en direct.

### B.3 Partage de volume (readonly)
Vérifiez que nginx_server tourne encore (docker ps), sinon relancez-le.

Lancez un conteneur CentOS avec montage en lecture seule :
```
docker run -it --rm \
  -v nginx_logs:/data/mylogs:ro \
  centos:7 bash
```
Allez dans le dossier partagé :
```
cd /data/mylogs
```
Listez les fichiers de log :
```
ls
```
Essayez d’écrire :
```
touch test.txt  # Échoue
```
📌 Important : Le mode :ro interdit l'écriture — pratique pour la sécurité et les applications de monitoring.

### B.4 Conclusion
Les volumes facilitent le partage de données entre conteneurs et avec l’hôte.

Ils sont très utiles pour la collecte de logs.

Le mode lecture seule protège contre la corruption accidentelle ou malveillante.

## Bonnes pratiques :

Utilisez des volumes nommés pour la persistance.

Évitez de stocker des données critiques dans le système de fichiers interne du conteneur.

Montez les volumes avec les bons droits d’accès (rw, ro) selon le besoin.
