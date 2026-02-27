# TP DevOps Docker - Hammou Ayoub

Mini projet de conteneurisation d'une application web statique avec Nginx, et pipeline CI/CD avec GitHub Actions vers GHCR.

## Objectif

- Construire une image Docker de l'application
- Lancer l'application en local
- Publier automatiquement l'image sur `ghcr.io` via GitHub Actions
- Versionner les images avec des tags Git

## Prerequis

- Un compte GitHub
- Docker Desktop installe et demarre
- Git installe

Verification rapide :

```bash
docker --version
docker run hello-world
git --version
```

## Structure du projet

```text
.github/workflows/docker-deploy.yml
nginx/nginx.conf
src/index.html
src/style.css
src/app.js
Dockerfile
.dockerignore
README.md
```

## Executer le projet en local

1. Construire l'image Docker

```bash
docker build -t devops-tp-docker:latest .
```

2. Lancer le conteneur

```bash
docker run -d --name devops-tp -p 8080:8080 devops-tp-docker:latest
```

3. Ouvrir l'application

`http://localhost:8080`

4. Verifier le conteneur

```bash
docker ps
docker logs devops-tp
docker inspect devops-tp
```

5. Arreter et supprimer

```bash
docker stop devops-tp
docker rm devops-tp
```

Note : dans ce projet, Nginx ecoute sur le port `8080` (dans `nginx/nginx.conf`), donc le mapping local est `-p 8080:8080`.

## Pipeline CI/CD (GitHub Actions)

Le workflow est dans `.github/workflows/docker-deploy.yml`.

Il se declenche sur :

- `push` sur `main` et `test`
- `pull_request` sur `main` et `test`
- tags Git commencant par `v` (ex: `v1.0.0`)

Etapes principales :

- checkout du code
- login sur `ghcr.io`
- generation des tags/metadata
- build et push de l'image Docker

## Publier l'image (premier deploiement)

```bash
git add .
git commit -m "Initial commit: Dockerized application with CI/CD"
git push origin main
```

Ensuite :

1. Aller dans l'onglet **Actions** de GitHub
2. Verifier que le workflow est vert
3. Aller dans **Packages** pour voir l'image publiee

## Utiliser l'image depuis GHCR

1. Rendre le package public (Package settings -> Public)
2. Pull et run :

```bash
docker pull ghcr.io/<username>/<repo>:main
docker run -d -p 8080:8080 ghcr.io/<username>/<repo>:main
```

Exemple `repo` pour ce projet : `devops-tp-docker-hammou-ayoub`.

## Versioning avec tags

Creer un tag :

```bash
git tag -a v1.0.0 -m "Version 1.0.0"
git push origin v1.0.0
```

Le workflow generera automatiquement des images versionnees (ex: `v1.0.0`, `1.0`, etc.).

## Commandes Docker utiles

```bash
docker images
docker ps -a
docker logs <container-id>
docker exec -it <container-id> sh
docker image prune -a
```
