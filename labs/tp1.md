
# Exercices Docker : Exécution et Inspection de Conteneurs

## Préambule

```bash
$ ls -l
$ echo 'Bonjour…' > test.txt
$ ls -l
```

## A. Exécution et inspection d'un conteneur

### A.1 Exécution de conteneurs

1. Démarrer un conteneur et observer le résultat :
```bash
$ docker run centos:7 echo "hello world"
```

2. Créer un autre conteneur et exécuter un autre processus :
```bash
$ docker run centos:7 ps -ef
```

3. Comparer avec la commande `ps -ef` sur l'hôte.

### A.2 Liste des conteneurs

1. Répertorier les conteneurs en cours :
```bash
$ docker ps
```

2. Répertorier les conteneurs arrêtés et en cours :
```bash
$ docker ps -a
```

### A.3 Conclusion

> Le processus PID 1 est central dans un conteneur : son arrêt entraîne l'arrêt du conteneur.

## B. Conteneurs interactifs

### B.1 Écriture dans des conteneurs

1. Connexion à un shell bash en mode interactif :
```bash
$ docker run -it centos:7 bash
```

2. Explorer le système de fichiers et créer un fichier.

3. Quitter :
```bash
$ exit
```

4. Autres commandes utiles :
```bash
$ docker ps -a
$ docker start <container ID>
$ docker exec <container ID> ps -ef
$ docker exec -it <container ID> bash
$ docker ps -a -q
$ docker ps -a --filter "exited=1"
$ docker ps -a --filter "exited=0"
```

### B.2 Reconnexion aux conteneurs

- Redémarrer un conteneur arrêté et s'y reconnecter :
```bash
$ docker ps -a
$ docker start <container ID>
$ docker exec -it <container ID> bash
```

- Vérifier que `test.txt` est bien présent.

### B.3 Liste des options de conteneur

```bash
$ docker ps -a --no-trunc
$ docker ps -q
$ docker ps -l
$ docker ps -a --filter "exited=0"
$ docker ps -a --filter "exited=1"
```

### B.4 Conclusion

> Les modifications faites dans un conteneur sont locales à celui-ci.

## C. Conteneurs détachés et logs

### C.1 Exécution en arrière-plan

```bash
$ docker run centos:7 ping 127.0.0.1 -c 10
$ docker run -d centos:7 ping 127.0.0.1
$ docker logs <container ID>
```

### C.2 Connexion à la sortie du conteneur

```bash
$ docker attach <container ID>
# Quitter avec Ctrl+C (tue le conteneur)
$ docker run -d -it centos:7 ping 127.0.0.1
# Détacher avec Ctrl+P Ctrl+Q
```

### C.3 Options de journalisation

```bash
$ docker logs --tail 5 <container ID>
$ docker logs -f <container ID>
```

Autres commandes utiles :
```bash
$ docker start <container ID>
$ docker inspect <container ID>
$ docker inspect <container ID> | grep IPAddress
$ docker inspect --format='{{.Config.Cmd}}' <container ID>
```

### C.4 Conclusion

> Si le PID 1 meurt, le conteneur est stoppé.

## D. Démarrage, arrêt, inspection et suppression

```bash
$ docker run -d tomcat
$ docker ps
$ docker stop <container ID>
$ docker start -a <container ID>
$ docker logs --tail 10 <container ID>
$ docker kill <container ID>
```

---

> Ce TP permet de mieux comprendre le fonctionnement des conteneurs Docker à travers des commandes simples mais essentielles.
