# TP2 – Dockerfiles et multistage builds

## Objectifs
- Créer un `Dockerfile` multistage pour builder une app Node.js.
- Construire et tagger une image.
- Pousser l’image sur Docker Hub.

## Arborescence
```
TP2_Dockerfile_Multistage/
├── app/
│   ├── package.json
│   └── index.js
└── Dockerfile
```

## Étapes

### 1. Exemple de Dockerfile multistage
```Dockerfile
FROM node:18 AS builder
WORKDIR /app
COPY . .
RUN npm install

FROM node:18-slim
WORKDIR /app
COPY --from=builder /app .
CMD ["node", "index.js"]
```

### 2. Build de l’image
```bash
docker build -t monuser/node-app .
```

### 3. Tester en local
```bash
docker run --rm -p 3000:3000 monuser/node-app
```

### 4. Publier sur Docker Hub
```bash
docker login
docker push monuser/node-app
```
