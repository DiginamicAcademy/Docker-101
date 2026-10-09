# Docker 101

<center>

![Logo Docker](assets/docker-logo.png)

</center>

Un cours pour (re)voir les bases de Docker, pas à pas, et repartir avec un Docker fonctionnel sur sa machine.

## 🎯 Objectifs pédagogiques

À la fin de ce cours, vous saurez :

1. Expliquer ce qu'est un conteneur, ce qui le distingue d'une machine virtuelle, et le vocabulaire de base (image, conteneur, registre, Dockerfile, volume, réseau).
2. Installer Docker sur Windows, macOS ou Linux, et vérifier que l'installation fonctionne.
3. Lancer, inspecter, arrêter et supprimer des conteneurs.
4. Écrire un `Dockerfile`, construire une image et la lancer.
5. Conserver des données avec un volume et faire communiquer des conteneurs sur un réseau.
6. Décrire une application multi-conteneurs dans un fichier `compose.yaml` et la piloter avec `docker compose`.

## ❔ Pourquoi ce cours ?

Docker est devenu le format d'échange de fait entre développement, intégration continue et production : une même image tourne sur le poste du développeur, dans la CI et sur le serveur. La plupart des cours plus avancés (Kubernetes, CI/CD, observabilité, bases de données…) supposent ce socle acquis. Ce cours le pose, ou le remet à plat.

## Prérequis

- Être à l'aise avec un terminal (bash, zsh ou PowerShell).
- Une machine 64 bits avec au moins 8 Go de RAM et la virtualisation matérielle activée dans le BIOS/UEFI.
- Les droits administrateur, au moins pour la première installation (activation de WSL 2 sous Windows, `sudo` sous Linux).
- Une connexion Internet pour télécharger l'installeur et les images.

## Le parcours

Chaque étape est un fichier. On les suit **dans l'ordre** : chacune s'appuie sur la base laissée par la précédente.

| # | Étape | Ce qu'on y fait |
|---|---|---|
| [01](01-Pourquoi-Docker.md) | Pourquoi Docker ? | conteneur vs machine virtuelle, vocabulaire, architecture du moteur |
| [02](02-Installer-Docker.md) | Installer Docker | Docker Desktop (Windows, macOS), Docker Engine (Linux), vérification, dépannage |
| [03](03-Premiers-conteneurs.md) | Premiers conteneurs | `run`, `ps`, `logs`, `exec`, ports, cycle de vie, nettoyage |
| [04](04-Construire-une-image.md) | Construire une image | `Dockerfile`, `docker build`, couches et cache, `.dockerignore`, bonnes pratiques |
| [05](05-Volumes-et-reseaux.md) | Volumes et réseaux | persistance des données, montages, réseaux et résolution de noms |
| [06](06-Docker-Compose.md) | Docker Compose | un `compose.yaml` à trois services, `up`, `logs`, `down` |
| [A](A-Aide-memoire.md) | Aide-mémoire | les commandes du cours sur une page, liens de référence |

## Les fichiers d'exemple

Le dossier `demo/` contient les fichiers construits au fil des chapitres 04 à 06 (`Dockerfile`, `site/index.html`, `compose.yaml`, `.env.example`). Ils permettent de rattraper une étape ou de vérifier une correction.

## Versions de référence

Vérifiées le 5 octobre 2026. Docker publie souvent : les commandes du cours restent valables, seuls les numéros changent.

| Composant | Version de référence |
|---|---|
| Docker Engine | 29.8.x |
| Docker Desktop | 4.93.x |
| Docker Compose | v5.5.x |
| Image `nginx` (branche stable) | 1.30 |
| Image `postgres` | 18 |
| Image `adminer` | 5 |
| Image `ubuntu` (LTS) | 26.04 |
