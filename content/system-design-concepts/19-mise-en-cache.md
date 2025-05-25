---
title: Mise en Cache
tags: system-design mise-en-cache caching
draft : false
---

# Mise En Cache (Caching)

**Présentation**
La mise en cache est une technique qui consiste à stocker des copies de données fréquemment consultées dans un emplacement de stockage temporaire et rapide (le cache), généralement la mémoire vive (RAM). L'objectif est de réduire le temps d'accès aux données en évitant de les récupérer à chaque fois depuis une source plus lente, comme une base de données ou un disque.

**Principes Clés**
- Stockage temporaire de données pour un accès rapide.
- Réduit la latence et améliore le débit.
- Diminue la charge sur la source de données principale.
- Nécessite une stratégie pour gérer la cohérence entre le cache et la source de données (invalidation, TTL).

**Niveaux de Cache**
La mise en cache peut être implémentée à différents niveaux d'une architecture système :
- **Cache Navigateur (Client-side Cache):** Le navigateur web stocke des copies de ressources (images, CSS, JS) pour les réutiliser lors de visites ultérieures. Contrôlé par les en-têtes HTTP (Cache-Control, ETag, Last-Modified).
- **Cache DNS:** Les serveurs DNS et les systèmes d'exploitation mettent en cache les résolutions de noms de domaine pour accélérer les requêtes.
- **CDN (Content Delivery Network):** Réseau de serveurs distribués géographiquement qui stockent des copies de contenu statique (et parfois dynamique) pour le livrer rapidement aux utilisateurs en fonction de leur proximité.
- **Cache Applicatif (Application-level Cache):**
    - **En mémoire (In-memory):** Cache directement dans la mémoire de l'application (rapide mais non partagé entre les instances).
    - **Distribué:** Un service de cache séparé et partagé entre plusieurs instances de l'application (ex: Redis, Memcached). Idéal pour la mise à l'échelle horizontale.
- **Cache de Base de Données:** Le SGBD lui-même peut avoir des mécanismes de cache pour les requêtes ou les données fréquemment accédées.

**Composants Principaux**
- **Cache:** L'emplacement de stockage rapide (par exemple, Redis, Memcached, cache en mémoire de l'application).
- **Source de Données:** L'emplacement d'origine des données (par exemple, base de données, service externe).
- **Stratégie de Cache:** Les règles qui déterminent comment les données sont stockées, récupérées et invalidées dans le cache (par exemple, Cache-Aside, Read-Through, Write-Through, Write-Back).
- **TTL (Time-to-Live):** Une durée après laquelle les données dans le cache sont considérées comme périmées et doivent être rafraîchies.

**Guides d'utilisation**
La mise en cache est efficace pour les données qui sont lues beaucoup plus fréquemment qu'elles ne sont écrites. Elle peut être implémentée à différents niveaux d'une architecture système : côté client (navigateur), côté DNS, côté CDN, côté application (en mémoire ou distribué), et côté base de données. Le choix de la stratégie de cache et de l'outil de cache dépend des exigences de cohérence, de performance et de scalabilité.

**Stratégies d'Invalidation de Cache**
L'invalidation du cache est cruciale pour garantir que les utilisateurs voient les données les plus récentes.
- **Time-to-Live (TTL):** Les données sont automatiquement supprimées du cache après une période définie. Simple à implémenter, mais peut entraîner des données périmées temporairement.
- **Write-Through:** Les données sont écrites simultanément dans le cache et dans la base de données. Assure la cohérence mais peut augmenter la latence d'écriture.
- **Write-Back:** Les données sont d'abord écrites dans le cache, puis écrites de manière asynchrone dans la base de données. Offre de meilleures performances d'écriture mais un risque de perte de données en cas de panne du cache.
- **Cache-Aside (Lazy Loading):** L'application vérifie d'abord le cache. Si les données ne sont pas trouvées (cache miss), elles sont récupérées de la base de données et stockées dans le cache. Les écritures vont directement à la base de données, et le cache est invalidé ou mis à jour explicitement.
- **Invalidation Basée sur les Événements:** Le cache est invalidé (ou mis à jour) lorsqu'un événement spécifique se produit dans la source de données (ex: une mise à jour de base de données déclenche une invalidation du cache).
- **Cache Stale-While-Revalidate:** Sert les données périmées du cache tout en rafraîchissant les données en arrière-plan. Améliore la réactivité perçue.

**Exemples de Code (Hono avec Cache - Conceptuel)**
Une application Hono peut interagir avec un système de cache distribué (comme Redis) pour stocker et récupérer des données fréquemment utilisées.

Voici un exemple conceptuel montrant comment une application Hono pourrait utiliser une stratégie Cache-Aside avec Redis :

```typescript
import { Hono } from 'hono';
import { json } from 'hono/json';
// Importation conceptuelle d'un client Redis
// import redisClient from './redisClient';
// Importation conceptuelle d'un client de base de données
// import db from './db';

const app = new Hono();

app.get('/products/:productId', async (c) => {
  const productId = c.req.param('productId');
  const cacheKey = `product:${productId}`;

  try {
    // 1. Vérifier le cache
    // let product = await redisClient.get(cacheKey);

    let product = null; // Simulation: produit non trouvé dans le cache

    if (product) {
      console.log('Produit trouvé dans le cache');
      return c.json(JSON.parse(product)); // Désérialiser si stocké en JSON
    }

    // 2. Si non trouvé dans le cache, lire depuis la base de données
    console.log('Produit non trouvé dans le cache, lecture depuis la DB');
    // product = await db.products.findById(productId);

    // Simulation: produit trouvé dans la DB
    product = { id: productId, name: `Produit ${productId}`, price: 100 };

    if (product) {
      // 3. Stocker dans le cache pour les futures requêtes (avec TTL)
      // await redisClient.set(cacheKey, JSON.stringify(product), 'EX', 3600); // Cache pendant 1 heure
      console.log('Produit stocké dans le cache');
      return c.json(product);
    }

    return c.json({ message: 'Produit non trouvé' }, 404);

  } catch (error) {
    console.error('Erreur de cache ou DB:', error);
    return c.json({ message: 'Erreur serveur' }, 500);
  }
});

export default app;
```

*Note : L'implémentation réelle nécessiterait un client Redis configuré et une logique de sérialisation/désérialisation si vous stockez des objets complexes.*

**Diagramme Mermaid**

```mermaid
sequenceDiagram
    participant Client
    participant Application
    participant Cache
    participant BaseDeDonnees

    Client->>Application: Requête de données
    activate Application
    Application->>Cache: 1. Vérifier si les données sont en cache

    alt Cache Hit
        Cache-->>Application: Données trouvées dans le cache
        Application-->>Client: Réponse (rapide depuis le cache)
    else Cache Miss
        Cache-->>Application: Données non trouvées
        Application->>BaseDeDonnees: 2. Lire les données depuis la base de données
        activate BaseDeDonnees
        BaseDeDonnees-->>Application: Données lues
        deactivate BaseDeDonnees
        Application->>Cache: 3. Stocker les données dans le cache (avec TTL)
        Cache-->>Application: Confirmation de stockage
        Application-->>Client: Réponse (depuis la base de données)
    end
    deactivate Application

```
