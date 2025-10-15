---
draft: "false"
tags:
  - docker
title: docker
---

# DOCKER

## Qu'est-ce Que Docker ?

Docker permet aux développeurs de conditionner une application avec toutes ses dépendances dans un conteneur léger et portable. Cela garantit que l'application fonctionne de manière cohérente, quel que soit l'environnement (développement, test, production) dans lequel elle est exécutée. Docker a été initialement conçu pour Linux, mais il est désormais compatible avec Windows et macOS, ainsi que divers services cloud comme AWS et Azure.

## Composants Clés De Docker

1.  **Docker Engine** : Le cœur de Docker, un moteur client-serveur qui gère la création, l'exécution et la suppression des conteneurs. Il comprend :
    *   **Docker Daemon** : Un service qui s'exécute en arrière-plan pour gérer les objets Docker (images, conteneurs, réseaux).
    *   **API REST** : Permet aux applications de communiquer avec le daemon pour exécuter des commandes.
    *   **Docker CLI** : Une interface en ligne de commande utilisée pour interagir avec le daemon.
2.  **Docker Compose** : Un outil qui permet de définir et de gérer des applications multi-conteneurs à l'aide de fichiers YAML, facilitant ainsi le déploiement d'environnements complexes.
3.  **Docker Hub** : Un registre public où les utilisateurs peuvent partager et télécharger des images Docker. C'est un lieu centralisé pour stocker des images.
4.  **Docker Swarm** : Une fonctionnalité d'orchestration qui permet de gérer des clusters de conteneurs, facilitant l'équilibrage de charge et le déploiement à grande échelle.

## Comment Fonctionne Docker ?

Docker repose sur des fonctionnalités du noyau Linux telles que les groupes de contrôle (cgroups) et les espaces de noms (namespaces). Ces technologies permettent d'isoler les processus dans des conteneurs, garantissant qu'ils s'exécutent indépendamment les uns des autres tout en partageant le même noyau du système d'exploitation. Cela optimise l'utilisation des ressources sans compromettre la sécurité.

## Quels Problèmes Docker Résout ?

Docker résout plusieurs problèmes courants dans le développement et le déploiement d'applications :

1.  **Problème de dépendances** : Empaqueter les dépendances avec l'application élimine les problèmes de compatibilité.
2.  **Problème d'environnement** : Créer des environnements isolés et personnalisés pour chaque application.
3.  **Problème de portabilité** : Déployer des applications de manière portable et fiable sur différents systèmes.
4.  **Problème de sécurité** : Isoler les applications dans des conteneurs sécurisés réduit les risques.
5.  **Problème de scalabilité** : Mettre à l'échelle facilement les conteneurs pour gérer des charges de travail importantes.
6.  **Problème de gestion des ressources** : Allouer et gérer efficacement les ressources (CPU, mémoire).
7.  **Problème de collaboration** : Partager des environnements de développement cohérents au sein des équipes.

## Concepts Fondamentaux

### 1. Images Docker

Une **image Docker** est un package immuable contenant tout ce qu'une application nécessite pour fonctionner (code, bibliothèques, variables d'environnement, etc.). Elle est construite à partir d'un `Dockerfile`.

#### Les Registres Docker

Un **registre Docker** est un service qui stocke et distribue des images.
*   **Publics** : Comme **Docker Hub**, accessibles à tous.
*   **Privés** : Pour un environnement sécurisé (sur site ou cloud privé) avec un contrôle d'accès.
*   **Exemples** : Docker Hub, Google Container Registry (GCR), Amazon Elastic Container Registry (ECR).

#### Pourquoi Utiliser Des Images Officielles ?

Les images officielles sur Docker Hub offrent :
1.  **Documentation claire** : Facilite leur utilisation.
2.  **Meilleures pratiques** : Conçues avec des configurations optimisées et des mises à jour de sécurité.
3.  **Fiabilité** : Adaptées aux cas d'utilisation les plus courants.

#### Les Tags

Un **tag** est une étiquette (ex: `1.0`, `latest`, `stable`) associée à une image pour en identifier une version spécifique. Ils sont essentiels pour gérer les mises à jour et les déploiements.

### 2. Conteneurs

Les **conteneurs** sont des instances exécutables d'images. Chaque conteneur est isolé, possédant son propre système de fichiers, sa pile réseau et ses ressources limitées, mais partage le noyau de l'OS hôte, ce qui le rend léger et rapide.

### 3. Image vs Conteneur

