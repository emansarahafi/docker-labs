# TP4 – Réseaux Docker

## Objectifs
- Créer un réseau Docker.
- Connecter deux conteneurs à ce réseau.
- Vérifier leur communication.

## Étapes

### 1. Créer un réseau
```bash
docker network create monreseau
```

### 2. Lancer un serveur nginx et un client alpine dans le réseau
```bash
docker run -d --name web --network monreseau nginx
docker run -it --rm --network monreseau alpine sh
```

### 3. Depuis le client, tester la communication
```sh
wget -O- http://web
```

### 4. Vérifier les détails du réseau
```bash
docker network inspect monreseau
```
