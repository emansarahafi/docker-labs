# Lab Docker – Mise en réseau des conteneurs et mappage de ports
## A. Introduction à la mise en réseau de conteneurs
### A.1 Inspection du pont par défaut
Lister les réseaux Docker :
```
docker network ls
```
Vous devriez voir bridge, host, et none.
Inspecter le réseau bridge :
```
docker network inspect bridge
```
Notez le sous-réseau IP et la passerelle attribués.

Afficher les interfaces réseau :
```
ip a
```
Vérifiez que l’interface docker0 correspond bien à l’IP de la passerelle du réseau bridge.

Installer et utiliser brctl pour visualiser les connexions au pont Docker :
```
sudo apt update 
sudo apt install bridge-utils
brctl show docker0
```
Aucune interface connectée au pont à ce stade.

### A.2 Connexion des conteneurs à docker0
Démarrer un conteneur (ex : ubuntu) :
```
docker run --name myubuntu --rm -d --cap-add=NET_ADMIN ubuntu sleep infinity
```
Re-examiner le réseau (interfaces et ponts) :
```
ip a
brctl show docker0
```
Une interface vethXXXX devrait apparaître liée à docker0.

Entrer dans le conteneur et inspecter eth0 :
```
docker exec -it myubuntu bash
apt update && apt install -y iproute2 iputils-ping

ip a
```
Vous verrez une interface eth0@if7 reliée à l’hôte via le lien veth.

### A.3 Définition de Bridge Networks supplémentaires
Créer un réseau personnalisé :
```
docker network create --driver bridge my_bridge
```
Lancer un conteneur connecté à ce réseau :
```
docker run -d --name u1 --network my_bridge nginx
```
Inspecter le conteneur :
```
docker inspect u1
```
Vérifiez la section Networks → my_bridge.

Lancer un autre conteneur (interactif) :
```
docker run -it --name u2 --network my_bridge ubuntu bash
```
Installer ping dans u2 (si image Ubuntu) :
```
apt update && apt install iputils-ping -y
```
Depuis u2, tester la résolution de nom DNS :
```
ping u1
```
Le ping fonctionne → DNS interne Docker actif dans les réseaux personnalisés.

Tester depuis un conteneur dans le réseau bridge :
```
docker run -it --name u3 ubuntu bash
apt update && apt install iputils-ping -y
ping u1
```
❌ Le ping par nom échoue.
✅ Le ping par IP réussit.

Tester la communication entre u2 et u1 via IP :

Depuis u2, pinguer l’IP de u1.
❌ Le ping échoue → isolation inter-réseaux activée par défaut.

### A.4 Conclusion
Les conteneurs sur des réseaux distincts sont isolés par défaut.
La séparation des réseaux doit être exploitée pour la sécurité.

Commandes utiles :
```
docker network ls
docker network inspect <nom>
docker network create --driver bridge <nom>
docker network connect <réseau> <conteneur>
docker container inspect <nom>
```

## B. Cartographie des ports : Container Port Mapping
### B.1 Mappage des ports lors de l’exécution
Lancer un conteneur nginx sans mappage :
```
docker run -d --name nginx1 nginx
```
❌ La page web n’est pas accessible via localhost.

Lancer nginx avec un mappage de port :
```
docker run -d --name nginx2 -p 5000:80 nginx
```
Vérifier le port mapping :
```
docker port nginx2
```
Affiche : 5000/tcp -> 0.0.0.0:80

Accéder au conteneur via le port mappé :
```
curl localhost:5000
```
✅ Le contenu de la page d'accueil nginx s’affiche.

💡 Astuce :
Le mappage de port est essentiel pour rendre les conteneurs accessibles depuis l’extérieur.