| Concept | Image Docker | Conteneur Docker |
| :--- | :--- | :--- |
| **Nature** | Modèle, template | Instance d'une image |
| **État** | Immuable (read-only) | Modifiable (read-write) |
| **Cycle de vie** | N'a pas de cycle de vie | Créé, démarré, arrêté, supprimé |
| **Analogie** | Une classe en programmation | Un objet (instance de la classe) |

### 4. Dockerfile

Le **Dockerfile** est un fichier script contenant des instructions pour construire une image Docker. Chaque instruction crée une couche, permettant à Docker de réutiliser les couches inchangées pour optimiser les builds.

### 5. Réseaux (Networking)

Docker fournit plusieurs "drivers" réseau pour contrôler comment les conteneurs communiquent.

*   **`bridge` (par défaut)** : Crée un réseau privé interne à l'hôte. Les conteneurs sur le même réseau peuvent communiquer via leur nom. Pour une communication externe, il faut mapper les ports (`-p 8080:80`).
*   **`host`** : Le conteneur partage la pile réseau de l'hôte. Pas d'isolation, mais plus performant.
*   **`overlay`** : Permet de connecter des conteneurs qui tournent sur des hôtes Docker différents (utilisé par Docker Swarm).
*   **`none`** : Isole complètement le conteneur du réseau.

### 6. Gestion des Données : Volumes vs. Bind Mounts

Pour la persistance des données, Docker propose deux solutions principales :

*   **Volumes** :
    *   **Gérés par Docker** et stockés dans une partie du système de fichiers de l'hôte dédiée à Docker.
    *   C'est la **manière recommandée** de persister des données (bases de données, uploads).
    *   Indépendants de la structure de l'hôte, ils sont portables et plus sûrs.

*   **Bind Mounts** :
    *   Montent un **fichier ou dossier de la machine hôte** directement dans le conteneur.
    *   Très utiles en **développement** pour le hot-reloading du code source.
    *   **Inconvénient** : Couplent le conteneur à la structure de l'hôte, ce qui le rend moins portable.

### 7. Machine Virtuelle (VM) vs. Conteneur Docker

*   **Machine Virtuelle** : C'est un ordinateur complet émulé, avec son propre OS invité. Elle est lourde et lente à démarrer mais offre une isolation totale.
*   **Conteneur Docker** : C'est une "boîte" isolée qui exécute une application en partageant le noyau de l'OS hôte. Il est léger, rapide et portable.

## Guide Pratique

### Les Commandes Docker Essentielles

*   **Gestion des conteneurs** : `docker run`, `docker start`, `docker stop`, `docker rm`, `docker ps`
*   **Gestion des images** : `docker build`, `docker pull`, `docker push`, `docker rmi`, `docker images`
*   **Débogage** : `docker logs`, `docker inspect`, `docker exec` (pour lancer une commande dans un conteneur)
*   **Réseaux et Volumes** : `docker network ...`, `docker volume ...`

### Comment Créer une Image Docker

1.  **Créer un `Dockerfile`** : Un fichier texte avec les instructions de build.
2.  **Définir une image de base** : `FROM ubuntu:latest`
3.  **Ajouter des instructions** :
    *   `RUN` : Exécute une commande.
    *   `COPY` / `ADD` : Copie des fichiers dans l'image.
    *   `WORKDIR` : Définit le répertoire de travail.
    *   `ENV` : Définit une variable d'environnement.
    *   `EXPOSE` : Informe Docker que le conteneur écoute sur un port spécifique.
    *   `CMD` / `ENTRYPOINT` : Spécifie la commande à exécuter au démarrage du conteneur.
4.  **Construire l'image** : `docker build -t mon-image .`
5.  **Exécuter l'image** : `docker run -p 80:80 mon-image`

### Optimisation des Images et du Build

*   **Mise en cache des couches** : Placez les instructions qui changent le moins souvent (ex: `RUN apt-get install`) avant celles qui changent fréquemment (ex: `COPY . .`).
*   **Builds Multi-stage** : Séparez l'environnement de build de l'environnement d'exécution pour produire une image finale beaucoup plus petite et sécurisée.
*   **Regrouper les commandes `RUN`** : Utilisez `&&` pour enchaîner les commandes et réduire le nombre de couches. N'oubliez pas de nettoyer les caches (`apt-get clean`, etc.) dans la même instruction.

### Bonnes Pratiques de Sécurité

1.  **Utiliser des images de base minimalistes** : Préférez `alpine` pour réduire la surface d'attaque.
2.  **Exécuter en tant qu'utilisateur non-root** : Utilisez l'instruction `USER` dans votre Dockerfile pour éviter de tourner en `root`.
    ```dockerfile
    RUN addgroup -S appgroup && adduser -S appuser -G appgroup
    USER appuser
    ```
