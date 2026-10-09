---
marp: true
theme: default
style: |
  section {background-color: #121114}
  h1,h2,h3 {color: #8393f0}
  p,ul,li,td,th {color: #ccc}
  section table {font-size: 0.85em}
  section table td {background-color: #121114}
  section table th {background-color: #272133; font-weight: bolder}
  pre {background-color: #17151a; color: #ccc}
  blockquote {border: 2px green solid; border-left-width: 15px; padding: 0.5em 15px;font-style: italic }
paginate: true
header: "Docker 101"
footer: "![height:20px](https://raw.githubusercontent.com/DiginamicInternal/PublicAssets/refs/heads/main/Logo-diginamic-color-blk.png)"
---

![height:180px](assets/docker-logo.png)

# Docker 101

### Les bases de Docker, pas à pas

<!-- Prévoir que chacun ait lancé le téléchargement de l'installeur dès le début de la séance. -->

---

## 🎯 Objectifs pédagogiques

À la fin de ce cours, vous saurez :

1. Expliquer ce qu'est un conteneur et le vocabulaire de base
2. Installer Docker et vérifier l'installation
3. Lancer, inspecter, arrêter et supprimer des conteneurs
4. Écrire un `Dockerfile` et construire une image
5. Conserver des données et relier des conteneurs
6. Piloter une application avec `docker compose`

---

## Le parcours

| #  | Étape                 |
|----|-----------------------|
| 01 | Pourquoi Docker ?     |
| 02 | Installer Docker      |
| 03 | Premiers conteneurs   |
| 04 | Construire une image  |
| 05 | Volumes et réseaux    |
| 06 | Docker Compose        |

Chaque étape s'appuie sur la précédente : on les suit **dans l'ordre**.

---

# 1. Pourquoi Docker ?

---

## « Ça marche sur ma machine »

Une application dépend de bien plus que son code :

- un runtime dans une version précise
- des bibliothèques système
- des variables d'environnement et des fichiers de configuration

Docker **empaquette l'application et ses dépendances** dans une **image**, qui s'exécute à l'identique partout.

---

## Conteneur ou machine virtuelle ?

| | Machine virtuelle | Conteneur |
|---|---|---|
| Ce qui est isolé | un système complet | un processus |
| Noyau | un par VM | partagé avec l'hôte |
| Taille typique | Go | Mo |
| Démarrage | dizaines de secondes | moins d'une seconde |

Isolation par le noyau Linux : **namespaces** (ce que le processus voit) et **cgroups** (ce qu'il consomme).

> Sur Windows et macOS, Docker Desktop fait tourner une petite VM Linux pour vous.

---

## Le vocabulaire

| Terme | En une phrase |
|---|---|
| **Image** | modèle en lecture seule, en couches |
| **Conteneur** | instance en cours d'exécution d'une image |
| **Dockerfile** | recette de construction d'une image |
| **Registre** | serveur d'images (Docker Hub par défaut) |
| **Tag** | version d'une image : `nginx:1.30-alpine` |
| **Volume** | stockage qui survit au conteneur |
| **Réseau** | réseau virtuel entre conteneurs |

---

## Sous le capot

```text
docker (CLI) ──API──▶ dockerd ──▶ containerd ──▶ runc ──▶ processus isolé
                         │
                         └──pull / push──▶ registre
```

- La CLI n'exécute rien : elle parle à l'API du moteur
- Images et exécution suivent le standard **OCI** : une image Docker tourne aussi avec Podman ou Kubernetes

---

# 2. Installer Docker

---

## Desktop ou Engine ?

| | Docker Desktop | Docker Engine |
|---|---|---|
| Systèmes | Windows, macOS, Linux | Linux |
| Contenu | moteur, CLI, Compose, interface, VM intégrée | moteur, CLI, plugins Compose et Buildx |
| Licence | gratuite sous conditions | open source (Apache 2.0) |

**Licence Desktop** : gratuite pour usage personnel, enseignement, open source non commercial et entreprises de **moins de 250 salariés et moins de 10 M$ de CA**. Sinon, abonnement payant.

---

## Windows : Docker Desktop + WSL 2

- Windows 10 22H2 (19045) ou Windows 11 23H2 (22631) et plus
- WSL ≥ 2.1.5, 8 Go de RAM, virtualisation activée

```powershell
wsl --install        # ou wsl --update, en administrateur
Start-Process 'Docker Desktop Installer.exe' -Wait -ArgumentList 'install', '--user'
```

Installation **par utilisateur** recommandée : pas de droits administrateur pour installer ni mettre à jour.

---

## macOS : Docker Desktop

- macOS : version majeure actuelle et les deux précédentes
- Choisir l'installeur **Apple silicon** ou **Intel**
- Glisser Docker dans Applications, lancer, accepter les conditions

```bash
sudo hdiutil attach Docker.dmg
sudo /Volumes/Docker/Docker.app/Contents/MacOS/install
sudo hdiutil detach /Volumes/Docker
```

---

## Linux (Ubuntu 22.04 / 24.04 / 26.04) : Docker Engine

1. Retirer les paquets non officiels (`docker.io`, `docker-compose`…)
2. Ajouter la clé GPG et le dépôt `download.docker.com`
3. Installer :

```bash
sudo apt install docker-ce docker-ce-cli containerd.io \
  docker-buildx-plugin docker-compose-plugin
```

4. Utiliser Docker sans `sudo` :

```bash
sudo usermod -aG docker "$USER" && newgrp docker
```

> Le groupe `docker` équivaut à des droits root sur la machine.

---

## Vérifier l'installation

```bash
docker version            # blocs Client ET Server
docker info
docker compose version    # v5.x à date
docker run --rm hello-world
```

| Symptôme | Piste |
|---|---|
| `Cannot connect to the Docker daemon` | démarrer Docker Desktop / `systemctl start docker` |
| `permission denied … docker.sock` | ajouter l'utilisateur au groupe `docker` |
| Desktop bloqué sur « Starting » | `wsl --update` |

---

# 3. Premiers conteneurs

---

## Ce que fait `docker run`

```bash
docker run --rm hello-world
```

1. L'image est-elle en local ? Sinon **pull** depuis le registre
2. **create** : création du conteneur
3. **start** : lancement du processus
4. Le processus se termine, `--rm` supprime le conteneur

---

## Nommer une image

```text
[registre/][espace/]nom[:tag][@digest]

nginx:1.30-alpine  →  docker.io/library/nginx:1.30-alpine
```

- Registre par défaut : **Docker Hub**
- Tag par défaut : `latest`, qui n'est **pas** forcément la dernière version

> Toujours préciser un tag.

---

## Un serveur web en arrière-plan

```bash
docker run -d --name web -p 8080:80 nginx:1.30-alpine
```

| Option | Rôle |
|---|---|
| `-d` | arrière-plan |
| `--name web` | nom lisible |
| `-p 8080:80` | port **hôte:conteneur** |

→ http://localhost:8080

---

## Observer et intervenir

```bash
docker ps / docker ps -a     # actifs / tous
docker logs -f web           # journaux en continu
docker exec -it web sh       # shell dans le conteneur
docker stats --no-stream     # CPU, mémoire
docker inspect web           # configuration complète
```

Conteneur jetable et interactif :

```bash
docker run --rm -it ubuntu:26.04 bash
```

---

## Cycle de vie

```text
create ──▶ Created ──start──▶ Running ──stop──▶ Exited ──rm──▶ (supprimé)
                                 ▲                 │
                                 └──── start ──────┘
```

```bash
docker stop web   |   docker start web   |   docker rm -f web
```

> Un conteneur vit tant que son **processus principal** vit.

---

## Faire le ménage

```bash
docker system df          # qui occupe l'espace ?
docker container prune    # conteneurs arrêtés
docker image prune        # images sans tag
docker system prune       # tout cela + réseaux inutilisés + cache de build
```

Les volumes ne sont supprimés qu'avec `--volumes`.

---

## Exercice

1. Lancer `nginx:1.30-alpine` nommé `exo`, en arrière-plan, sur le port **8081**
2. Vérifier qu'il répond et que ses journaux défilent
3. Remplacer la page d'accueil par « Bonjour Docker » depuis l'intérieur du conteneur (`/usr/share/nginx/html/index.html`)
4. Supprimer puis recréer le conteneur : la modification a-t-elle survécu ?

---

# 4. Construire une image

---

## Le Dockerfile

```dockerfile
# syntax=docker/dockerfile:1
FROM nginx:1.30-alpine

COPY site/ /usr/share/nginx/html/

EXPOSE 80

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD ["wget", "-q", "-O", "/dev/null", "http://127.0.0.1/"]
```

```bash
docker build -t mon-site:1.0 .
docker run -d --name site -p 8080:80 mon-site:1.0
```

---

## Les instructions essentielles

| Instruction | Quand ? | Rôle |
|---|---|---|
| `FROM` | build | image de départ |
| `WORKDIR` | build | répertoire de travail |
| `COPY` | build | copier depuis le contexte |
| `RUN` | build | exécuter une commande |
| `ENV` / `ARG` | build (+ exécution pour `ENV`) | variables |
| `EXPOSE` | doc | port écouté |
| `USER` | build + exécution | utilisateur |
| `CMD` / `ENTRYPOINT` | **exécution** | commande de démarrage |

---

## Couches et cache

- Chaque `COPY` / `RUN` produit une **couche**, mise en cache
- Dès qu'une entrée change, cette étape **et toutes les suivantes** sont rejouées

```dockerfile
# Change rarement : reste en cache
COPY package.json package-lock.json ./
RUN npm ci
# Change souvent : seule cette étape est rejouée
COPY . .
```

> Ordonner de la moins changeante à la plus changeante.

---

## Bonnes pratiques

- Image **officielle**, **légère**, **tag épinglé**
- Un processus principal par conteneur
- **Aucun secret** dans l'image ; un `.dockerignore` pour `.git`, `.env`…
- Pas de root quand c'est possible (`USER`, images *unprivileged*)
- `docker build --check .` pour vérifier le Dockerfile
- Builds **multi-étapes** pour les langages compilés

---

## Exercice

1. Construire `mon-site:1.0` et le lancer
2. Modifier `site/index.html`, construire `mon-site:1.1` : quelles étapes sont `CACHED` ?
3. Remplacer le conteneur par la version 1.1
4. Lancer `docker build --check .`

---

# 5. Volumes et réseaux

---

## Trois façons de monter des données

| Type | Exemple | Usage |
|---|---|---|
| Volume nommé | `-v pgdata:/var/lib/postgresql` | données persistantes |
| Dossier de l'hôte | `-v "$(pwd)/site:/usr/share/nginx/html:ro"` | développement, configuration |
| `tmpfs` | `--tmpfs /tmp` | temporaire, en mémoire |

> ⚠️ **PostgreSQL 18+** : monter `/var/lib/postgresql`, et non plus `/var/lib/postgresql/data`.

---

## Une base qui survit à son conteneur

```bash
docker volume create pgdata
docker run -d --name db -e POSTGRES_PASSWORD=change-me \
  -v pgdata:/var/lib/postgresql postgres:18-alpine

docker exec db psql -U postgres -c "CREATE TABLE t (msg text); INSERT INTO t VALUES ('toujours là');"
docker rm -f db
# … même docker run qu'au-dessus …
docker exec db psql -U postgres -c "SELECT * FROM t;"
```

---

## Des conteneurs qui se trouvent par leur nom

```bash
docker network create appnet
docker network connect appnet db
docker run -d --name adminer --network appnet \
  -p 127.0.0.1:8081:8080 -e ADMINER_DEFAULT_SERVER=db adminer:5
```

- Réseau **créé par vous** = DNS interne : `db` est résolu
- La base n'a **aucun port publié**
- `127.0.0.1:` limite l'accès à la machine locale

---

# 6. Docker Compose

---

## Une application en un fichier (1/2)

```yaml
services:
  web:
    build: .
    image: mon-site:1.0
    ports: ["127.0.0.1:8080:80"]
  db:
    image: postgres:18-alpine
    environment:
      POSTGRES_USER: app
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:?POSTGRES_PASSWORD manquant}
    volumes: [pgdata:/var/lib/postgresql]
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "app"]
```

Un service = un conteneur ; son nom sert de nom DNS sur le réseau du projet.

---

## Une application en un fichier (2/2)

```yaml
  adminer:
    image: adminer:5
    environment:
      ADMINER_DEFAULT_SERVER: db
    ports: ["127.0.0.1:8081:8080"]
    depends_on:
      db: { condition: service_healthy }
volumes:
  pgdata:
```

- `depends_on` + `service_healthy` : attendre que la base soit **prête**, pas seulement démarrée
- Compose crée seul le réseau du projet et y branche tous les services

---

## Les commandes

```bash
docker compose config          # valider
docker compose up -d --build   # démarrer
docker compose ps              # état et santé
docker compose logs -f db      # journaux
docker compose down            # arrêter (volumes conservés)
docker compose down -v         # … et supprimer les volumes
```

- Fichier `compose.yaml`, commande `docker compose` (avec une espace)
- L'attribut `version:` est **obsolète** : ne plus l'écrire
- Mot de passe dans `.env`, jamais versionné ; on versionne `.env.example`

---

## Exercice

1. `cp .env.example .env`, puis `docker compose up -d --build`
2. Vérifier que `db` est `healthy`
3. Créer une table via adminer (http://localhost:8081)
4. `down` puis `up` : la table est-elle là ? Et après `down -v` ?
5. Retirer `POSTGRES_PASSWORD` de `.env` et lancer `docker compose config`

---

## Pour aller plus loin

- `docker compose watch` : resynchronisation automatique en développement
- Builds multi-étapes, images non root, Docker Scout
- Kubernetes, quand une machine ne suffit plus

Documentation : [docs.docker.com](https://docs.docker.com/)

---

# Des questions ?

### L'aide-mémoire est en annexe du support

Pour toute question [adressez-vous au service pédagogie](mailto:pedagogie@diginamic.fr)
