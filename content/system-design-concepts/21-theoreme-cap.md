---
title: Théorème CAP
tags: system-design theoreme-cap cap-theorem
draft : false
---

# Théorème CAP (CAP Theorem)

**Introduction**
Le théorème CAP est un principe fondamental en conception de systèmes distribués. Il énonce qu'il est impossible pour un système de données distribué de garantir simultanément la Cohérence, la Disponibilité et la Tolérance aux Partitions. En d'autres termes, face à une partition réseau, un compromis entre Cohérence et Disponibilité est inévitable.

**Principes Clés**
- **Cohérence (Consistency):** Chaque lecture reçoit la donnée la plus récente ou une erreur. Cela signifie que toutes les répliques de données sont synchronisées et présentent la même information à tout moment. Exemple : Après une mise à jour, tous les utilisateurs voient immédiatement la nouvelle valeur.
- **Disponibilité (Availability):** Chaque requête reçoit une réponse, sans garantie que ce soit la donnée la plus récente. Le système reste opérationnel même si certains nœuds sont défaillants. Exemple : Un site web reste accessible même si certains serveurs sont hors ligne.
- **Tolérance aux Partitions (Partition Tolerance):** Le système continue de fonctionner malgré les pannes de communication (partitions réseau) entre les nœuds. Les partitions réseau sont inévitables dans un système distribué. Exemple : Deux centres de données peuvent continuer à fonctionner indépendamment même si la connexion entre eux est interrompue.

**Guides d'utilisation**
Étant donné que la tolérance aux partitions est généralement une exigence non négociable dans les systèmes distribués modernes (les pannes réseau se produisent), le théorème CAP implique que vous devez choisir entre la Cohérence et la Disponibilité en cas de partition.
- **Systèmes CP (Cohérence + Tolérance aux Partitions):** Privilégient la cohérence. En cas de partition, le système peut refuser certaines opérations pour garantir que les données restent cohérentes. Exemples :
    - **ZooKeeper:** Utilisé pour la coordination distribuée, il privilégie la cohérence pour éviter des états incohérents dans le cluster.
    - **Consul (en mode cohérent):** Un outil de service discovery et de configuration qui peut être configuré pour privilégier la cohérence.
    - **Bases de données relationnelles distribuées (ex: Spanner):** Dans certaines configurations, elles peuvent garantir la cohérence au détriment de la disponibilité.
- **Systèmes AP (Disponibilité + Tolérance aux Partitions):** Privilégient la disponibilité. En cas de partition, le système accepte les requêtes, mais peut renvoyer des données potentiellement obsolètes. La cohérence est atteinte éventuellement (Eventual Consistency). Exemples :
    - **Cassandra:** Une base de données NoSQL conçue pour la haute disponibilité et la scalabilité.
    - **DynamoDB:** Un service de base de données NoSQL offert par AWS, conçu pour la disponibilité et la scalabilité.
    - **Sytèmes DNS:** Les serveurs DNS privilégient la disponibilité pour assurer la résolution de noms de domaine, même en cas de problèmes réseau.

**Implications Pratiques du Théorème CAP**
- **Pas de système parfait:** Il n'existe pas de système distribué qui puisse garantir les trois propriétés simultanément. Le choix dépend des priorités de l'application.
- **Les partitions sont inévitables:** Dans un environnement distribué, les pannes réseau et les retards sont une réalité. Il est donc impératif de concevoir des systèmes qui tolèrent les partitions.
- **Compromis:** Le théorème CAP force un compromis entre la cohérence et la disponibilité en cas de partition.
    - **Pour les applications bancaires ou financières:** La cohérence est souvent primordiale (systèmes CP).
    - **Pour les réseaux sociaux ou les systèmes de recommandation:** La disponibilité est souvent plus importante, et une cohérence éventuelle est acceptable (systèmes AP).
- **Cohérence Éventuelle:** Dans les systèmes AP, les données finiront par être cohérentes une fois que la partition est résolue. Le défi est de gérer cette période d'incohérence.
- **Microservices et Services Distribués:** Le théorème CAP est particulièrement pertinent dans les architectures de microservices où de nombreux services communiquent entre eux et gèrent leurs propres données.

**Gestion des Conflits dans les Systèmes AP**
Dans les systèmes AP, où la cohérence est éventuelle, des conflits peuvent survenir lorsque des mises à jour concurrentes sont effectuées sur différentes partitions. Voici quelques stratégies pour gérer ces conflits :

- **Last Write Wins (LWW):** La dernière écriture est considérée comme la version la plus récente et écrase les écritures précédentes. Cette stratégie est simple, mais peut entraîner la perte de données si l'horloge n'est pas parfaitement synchronisée.
- **Vector Clocks:** Chaque réplique maintient un vecteur d'horloges, qui permet de détecter les conflits et de déterminer l'ordre des événements.
- **CRDTs (Conflict-free Replicated Data Types):** Les CRDTs sont des structures de données qui garantissent que les mises à jour concurrentes convergent vers un état cohérent, sans nécessiter de coordination.
- **Réconciliation au niveau de l'application:** La logique de l'application peut être utilisée pour résoudre les conflits en fonction des besoins spécifiques du domaine.

**Exemples de Code (Hono et CAP - Conceptuel)**
Le théorème CAP est un concept d'architecture système et ne se traduit pas directement par du code Hono spécifique. Cependant, la conception de votre application Hono et la manière dont elle interagit avec les bases de données ou d'autres services distribués doit tenir compte des compromis CAP faits par ces services.

Par exemple, si votre application Hono utilise une base de données AP, vous devez être conscient que les lectures peuvent renvoyer des données obsolètes pendant une courte période après une écriture, et votre logique applicative doit pouvoir gérer cela.

```typescript
import { Hono } from 'hono';
import { json } from 'hono/json';
// Importation conceptuelle d'un client de base de données AP (par exemple, DynamoDB)
// import apDb from './apDb';

const app = new Hono();

app.get('/user-profile/:userId', async (c) => {
  const userId = c.req.param('userId');
  try {
    // Lecture depuis une base de données AP.
    // Les données peuvent être éventuellement cohérentes.
    // const userProfile = await apDb.profiles.get(userId);

    // Simulation de données de profil potentiellement obsolètes
    const userProfile = { userId: userId, status: 'actif', last_update: new Date().toISOString() }; // Simulation

    // Dans une application réelle, vous pourriez afficher un avertissement
    // si last_update est trop ancien, ou implémenter une logique pour gérer la cohérence éventuelle.

    return c.json(userProfile);
  } catch (error) {
    console.error('Erreur DB AP:', error);
    // En cas de partition, la DB AP pourrait toujours répondre (Disponibilité)
    return c.json({ message: 'Service de profil disponible (données potentiellement non à jour)' });
  }
});

export default app;
```

*Note : La gestion de la cohérence éventuelle dans le code applicatif peut impliquer des techniques comme la lecture de vos propres écritures (read-your-writes consistency) ou l'utilisation de versions de données.*

