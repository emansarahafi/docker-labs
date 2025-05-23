# TP – Démarrer et scaler une application Docker Compose : Dockercoins
## Objectifs pédagogiques
Comprendre le déploiement multi-conteneurs avec Docker Compose

Construire, taguer et pousser des images Docker sur Docker Hub

Démarrer une application distribuée via docker-compose

Suivre les logs et comprendre les interactions inter-services

Mettre à l’échelle dynamiquement un service avec docker-compose up --scale

## 1. Préparation de l’application Dockercoins
### 1.1 Cloner le dépôt GitHub
```
git clone https://github.com/docker-training/orchestration-workshop.git
cd orchestration-workshop/dockercoins
```
Cette application comprend 5 services :

rng : Générateur de nombres aléatoires

hasher : Effectue des hachages SHA256

worker : Consomme les services rng + hasher et envoie les résultats à Redis

redis : File d'attente des résultats

webui : Frontend Web affichant le taux de hash



## 2. Démarrage de l'application
### 2.1 Lancement en mode interactif
```
docker-compose up
```
Cela démarre les 5 services.
Les logs s’affichent dans le terminal.
Arrêt avec CTRL+C.

### 2.2 Lancement en arrière-plan
```
docker-compose up -d
```
L'application tourne en background

Vérifie les conteneurs :
```
docker-compose ps
docker ps
```
🧪 Test : Lance un autre conteneur indépendant via docker run et compare docker-compose ps avec docker ps.

### 2.3 Accès à l'application
Dans ton navigateur, visite :
http://<ip_machine>:8000
Tu verras un graphique représentant le nombre de hachages/seconde (env. 4 h/s)

## 3. Logs d’une application Compose
### 3.1 Afficher les logs de tous les services
```
docker-compose logs
```
### 3.2 Logs d’un service particulier
```
docker-compose logs worker
```
### 3.3 Suivre en temps réel (comme tail -f)
```
docker-compose logs -f webui
```
✋ Contrôles pratiques :

CTRL+S pour pause

CTRL+Q pour reprendre

## 4. Mise à l’échelle d’un service
### 4.1 Scaling du worker
```
docker-compose up -d --scale worker=2
```
📌 Vérifie :

```
docker-compose ps
```
➡️ Deux conteneurs worker sont maintenant actifs.

### 4.2 Observations
Recharge l’interface web → le taux de hash devrait doubler (~8 h/s).

Affiche les logs :
```
docker-compose logs worker
```
## 5. Nettoyage de l’environnement
```
docker-compose down
```
Supprime tous les conteneurs, réseaux et volumes liés à l’application.

## Conclusion
Le fichier docker-compose.yml est la source de vérité décrivant l’architecture d’une application multi-services.

Grâce à Docker Compose :

Les services interagissent via leur nom de service (grâce au DNS interne Docker).

La scalabilité est simple via --scale.

La supervision/logging reste un point d’attention important pour le debug.
