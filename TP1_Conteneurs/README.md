# TP1 – Gestion des conteneurs Docker

## Objectifs
- Lancer un conteneur en mode détaché et interactif.
- Inspecter un conteneur.
- Accéder aux logs.
- Reconnecter un terminal à un conteneur en cours d’exécution.
- Supprimer proprement un conteneur.

## Étapes

### 1. Lancer un conteneur nginx en mode détaché
```bash
docker run -d --name web nginx
```

### 2. Voir les conteneurs actifs
```bash
docker ps
```

### 3. Inspecter le conteneur
```bash
docker inspect web
```

### 4. Voir les logs du conteneur
```bash
docker logs web
```

### 5. Se connecter à un shell bash dans un conteneur
```bash
docker exec -it web bash
```

### 6. Arrêter puis redémarrer le conteneur
```bash
docker stop web
docker start web
```

### 7. Supprimer le conteneur
```bash
docker rm -f web
```
