# Pgsql - Reservation De Vols

Absolument ! Puisque vous souhaitez explorer des fonctionnalités avancées spécifiques à PostgreSQL, cet exercice portera sur la **gestion d'un système de réservation de vols**. Nous allons intégrer des triggers, des fonctions en PL/pgSQL et d'autres concepts avancés.

**Attention :** Cet exercice est conçu pour PostgreSQL et certaines fonctionnalités mentionnées (comme PL/pgSQL) ne sont pas directement disponibles dans SQLite. Assurez-vous d'avoir un environnement PostgreSQL configuré pour le réaliser.

**1. Modèle Logique des Données (MLD) en ER Diagram**

```mermaid
erDiagram
    PASSAGER ||--o{ RESERVATION : effectue
    VOL ||--o{ RESERVATION : concerne
    AEROPORT ||--o{ VOL : est_origine
    AEROPORT ||--o{ VOL : est_destination
    COMPAGNIE_AERIENNE ||--o{ VOL : est_opere_par
    RESERVATION {
        INTEGER id_reservation PK
        INTEGER id_passager FK
        INTEGER id_vol FK
        DATE date_reservation
        INTEGER nombre_passagers
        VARCHAR statut
    }
    PASSAGER {
        INTEGER id_passager PK
        VARCHAR nom
        VARCHAR prenom
        VARCHAR email
        VARCHAR telephone
        DATE date_naissance
    }
    VOL {
        INTEGER id_vol PK
        INTEGER id_aeroport_depart FK
        INTEGER id_aeroport_arrivee FK
        INTEGER id_compagnie FK
        TIMESTAMP heure_depart
        TIMESTAMP heure_arrivee
        INTEGER capacite
        NUMERIC prix
    }
    AEROPORT {
        INTEGER id_aeroport PK
        VARCHAR code_iata
        VARCHAR nom_aeroport
        VARCHAR ville
        VARCHAR pays
    }
    COMPAGNIE_AERIENNE {
        INTEGER id_compagnie PK
        VARCHAR nom_compagnie
        VARCHAR code_icao
    }
```

**2. Création de la Base de Données et des Tables (À faire par vous - en PostgreSQL)**

Créez une nouvelle base de données nommée `reservations_vols` dans PostgreSQL et exécutez les instructions SQL pour créer les tables basées sur le diagramme ER ci-dessus. Assurez-vous de définir les clés primaires, les clés étrangères et les contraintes (NOT NULL, UNIQUE, CHECK) correctement. Utilisez `SERIAL` pour les colonnes d'auto-incrémentation.

**3. Insertion de Données (Instructions et Données)**

Insérez les données suivantes dans les tables que vous avez créées.

**Table `COMPAGNIE_AERIENNE`:**

| nom\_compagnie | code\_icao |
|-----------------|------------|
| Air Sénégal     | CSE        |
| Transavia       | TVF        |
| Brussels Airlines | BEL        |

**Table `AEROPORT`:**

| code\_iata | nom\_aeroport                      | ville      | pays        |
|------------|------------------------------------|------------|-------------|
| DSS        | Aéroport International Blaise Diagne | Diass      | Sénégal     |
| CDG        | Aéroport Paris-Charles de Gaulle   | Paris      | France      |
| BRU        | Aéroport de Bruxelles-National     | Bruxelles  | Belgique    |
| AMS        | Aéroport d'Amsterdam-Schiphol      | Amsterdam  | Pays-Bas    |

**Table `PASSAGER`:**

| nom     | prenom    | email                     | telephone    | date\_naissance |
|---------|-----------|---------------------------|--------------|-----------------|
| Diouf   | Khadim    | khadim.diouf@example.com   | 77 777 77 77 | 1985-05-10      |
| Dubois  | Sophie    | sophie.dubois@example.com | 06 06 06 06 06 | 1992-11-22      |
| Janssens| Pieter    | pieter.janssens@example.com | 02 222 22 22 | 1988-03-01      |

**Table `VOL`:**

| id\_aeroport\_depart | id\_aeroport\_arrivee | id\_compagnie | heure\_depart          | heure\_arrivee         | capacite | prix  |
|----------------------|-----------------------|---------------|-----------------------|-----------------------|----------|-------|
| 1                    | 2                     | 1             | 2025-04-15 10:00:00   | 2025-04-15 16:00:00   | 150      | 350.00|
| 2                    | 3                     | 2             | 2025-04-20 08:00:00   | 2025-04-20 09:30:00   | 180      | 120.50|
| 3                    | 4                     | 3             | 2025-05-01 14:00:00   | 2025-05-01 15:15:00   | 100      | 180.75|
| 1                    | 3                     | 1             | 2025-05-10 18:00:00   | 2025-05-11 00:30:00   | 120      | 420.20|

**Table `RESERVATION`:**

| id\_passager | id\_vol | nombre\_passagers |
|--------------|---------|-------------------|
| 1            | 1       | 2                 |
| 2            | 2       | 1                 |
| 3            | 3       | 1                 |

<details>
<summary>Requêtes d'insertion de données</summary>

