---
title: Idempotence
tags: system-design idempotence idempotency
draft : false
---

Imaginez un système de paiement où, en raison d'un problème de réseau, une transaction est traitée deux fois. Le client est débité deux fois pour le même achat, ce qui entraîne frustration et mécontentement. C'est là qu'intervient l'idempotence.

L'idempotence est une propriété essentielle des opérations dans les systèmes distribués qui garantit qu'une opération peut être exécutée plusieurs fois sans modifier le résultat au-delà de l'exécution initiale. En d'autres termes, l'exécution répétée d'une opération idempotente a le même effet qu'une seule exécution.

# Idempotence (Idempotency)

**Présentation**
L'idempotence est une propriété d'une opération ou d'une requête qui garantit que l'exécution répétée de cette opération ou requête produit le même résultat que si elle n'avait été exécutée qu'une seule fois. Dans les systèmes distribués, où les requêtes peuvent être retentées en raison de problèmes réseau ou de défaillances de service, l'idempotence est cruciale pour éviter les effets secondaires indésirables (comme débiter un client plusieurs fois pour la même transaction).

**Principes Clés**
- L'exécution multiple d'une opération idempotente a le même effet qu'une seule exécution.
- Important dans les systèmes distribués pour gérer les requêtes retentées.
- Ne signifie pas que la réponse de l'opération sera toujours la même (par exemple, une réponse peut indiquer que la ressource existait déjà lors des appels suivants). L'important est que l'état du système ne change pas après la première exécution réussie. Les exécutions suivantes doivent simplement renvoyer le même résultat que la première, sans provoquer d'effets secondaires supplémentaires.

**Cas d'Utilisation de l'Idempotence**
L'idempotence est particulièrement importante dans les scénarios suivants :
- **Systèmes de Paiement:** Empêcher les doubles débits ou les doubles crédits lors de transactions financières.
- **Files de Messages et Traitement d'Événements:** Assurer que les messages consommés plusieurs fois (en raison de relectures ou de pannes) ne provoquent pas d'effets secondaires indésirables.
- **APIs Publiques:** Permettre aux clients de retenter des requêtes en toute sécurité sans craindre de dupliquer des opérations.
- **Opérations de Création de Ressources:** S'assurer qu'une ressource n'est créée qu'une seule fois, même si la requête est envoyée plusieurs fois.
- **Opérations de Mise à Jour:** Garantir que l'état final de la ressource est le même, quelle que soit le nombre de fois que la mise à jour est appliquée.

**Composants Principaux**
- **Opération/Requête:** L'action exécutée.
- **Identifiant d'Idempotence (Idempotency Key):** Un identifiant unique généré par le client et inclus dans la requête. Cet identifiant permet au serveur de détecter les requêtes dupliquées. Il est crucial que cet identifiant soit unique pour chaque opération afin d'éviter des comportements inattendus.
- **Mécanisme de Détection des Doublons:** Côté serveur, logique pour vérifier si une requête avec le même identifiant d'idempotence a déjà été traitée.
- **Stockage de l'État des Requêtes:** Un endroit où le serveur stocke les identifiants d'idempotence des requêtes traitées pendant une certaine période.

**Guides d'utilisation**
Les opérations qui modifient l'état du système (comme les requêtes POST, PUT, PATCH, DELETE) devraient idéalement être idempotentes, en particulier dans les systèmes où les retries automatiques sont activés. Pour rendre une opération idempotente, le client inclut un identifiant unique avec la requête. Le serveur stocke cet identifiant et, s'il reçoit une requête avec un identifiant déjà vu, il renvoie le résultat de la première exécution réussie sans exécuter à nouveau l'opération.

**Exemples de Code (Hono avec Idempotence - Conceptuel)**
Pour implémenter l'idempotence dans une application Hono, vous devriez extraire un identifiant d'idempotence de la requête (souvent un en-tête comme `Idempotency-Key`), vérifier si cet identifiant a déjà été traité, et stocker l'identifiant et le résultat de l'opération après le premier traitement réussi.

Voici un exemple conceptuel montrant comment une route Hono pourrait gérer l'idempotence pour une requête de création de ressource :

```typescript
import { Hono } from 'hono';
import { json } from 'hono/json';
// Importation conceptuelle d'un client de cache ou de base de données pour stocker les clés d'idempotence
// import idempotencyStore from './idempotencyStore'; // Par exemple, Redis ou une table DB

const app = new Hono();

// Route pour créer une nouvelle ressource de manière idempotente
app.post('/resources', async (c) => {
  const idempotencyKey = c.req.header('Idempotency-Key');
  const requestBody = await c.req.json();

  if (!idempotencyKey) {
    return c.json({ message: 'En-tête Idempotency-Key manquant' }, 400);
  }

  try {
    // 1. Vérifier si la clé d'idempotence a déjà été traitée
    // const cachedResult = await idempotencyStore.getResult(idempotencyKey);

    const cachedResult = null; // Simulation: clé non trouvée

    if (cachedResult) {
      console.log(`Requête idempotente détectée pour la clé: ${idempotencyKey}. Renvoi du résultat mis en cache.`);
      return c.json(cachedResult.response, cachedResult.status);
    }

    // 2. Si non traitée, exécuter l'opération
    console.log(`Traitement de la nouvelle requête pour la clé: ${idempotencyKey}`);
    // const newResource = await createResource(requestBody); // Logique de création de ressource
    // const newResource = await createResource(requestBody); // Logique de création de ressource
    const newResource = { id: Math.random().toString(36).substring(7), ...requestBody }; // Simulation de création - REMPLACEZ PAR VOTRE LOGIQUE METIER

    // 3. Stocker le résultat de l'opération avec la clé d'idempotence
    const responseStatus = 201; // Statut de la réponse de création
    // await idempotencyStore.storeResult(idempotencyKey, newResource, responseStatus); // Stocker le résultat dans le cache/base de données
    console.log(`Résultat stocké pour la clé: ${idempotencyKey}`);

    return c.json(newResource, responseStatus);

  } catch (error) {
    console.error('Erreur lors du traitement de la requête idempotente:', error);
    return c.json({ message: 'Erreur serveur' }, 500);
  }
});

// Fonction conceptuelle pour créer la ressource (remplacez par votre logique métier)
// async function createResource(data) {
//   // Logique pour créer la ressource dans la base de données, etc.
//   return { id: 'new-id', ...data };
// }


export default app;
```

*Note : L'implémentation de `idempotencyStore` nécessiterait un stockage persistant (base de données, cache distribué) et une stratégie d'expiration pour les clés d'idempotence.*

**Diagramme Mermaid**

```mermaid
sequenceDiagram
    participant Client
    participant Application
    participant IdempotencyStore as "Stockage d'Idempotence"
    participant BusinessLogic as "Logique Métier"

    Client->>Application: Requête (avec Idempotency-Key)
    activate Application
    Application->>IdempotencyStore: 1. Vérifier si la clé existe

    alt Clé trouvée (requête dupliquée)
        IdempotencyStore-->>Application: Retourne le résultat mis en cache
        Application-->>Client: Renvoie le résultat mis en cache
    else Clé non trouvée (nouvelle requête)
        IdempotencyStore-->>Application: Clé non trouvée
        Application->>BusinessLogic: 2. Exécuter l'opération
        activate BusinessLogic
        BusinessLogic-->>Application: Résultat de l'opération
        deactivate BusinessLogic
        Application->>IdempotencyStore: 3. Stocker le résultat avec la clé
        IdempotencyStore-->>Application: Confirmation de stockage
        Application-->>Client: Renvoie le résultat de l'opération
    end
    deactivate Application

```
