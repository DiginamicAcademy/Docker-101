# syntax=docker/dockerfile:1

# Image de base officielle, version épinglée (branche stable de nginx, variante Alpine)
FROM nginx:1.30-alpine

# Le contenu du site : ce qui change le plus souvent vient en dernier
COPY site/ /usr/share/nginx/html/

# Documente le port écouté (ne publie rien à lui seul)
EXPOSE 80

# Docker vérifie régulièrement que le serveur répond
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD ["wget", "-q", "-O", "/dev/null", "http://127.0.0.1/"]