```sql
-- Insertion de données pour la table COMPAGNIE_AERIENNE
INSERT INTO COMPAGNIE_AERIENNE (id_compagnie, nom_compagnie, code_icao) VALUES
(1, 'Air Sénégal', 'CSE'),
(2, 'Transavia', 'TVF'),
(3, 'Brussels Airlines', 'BEL');

-- Insertion de données pour la table AEROPORT
INSERT INTO AEROPORT (id_aeroport, code_iata, nom_aeroport, ville, pays) VALUES
(1, 'DSS', 'Aéroport International Blaise Diagne', 'Diass', 'Sénégal'),
(2, 'CDG', 'Aéroport Paris-Charles de Gaulle', 'Paris', 'France'),
(3, 'BRU', 'Aéroport de Bruxelles-National', 'Bruxelles', 'Belgique'),
(4, 'AMS', 'Aéroport d''Amsterdam-Schiphol', 'Amsterdam', 'Pays-Bas');

-- Insertion de données pour la table PASSAGER
INSERT INTO PASSAGER (id_passager, nom, prenom, email, telephone, date_naissance) VALUES
(1, 'Diouf', 'Khadim', 'khadim.diouf@example.com', '77 777 77 77', '1985-05-10'),
(2, 'Dubois', 'Sophie', 'sophie.dubois@example.com', '06 06 06 06 06', '1992-11-22'),
(3, 'Janssens', 'Pieter', 'pieter.janssens@example.com', '02 222 22 22', '1988-03-01');

-- Insertion de données pour la table VOL
INSERT INTO VOL (id_vol, id_aeroport_depart, id_aeroport_arrivee, id_compagnie, heure_depart, heure_arrivee, capacite, prix) VALUES
(1, 1, 2, 1, '2025-04-15 10:00:00', '2025-04-15 16:00:00', 150, 350.00),
(2, 2, 3, 2, '2025-04-20 08:00:00', '2025-04-20 09:30:00', 180, 120.50),
(3, 3, 4, 3, '2025-05-01 14:00:00', '2025-05-01 15:15:00', 100, 180.75),
(4, 1, 3, 1, '2025-05-10 18:00:00', '2025-05-11 00:30:00', 120, 420.20);

-- Insertion de données pour la table RESERVATION
INSERT INTO RESERVATION (id_reservation, id_passager, id_vol, date_reservation, nombre_passagers, statut) VALUES
(1, 1, 1, '2025-04-01', 2, 'Confirmée'),
(2, 2, 2, '2025-04-05', 1, 'Confirmée'),
(3, 3, 3, '2025-04-10', 1, 'Confirmée');
```
</details>

**4. Fonctionnalités Avancées (À faire par vous - en PostgreSQL)**

**a) Triggers:**

1. **Trigger pour la mise à jour de la capacité restante d'un vol :**
	* Créez un trigger qui s'exécute `AFTER INSERT` sur la table `RESERVATION`.
	* Ce trigger doit décrémenter la `capacite` de la table `VOL` correspondant à la réservation, en fonction du `nombre_passagers` réservés.
	* Créez un trigger similaire qui s'exécute `AFTER DELETE` sur la table `RESERVATION` pour incrémenter la `capacite` du vol.
	* Créez un trigger `AFTER UPDATE` sur `RESERVATION` qui ajuste la `capacite` du vol en tenant compte de l'ancienne et de la nouvelle valeur de `nombre_passagers`.

2. **Trigger pour empêcher la surréservation :**
	* Créez un trigger qui s'exécute `BEFORE INSERT` sur la table `RESERVATION`.
	* Ce trigger doit vérifier si le nombre de passagers à réserver (`NEW.nombre_passagers`) ne dépasse pas la capacité restante du vol (`VOL.capacite - (somme des nombre_passagers des réservations existantes pour ce vol)`).
	* Si la réservation entraîne une surréservation, le trigger doit empêcher l'insertion (en lançant une exception).

**b) Fonctions en PL/pgSQL:**

3. **Fonction pour obtenir le nombre total de réservations pour un vol donné :**
	* Créez une fonction en PL/pgSQL qui prend en entrée l'`id_vol` et renvoie le nombre total de passagers réservés pour ce vol.

4. **Fonction pour calculer le prix total d'une réservation :**
	* Créez une fonction en PL/pgSQL qui prend en entrée l'`id_reservation` et renvoie le prix total de la réservation (prix du vol * nombre de passagers).

5. **Fonction pour vérifier si un passager a déjà une réservation pour un vol à une date donnée :**
	* Créez une fonction en PL/pgSQL qui prend en entrée l'`id_passager`, l'`id_vol` et une `date_vol` (extraite de `VOL.heure_depart`) et renvoie `TRUE` si le passager a déjà une réservation pour ce vol à cette date, `FALSE` sinon.

**c) Vues:**

6. **Vue affichant les détails des vols avec les noms des aéroports de départ et d'arrivée, et le nom de la compagnie aérienne :**
	* Créez une vue qui joint les tables `VOL`, `AEROPORT` (deux fois avec des alias), et `COMPAGNIE_AERIENNE` pour afficher des informations conviviales sur les vols.

