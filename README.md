﻿# TP1 – Docker DevOps

**Auteur :** Vincent Le Meur
**Objectif :** Découvrir et manipuler Docker pour exécuter un serveur web, une base de données, et utiliser Docker Compose.

---

## Partie 1 – Initialisation

- Création du dépôt Git : `tp1-devops`
- Initialisation avec `git init`
- Ajout d’un `README.md` et premiers commits

---

## Partie 3 – Serveur Web avec Docker

### Commandes utilisées

```bash
docker pull httpd
docker run --rm httpd
```

### Lancer le serveur avec volume

```bash
docker run -d -p 8080:80 -v ${PWD}/html:/usr/local/apache2/htdocs httpd
```

### Supprimer le container

```bash
docker ps -a
docker stop <id>
docker rm <id>
```

### Relancer avec `--rm`

```bash
docker run --rm -p 8080:80 -v ${PWD}/html:/usr/local/apache2/htdocs httpd
```

---

## Partie 4 – Dockerfile et build d’image

### Dockerfile

```Dockerfile
FROM httpd
COPY html/ /usr/local/apache2/htdocs/
```

### Build de l’image

```bash
docker build -t mon-apache .
```

### Lancement de l’image custom

```bash
docker run -d -p 8080:80 mon-apache
```

### Comparaison volume vs COPY

```bash

Méthode: `-v` (volume) 
Avantages: Rechargement instantané, pratique pour le dev
Inconvénients: Pas figé dans l’image 
```

```bash
Méthode: `COPY` 
Avantages: Stable, versionnée dans l’image 
Inconvénients: Nécessite un rebuild pour chaque modif 
```

---

## Partie 5 – Base de données MySQL + phpMyAdmin

### Réseau Docker

```bash
docker network create tp1-net
```

### Lancement de MySQL

```bash
docker run -d --name mysql \
  --network tp1-net \
  -e MYSQL_ROOT_PASSWORD=root \
  -e MYSQL_DATABASE=test \
  mysql
```

### Lancement de phpMyAdmin

```bash
docker run -d --name phpmyadmin \
  --network tp1-net \
  -e PMA_HOST=mysql \
  -p 8081:80 \
  phpmyadmin
```

### Accès à phpMyAdmin

- URL : http://localhost:8081
- Utilisateur : root
- Mot de passe : root
- Base de données : `test`
- Table testée : `utilisateurs` (id, nom)

---

## Partie 6 – Docker Compose

### Différences `docker run` vs `docker-compose`

- `docker run` : simple mais verbeux, bon pour tester
- `docker-compose` : définir plusieurs services dans un fichier unique, plus clair et réutilisable

### Fichier `docker-compose.yml`

```yaml
version: '3.8'

services:
  mysql:
    image: mysql
    container_name: mysql
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: test
    networks:
      - backend

  phpmyadmin:
    image: phpmyadmin
    container_name: phpmyadmin
    ports:
      - "8081:80"
    environment:
      PMA_HOST: mysql
    networks:
      - backend

networks:
  backend:
```

### Commandes utilisées

```bash
docker-compose up -d
docker-compose down
```

### Résultat

- phpMyAdmin accessible à http://localhost:8081
- Base de données `test` prête à l'emploi
- Simplification de l’ensemble grâce à Compose

---


