[← Chapitre précédent](06-Docker-Compose.md) · [Sommaire](Readme.md)

# Aide-mémoire

Les commandes vues dans le cours, regroupées par usage.

## Installation et diagnostic

| Commande | Effet |
|---|---|
| `docker version` | versions du client et du moteur |
| `docker info` | état détaillé du moteur |
| `docker compose version` | version du plugin Compose |
| `docker run --rm hello-world` | test de bout en bout |

## Images

| Commande | Effet |
|---|---|
| `docker pull nginx:1.30-alpine` | télécharger une image |
| `docker image ls` | lister les images locales |
| `docker build -t nom:tag .` | construire une image depuis le dossier courant |
| `docker build --check .` | vérifier le Dockerfile sans construire |
| `docker history nom:tag` | voir les couches d'une image |
| `docker tag source cible` | ajouter un nom ou un tag |
| `docker login` / `docker push nom:tag` | publier sur un registre |
| `docker image rm nom:tag` | supprimer une image |

## Conteneurs

| Commande | Effet |
|---|---|
| `docker run -d --name web -p 8080:80 image` | lancer en arrière-plan avec un port publié |
| `docker run --rm -it image sh` | shell interactif dans un conteneur jetable |
| `docker ps` / `docker ps -a` | conteneurs actifs / tous |
| `docker logs -f web` | suivre les journaux |
| `docker exec -it web sh` | ouvrir un shell dans un conteneur actif |
| `docker stop web` / `docker start web` | arrêter / redémarrer |
| `docker rm -f web` | arrêter et supprimer |
| `docker inspect web` | configuration complète (JSON) |
| `docker stats --no-stream` | consommation de ressources |

## Volumes et réseaux

| Commande | Effet |
|---|---|
| `docker volume create pgdata` | créer un volume nommé |
| `-v pgdata:/var/lib/postgresql` | monter un volume (PostgreSQL 18 et plus) |
| `-v "$(pwd)/site:/usr/share/nginx/html:ro"` | monter un dossier de l'hôte en lecture seule |
| `docker network create appnet` | créer un réseau avec résolution DNS par nom |
| `--network appnet` | brancher un conteneur au lancement |
| `docker network connect appnet db` | brancher un conteneur existant |
| `-p 127.0.0.1:8081:8080` | publier un port pour la seule machine locale |

## Compose

| Commande | Effet |
|---|---|
| `docker compose config` | valider le fichier et voir la configuration résolue |
| `docker compose up -d --build` | construire et démarrer |
| `docker compose ps` | état des services |
| `docker compose logs -f service` | journaux d'un service |
| `docker compose exec service commande` | commande dans un service actif |
| `docker compose down` | tout arrêter, garder les volumes |
| `docker compose down -v` | tout arrêter et supprimer les volumes |

## Ménage

| Commande | Effet |
|---|---|
| `docker system df` | espace disque utilisé |
| `docker container prune` | supprimer les conteneurs arrêtés |
| `docker image prune` | supprimer les images sans tag |
| `docker system prune` | conteneurs arrêtés, réseaux inutilisés, images sans tag, cache de build |

## Références

- Documentation officielle : [docs.docker.com](https://docs.docker.com/)
- Référence de la CLI : [docs.docker.com/reference/cli/docker](https://docs.docker.com/reference/cli/docker/)
- Référence du Dockerfile : [docs.docker.com/reference/dockerfile](https://docs.docker.com/reference/dockerfile/)
- Spécification Compose : [docs.docker.com/reference/compose-file](https://docs.docker.com/reference/compose-file/)
- Bonnes pratiques de construction : [docs.docker.com/build/building/best-practices](https://docs.docker.com/build/building/best-practices/)
- Notes de version de Docker Engine : [docs.docker.com/engine/release-notes](https://docs.docker.com/engine/release-notes/)

---

[← Chapitre précédent](06-Docker-Compose.md) · [Sommaire](Readme.md)