7. **Vue affichant les réservations avec les noms et prénoms des passagers, les détails du vol (aéroports, heures), et le nombre de passagers :**
	* Créez une vue qui joint les tables `RESERVATION`, `PASSAGER`, et la vue créée à l'étape 6 pour fournir une vue complète des réservations.

**d) Index:**

8. Créez des index pertinents pour optimiser les requêtes courantes, par exemple sur les clés étrangères (`id_passager`, `id_vol`), les colonnes de recherche fréquentes (`email` dans `PASSAGER`, `code_iata` dans `AEROPORT`), et les colonnes utilisées dans les clauses `WHERE` des requêtes que vous anticiperez.

**e) Transactions:**

9. Écrivez un bloc de code (en utilisant un client SQL comme `psql`) qui effectue les opérations suivantes dans une seule transaction :
	* Insérer un nouveau passager.
	* Créer une nouvelle réservation pour ce passager sur un vol existant.
	* Mettre à jour le statut de la réservation à 'Confirmée'.
	* Simulez une erreur après l'insertion du passager mais avant la création de la réservation et observez si le `ROLLBACK` fonctionne correctement.

**f) Gestion des erreurs et exceptions dans PL/pgSQL:**

10. Modifiez la fonction de vérification de surréservation (question 2) pour lancer une exception personnalisée avec un message informatif en cas de tentative de surréservation.

**g) Sécurité (Concepts de base):**

11. Créez un nouvel utilisateur (rôle) PostgreSQL avec des droits de lecture seule sur les tables `VOL` et `AEROPORT`.
12. Accordez à cet utilisateur les droits d'exécution sur les fonctions PL/pgSQL que vous avez créées pour obtenir des informations sur les vols.

**5. Requêtes SQL Avancées (Beaucoup \! - en PostgreSQL)**

Maintenant, exécutez une série de requêtes SQL avancées pour interroger votre base de données, en tirant parti des fonctionnalités que vous avez implémentées.

1. Affichez tous les détails des vols (en utilisant la vue créée).
2. Affichez toutes les réservations avec les noms des passagers et les détails des vols (en utilisant la vue créée).
3. Trouvez le nombre total de passagers réservés pour le vol avec `id_vol = 1` (en utilisant la fonction PL/pgSQL).
4. Calculez le prix total de la réservation avec `id_reservation = 1` (en utilisant la fonction PL/pgSQL).
5. Vérifiez si le passager avec `id_passager = 1` a une réservation pour le vol avec `id_vol = 1` le 2025-04-15 (en utilisant la fonction PL/pgSQL).
6. Affichez les vols avec leur capacité restante (calculée à partir des réservations).
7. Trouvez les vols qui sont complets ou presque complets (par exemple, plus de 80% de la capacité réservée).
8. Affichez les passagers qui ont effectué plus d'une réservation.
9. Trouvez les aéroports qui sont à la fois des points de départ et d'arrivée de vols.
10. Affichez les compagnies aériennes et le nombre total de vols qu'elles opèrent.
11. Trouvez les passagers nés avant une certaine date (par exemple, 1990-01-01).
12. Affichez les vols dont la durée est supérieure à 3 heures.
13. Trouvez les réservations effectuées à une date spécifique.
14. Affichez les vols triés par prix croissant.
15. Trouvez le vol le moins cher et le vol le plus cher.
16. Affichez le nombre moyen de passagers par réservation.
17. Trouvez les passagers dont le numéro de téléphone est renseigné.
18. Affichez les vols pour une destination spécifique (par exemple, 'Paris').
19. Trouvez les vols opérés par une compagnie aérienne spécifique (par exemple, 'Air Sénégal').
20. Affichez les réservations avec le statut 'Confirmée'.
21. Tentez d'insérer une réservation qui entraînerait une surréservation (pour tester votre trigger).
22. Mettez à jour le nombre de passagers d'une réservation pour voir si le trigger de mise à jour de la capacité fonctionne.
23. Supprimez une réservation et vérifiez si la capacité du vol correspondant est correctement mise à jour.
24. Utilisez `EXPLAIN` sur certaines de vos requêtes pour voir le plan d'exécution et comment les index sont utilisés (ou non).
25. Écrivez une requête qui utilise une fonction d'agrégation avec une clause `FILTER` pour compter le nombre de réservations avec plus de deux passagers pour un vol donné.
26. Utilisez une expression de table commune (CTE) pour trouver les vols avec le nombre moyen de passagers par réservation le plus élevé.
27. Écrivez une requête qui utilise une fonction de fenêtre pour attribuer un rang aux vols en fonction de leur prix au sein de chaque compagnie aérienne.
28. Trouvez les passagers qui ont des réservations sur des vols opérés par plus d'une compagnie aérienne.
29. Affichez les aéroports qui n'ont aucun vol au départ.
30. Affichez les compagnies aériennes qui n'ont aucun vol à destination d'un aéroport spécifique.
