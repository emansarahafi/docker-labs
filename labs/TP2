# 🐳 TP Docker - Création et gestion d’images avec Dockerfile

## Objectif
Apprendre à créer, optimiser et gérer des images Docker à partir de Dockerfiles, en explorant les concepts de cache de build, CMD, ENTRYPOINT, multi-stage builds et Docker Hub.

---

## 🧱 A. Créer des images avec des Dockerfiles (1/2)

### A.1 Écrire et construire un Dockerfile

1. Créez un dossier `myimage` :
   ```bash
   mkdir myimage && cd myimage
```
Créez un fichier Dockerfile contenant :

```bash
FROM alpine
RUN apk add --no-cache wget
```
Construisez l'image :

```bash
docker build -t <DockerID>/myimage .
```
Vérifiez l’image :

```bash
docker images
docker run -it <DockerID>/myimage sh
wget http://example.com
```

Optionnel : construire depuis STDIN :

```bash
cat Dockerfile | docker build -t <DockerID>/myimage -f - .
```

A.2 Utilisation du cache de build
Ajoutez vim dans une nouvelle instruction RUN :

```bash
RUN apk add --no-cache vim
```
Rebuild et observez :

```bash
docker build -t <DockerID>/myimage .
```

Rebuild sans changement et observez l’utilisation du cache.

Inversez l'ordre des instructions RUN (wget puis vim → vim puis wget), rebuild et observez l’effet sur le cache.

A.3 Utilisation de la commande history
```bash
docker image history <image_id>
```
A.4 Conclusion
Ce TP vous apprend à tirer profit du cache de Docker pour optimiser vos builds. Plus une commande change souvent, plus elle doit être placée en bas du Dockerfile.

⚙️ B. Créer des images avec Dockerfiles (2/2)
B.1 Commandes par défaut : CMD et ENTRYPOINT
Ajoutez une commande par défaut :

```bash
CMD ["ping", "127.0.0.1", "-c", "5"]
```
Rebuild :

```bash
docker build -t <DockerID>/myimage:1.0 .
```
Exécutez sans commande :

```bash
docker run <DockerID>/myimage:1.0
```
Remplacez CMD à l’exécution :

```bash
docker run <DockerID>/myimage:1.0 echo "hello world"
```
Remplacez CMD par :

```bash
ENTRYPOINT ["ping"]
```
Essayez sans argument :

```bash
docker run <DockerID>/myimage:1.0
Essayez avec un argument :

```bash
docker run <DockerID>/myimage:1.0 127.0.0.1
```
B.2 Combinaison CMD + ENTRYPOINT
Modifiez :

```bash
ENTRYPOINT ["ping", "-c", "3"]
CMD ["127.0.0.1"]
```
Exécutez :

```bash
docker run <DockerID>/myimage:1.0 8.8.8.8
docker run <DockerID>/myimage:1.0
```
B.3 Conclusion
CMD fournit les arguments par défaut, ENTRYPOINT la commande fixe. Leur combinaison rend les conteneurs plus prévisibles et réutilisables.

🔨 C. Multi-Stage Builds
C.1 Constructions multi-étapes
Créez un dossier :

```bash
mkdir multi-stage && cd multi-stage
Créez hello.c :
```
```bash
#include <stdio.h>
int main() {
    printf("Hello, Docker!\n");
    return 0;
}
```
Dockerfile de base :

```bash
FROM alpine
RUN apk add build-base
COPY hello.c /app/hello.c
RUN gcc /app/hello.c -o /app/hello
CMD ["/app/hello"]
```
Build et observez la taille :

```bash
docker build -t my-app-fat .
```
Dockerfile multi-stage :

```bash
FROM alpine as build
RUN apk add build-base
COPY hello.c /app/hello.c
RUN gcc /app/hello.c -o /app/bin/hello

FROM alpine
COPY --from=build /app/bin/hello /app/hello
CMD ["/app/hello"]
```
Build image légère :

```bash
docker build -t my-app-small .
docker run --rm my-app-small
```
C.2 Images intermédiaires
Construire le stage intermédiaire :

```bash
docker build -t my-build-stage --target build .
```
Exécuter :

```bash
docker run -it --rm my-build-stage /app/bin/hello
```
C.3 Conclusion
Les builds multi-étapes permettent de séparer build et exécution, et produisent des images plus petites et sécurisées.

📦 D. Gestion des images
D.1 Tags et liste
Télécharger alpine :

```bash
docker pull alpine:latest
docker tag alpine:latest my-alpine:dev
docker images
```
D.2 Partage sur Docker Hub
Connectez-vous à Docker Hub :

```bash
docker login
Retagger l’image :
```
```bash
docker tag my-alpine:dev <DockerID>/my-alpine:dev
docker push <DockerID>/my-alpine:dev
```
Créez une image basée sur my-alpine:dev :

```bash
FROM <DockerID>/my-alpine:dev
RUN apk add --no-cache curl
```
Build et push :

```bash
docker build -t <DockerID>/my-alpine:1.0 .
docker push <DockerID>/my-alpine:1.0
```
Nettoyez :

```bash
docker rmi my-alpine:dev
```
