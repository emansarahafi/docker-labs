# TP5 – Docker Compose

## Objectifs
- Définir une stack `web + client` via un `docker-compose.yml`.
- Lancer les services.
- Tester la communication.

## Arborescence
```
TP5_DockerCompose/
├── docker-compose.yml
└── client/
    └── Dockerfile
```

## docker-compose.yml
```yaml
version: "3.8"
services:
  web:
    image: nginx
  client:
    build: ./client
    depends_on:
      - web
```

## client/Dockerfile
```Dockerfile
FROM alpine
RUN apk add --no-cache curl
CMD ["sh", "-c", "curl web"]
```

## Étapes
```bash
docker compose up --build
```