3.  **Scanner les images** : Utilisez `docker scan` ou des outils comme Trivy pour détecter les vulnérabilités.
4.  **Utiliser `.dockerignore`** : Excluez les secrets (`.env`, `.git`), les logs et les dépendances locales du contexte de build.

### Docker Compose

Docker Compose est un outil pour définir et lancer des applications multi-conteneurs. Il utilise un fichier `docker-compose.yml` pour configurer les services, réseaux et volumes.

*   **Commandes clés** : `docker-compose up`, `docker-compose down`, `docker-compose build`, `docker-compose logs`.
*   **Avantages** : Simplifie la gestion d'architectures complexes (ex: un service web + une base de données), rend les environnements reproductibles.

## Exemple Complet : API Node.js

Voici un exemple complet d'une API REST Node.js avec Docker, incluant les bonnes pratiques.

---

### **Structure du Projet**

```
mon-api/
├── src/
│   └── app.js
├── .env
├── .dockerignore
├── Dockerfile
├── docker-compose.yml
└── package.json
```

---

### **1. `package.json`**

```json
{
  "name": "mon-api",
  "version": "1.0.0",
  "main": "src/app.js",
  "scripts": {
    "start": "node src/app.js"
  },
  "dependencies": {
    "dotenv": "^16.0.3",
    "express": "^4.18.2"
  }
}
```

### **2. `src/app.js` (API de base)**

```javascript
const express = require('express');
const dotenv = require('dotenv');

dotenv.config(); // Charge les variables d'environnement

const app = express();
const port = process.env.PORT || 3000;

app.get('/', (req, res) => {
  res.send(`API en cours d'exécution (Version: ${process.env.APP_VERSION})`);
});

app.get('/config', (req, res) => {
  res.json({
    apiKey: process.env.API_KEY,
    environment: process.env.NODE_ENV
  });
});

app.listen(port, () => {
  console.log(`Serveur démarré sur le port ${port}`);
});
```

### **3. `.env` (pour le développement local)**

```ini
PORT=3000
API_KEY=ma-super-cle-secrete
NODE_ENV=development
APP_VERSION=1.0.0
```

### **4. `Dockerfile` (Multi-stage Build)**

```dockerfile
# Étape 1 : Build de l'application
FROM node:18-alpine AS builder

WORKDIR /app

# Copie des fichiers de dépendances pour profiter du cache
COPY package*.json ./

# Installation des dépendances de production
RUN npm ci --only=production

# Copie du code source
COPY . .

# Étape 2 : Image finale légère
FROM node:18-alpine

# Définir l'environnement de production
ENV NODE_ENV=production

WORKDIR /app

# Créer un utilisateur non-root
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

# Copie des dépendances et du code depuis l'étape de build
COPY --from=builder /app /app

# Exposition du port
EXPOSE 3000

# Commande de démarrage
CMD ["node", "src/app.js"]
```

### **5. `.dockerignore`**

```
node_modules
npm-debug.log
.env
.git
```

### **6. `docker-compose.yml`**

```yaml
version: '3.8'

services:
  api:
    build: .
    ports:
      # Port de l'hôte:Port du conteneur
      - "3000:3000"
    # Passer les variables d'environnement au conteneur
    environment:
      - PORT=3000
      - API_KEY=${API_KEY}
      - NODE_ENV=${NODE_ENV}
      - APP_VERSION=${APP_VERSION}
    # Monter le code source pour le développement (hot-reloading)
    volumes:
      - ./src:/app/src
    networks:
      - api-network

networks:
  api-network:
    driver: bridge
```

### **7. Fichier `.env` pour Docker Compose**

Créez un autre fichier `.env` au même niveau que `docker-compose.yml` pour les variables de production.

```ini
# .env (pour docker-compose)
API_KEY=secret123-from-compose
NODE_ENV=production
APP_VERSION=1.0.1
```

### **8. Workflow Complet**

1.  **Construire l'image** : `docker-compose build`
2.  **Démarrer le conteneur** : `docker-compose up -d`
3.  **Tester l'API** :
    *   `curl http://localhost:3000`
        *   Résultat : `"API en cours d'exécution (Version: 1.0.1)"`
    *   `curl http://localhost:3000/config`
        *   Résultat : `{"apiKey":"secret123-from-compose","environment":"production"}`
4.  **Voir les logs** : `docker-compose logs -f`
5.  **Arrêter** : `docker-compose down`


