# TP3 – Volumes Docker

## Objectifs
- Créer un volume Docker.
- Utiliser ce volume dans un conteneur.
- Vérifier la persistance des données.

## Étapes

### 1. Créer un volume
```bash
docker volume create monvolume
docker volume inspect monvolume
```

### 2. Lancer un conteneur avec ce volume
```bash
docker run -it --name avecvolume -v monvolume:/data alpine sh
```

### 3. Écrire des données
```sh
echo "bonjour docker" > /data/test.txt
```

### 4. Supprimer et relancer un autre conteneur avec le même volume
```bash
docker rm -f avecvolume
docker run -it -v monvolume:/data alpine cat /data/test.txt
```
