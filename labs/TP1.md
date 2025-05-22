# TP Docker : Exploration complète des conteneurs
## A. Exécution et inspection d'un conteneur
### A.1 Exécution de conteneurs
Démarrage d'un conteneur avec une commande simple :
```
docker run centos:7 echo "hello world"
```
Exécution d’une commande différente dans un autre conteneur :
```
docker run centos:7 ps -ef
```
Comparaison avec le système hôte :
```
ps -ef
```
### A.2 Liste des conteneurs
Lister les conteneurs en cours d'exécution :
```
docker ps
```
Lister tous les conteneurs (y compris ceux arrêtés) :
```
docker ps -a
```
### A.3 Conclusion
Vous avez exécuté votre premier conteneur avec docker run et découvert l’importance du PID 1, qui joue un rôle critique : sa terminaison entraîne l’arrêt du conteneur.

## B. Conteneurs interactifs
### B.1 Écriture dans des conteneurs
Démarrer un conteneur interactif :
```
docker run -it centos:7 bash
```
Créer un fichier dans le conteneur :
```
touch test.txt
ls -l
```
Quitter :
```
exit
```
Recréer un conteneur identique :
```
docker run -it centos:7 bash
```
Rechercher test.txt → il n’existe pas. Quitter.

### B.2 Reconnexion aux conteneurs
Lister les conteneurs arrêtés :
```
docker ps -a
```
Redémarrer un conteneur existant :
```
docker start -ai <CONTAINER_ID>
```
Exécuter un processus dans un conteneur démarré :
```
docker exec <CONTAINER_ID> ps -ef
```
Lire le fichier précédemment créé :
```
docker exec <CONTAINER_ID> ls -l
```
Quitter :
```
exit
```
### B.3 Liste des options utiles
Voir les ID complets :
```
docker ps -a --no-trunc
```
Lister uniquement les IDs :
```
docker ps -aq
```
Dernier conteneur créé :
```
docker ps -l
```
Filtrer sur le status :
```
docker ps -a --filter "status=exited"
```
### B.4 Conclusion
Chaque conteneur a sa propre couche R/W. Modifier un conteneur n’impacte pas les autres conteneurs créés depuis la même image.

## C. Conteneurs détachés et logs
### C.1 Mode détaché (background)
Exécution classique :
```
docker run centos:7 ping 127.0.0.1 -c 10
```
Exécution en arrière-plan :
```
docker run -d centos:7 ping 127.0.0.1
```
Visualisation des logs :
```
docker logs <CONTAINER_ID>
```
### C.2 Attachement et détachement
S’attacher à un conteneur :
```
docker attach <CONTAINER_ID>
```
Ctrl+C tue le conteneur car cela tue le PID 1.

Exécution en détaché interactif :
```
docker run -d -it centos:7 ping 127.0.0.1
```
Détachement propre :
```
Ctrl+P Ctrl+Q
```
### C.3 Options avancées de logs
Dernières lignes :
```
docker logs --tail 5 <CONTAINER_ID>
```
Suivre les logs :
```
docker logs -f <CONTAINER_ID>
```
### C.4 Conclusion
Vous avez appris à exécuter en arrière-plan, vous attacher, et inspecter les logs. Attention : tuer le PID 1 met fin au conteneur.

## D. Démarrage, arrêt, inspection et suppression
### D.1 Démarrage et redémarrage
Démarrer un Tomcat :
```
docker run -d tomcat
docker ps
```
Arrêter :
```
docker stop <CONTAINER_ID>
```
Redémarrer :
```
docker start -ai <CONTAINER_ID>
```
Détachement + suivi des logs :
```
docker start <CONTAINER_ID>
docker logs --tail 10 -f <CONTAINER_ID>
```
Forcer l'arrêt :
```
docker kill <CONTAINER_ID>
```
### D.2 Inspection des conteneurs
Inspection brute :
```
docker inspect <CONTAINER_ID>
```
IP du conteneur :
```
docker inspect <CONTAINER_ID> | grep IPAddress
```
PID 1 (non fiable avec grep) :
```
docker inspect <CONTAINER_ID> | grep Cmd
```
Formattage Go Template :
```
docker inspect --format '{{.Config.Cmd}}' <CONTAINER_ID>
docker inspect --format '{{.NetworkSettings.IPAddress}}' <CONTAINER_ID>
```
Inspecter toute la config :
```
docker inspect --format='{{json .Config}}' <CONTAINER_ID>
```
### D.3 Suppression des conteneurs
Lancer 3 conteneurs :
```
docker run -d centos:7 sleep 300
docker run -d centos:7 sleep 300
docker run -d centos:7 sleep 300
```
Arrêter un conteneur :
```
docker stop <CONTAINER_ID>
Filtrer les conteneurs exited :
```
docker ps -a --filter "status=exited"
```
Supprimer un conteneur arrêté :
```
docker rm <CONTAINER_ID>
```
Supprimer un conteneur actif (forcer) :
```
docker rm -f <CONTAINER_ID>
```
### D.4 Conclusion
Vous savez désormais utiliser :

start, stop, kill, rm

inspect pour récupérer les métadonnées d’un conteneur

## E. Création d'images interactives
### E.1 Créer un compte Docker
👉 Créez un compte Docker sur : https://hub.docker.com
Vous y trouverez :
Docker Hub
Docker Store
Docker Cloud
Votre <Docker ID> vous sera utile plus tard.

### E.2 Modifier un conteneur
Démarrer un conteneur Ubuntu interactif :
```
docker run -it ubuntu bash
```
Installer un logiciel :
```
apt update
apt install -y cowsay
```
Quitter :
```
exit
```
Vérifier les changements :
```
docker ps -a
docker diff <CONTAINER_ID>
```
