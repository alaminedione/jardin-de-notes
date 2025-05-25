# Sql - Gestion De Bibliotheque

Parfait ! Voici un autre exercice complet de SQL (SQLite), cette fois-ci sur une **gestion de bibliothèque**.

**1. Modèle Logique des Données (MLD) en ER Diagram**

```mermaid
erDiagram
    LIVRE ||--o{ EMPRUNT : est_emprunte
    ADHERENT ||--o{ EMPRUNT : emprunte
    AUTEUR ||--o{ LIVRE : a_ecrit
    GENRE ||--o{ LIVRE : est_de_genre

    EMPRUNT {
        int id_emprunt PK
        int id_livre FK
        int id_adherent FK
        date date_emprunt
        date date_retour_prevue
        date date_retour_effective
    }

    LIVRE {
        int id_livre PK
        string titre
        string isbn
        int id_auteur FK
        int id_genre FK
        int nombre_exemplaires
    }

    ADHERENT {
        int id_adherent PK
        string nom
        string prenom
        string adresse
        string telephone
        date date_inscription
    }

    AUTEUR {
        int id_auteur PK
        string nom_auteur
        string prenom_auteur
    }

    GENRE {
        int id_genre PK
        string nom_genre
    }

```

**2. Création de la Base de Données et des Tables (À faire par vous)**

Créez une nouvelle base de données nommée `bibliotheque.db` et exécutez les instructions SQL pour créer les tables basées sur le diagramme ER ci-dessus. N'oubliez pas les clés primaires et étrangères.

**3. Insertion de Données (Instructions et Données)**

Insérez les données suivantes dans les tables que vous avez créées.

**Table `ADHERENT`:**

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|**id_adherent**|**nom**|**prenom**|**adresse**|**telephone**|**date_inscription**|
|1|Traoré|Fatou|Cité Keur Gorgui, Thiès|77 123 45 67|2024-01-15|
|2|Diop|Aliou|Quartier Grand Standing, Mbour|76 987 65 43|2024-03-20|
|3|Sow|Aminata|Centre Ville, Saly Portudal|78 555 11 22|2024-05-10|
|4|Mbaye|Moussa|Sicap Liberté, Dakar|70 777 88 99|2024-07-01|

**Table `AUTEUR`:**

|   |   |   |
|---|---|---|
|**id_auteur**|**nom_auteur**|**prenom_auteur**|
|1|Sembène|Ousmane|
|2|Kane|Cheikh Hamidou|
|3|Ba|Mariama|
|4|Soyinka|Wole|

**Table `GENRE`:**

|   |   |
|---|---|
|**id_genre**|**nom_genre**|
|1|Roman|
|2|Essai|
|3|Théâtre|
|4|Poésie|

**Table `LIVRE`:**

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|**id_livre**|**titre**|**isbn**|**id_auteur**|**id_genre**|**nombre_exemplaires**|
|1|Les Bouts de Bois de Dieu|9782708701506|1|1|3|
|2|L'Aventure Ambiguë|9782253033682|2|1|2|
|3|Une si longue lettre|9782842140047|3|1|4|
|4|La Tragédie du Roi Christophe|9782080703414|1|3|2|
|5|Le Soleil des Indépendances|9782253014933|4|1|3|
|6|Anthologie de la nouvelle poésie nègre et malgache de langue française|9782708700301|4|4|1|

**Table `EMPRUNT`:**

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|**id_emprunt**|**id_livre**|**id_adherent**|**date_emprunt**|**date_retour_prevue**|**date_retour_effective**|
|1|1|1|2025-03-10|2025-03-31|2025-03-28|
|2|2|2|2025-03-15|2025-04-05|NULL|
|3|3|1|2025-03-25|2025-04-15|2025-04-12|
|4|1|3|2025-04-01|2025-04-22|NULL|
|5|4|2|2025-04-05|2025-04-26|NULL|

<details>
<summary>Requêtes d'insertion de données</summary>

```sql
-- Insertion de données pour la table ADHERENT
INSERT INTO ADHERENT (id_adherent, nom, prenom, adresse, telephone, date_inscription) VALUES
(1, 'Traoré', 'Fatou', 'Cité Keur Gorgui, Thiès', '77 123 45 67', '2024-01-15'),
(2, 'Diop', 'Aliou', 'Quartier Grand Standing, Mbour', '76 987 65 43', '2024-03-20'),
(3, 'Sow', 'Aminata', 'Centre Ville, Saly Portudal', '78 555 11 22', '2024-05-10'),
(4, 'Mbaye', 'Moussa', 'Sicap Liberté, Dakar', '70 777 88 99', '2024-07-01');

-- Insertion de données pour la table AUTEUR
INSERT INTO AUTEUR (id_auteur, nom_auteur, prenom_auteur) VALUES
(1, 'Sembène', 'Ousmane'),
(2, 'Kane', 'Cheikh Hamidou'),
(3, 'Ba', 'Mariama'),
(4, 'Soyinka', 'Wole');

-- Insertion de données pour la table GENRE
INSERT INTO GENRE (id_genre, nom_genre) VALUES
(1, 'Roman'),
(2, 'Essai'),
(3, 'Théâtre'),
(4, 'Poésie');

-- Insertion de données pour la table LIVRE
INSERT INTO LIVRE (id_livre, titre, isbn, id_auteur, id_genre, nombre_exemplaires) VALUES
(1, 'Les Bouts de Bois de Dieu', '9782708701506', 1, 1, 3),
(2, 'L''Aventure Ambiguë', '9782253033682', 2, 1, 2),
(3, 'Une si longue lettre', '9782842140047', 3, 1, 4),
(4, 'La Tragédie du Roi Christophe', '9782080703414', 1, 3, 2),
(5, 'Le Soleil des Indépendances', '9782253014933', 4, 1, 3),
(6, 'Anthologie de la nouvelle poésie nègre et malgache de langue française', '9782708700301', 4, 4, 1);

-- Insertion de données pour la table EMPRUNT
INSERT INTO EMPRUNT (id_emprunt, id_livre, id_adherent, date_emprunt, date_retour_prevue, date_retour_effective) VALUES
(1, 1, 1, '2025-03-10', '2025-03-31', '2025-03-28'),
(2, 2, 2, '2025-03-15', '2025-04-05', NULL),
(3, 3, 1, '2025-03-25', '2025-04-15', '2025-04-12'),
(4, 1, 3, '2025-04-01', '2025-04-22', NULL),
(5, 4, 2, '2025-04-05', '2025-04-26', NULL);
```

</details>

**4. Requêtes SQL (Beaucoup !) (À faire par vous)**

Voici une autre longue liste de requêtes SQL pour votre base de données de bibliothèque.

**Requêtes Simples:**

1. Affichez tous les adhérents.
2. Affichez les noms et prénoms des auteurs.
3. Affichez tous les livres.
4. Affichez les titres et ISBN des livres.
5. Affichez les livres du genre 'Roman'.
6. Affichez les adhérents inscrits après le 1er mars 2024.
7. Affichez les emprunts en cours (date_retour_effective est NULL).
8. Affichez les livres dont le nombre d'exemplaires est inférieur à 3.
9. Affichez les genres de livres disponibles.
10. Affichez les livres écrits par l'auteur dont l'id est 1.

**Requêtes avec JOIN:**

11. Affichez les titres des livres et les noms de leurs auteurs.
12. Affichez les noms des adhérents et les titres des livres qu'ils ont empruntés.
13. Affichez les titres des livres et les noms de leurs genres.
14. Affichez les détails des emprunts (id_emprunt, titre du livre, nom de l'adhérent).
15. Affichez les noms des auteurs et le nombre de livres qu'ils ont écrits.
16. Affichez les noms des genres et le nombre de livres dans chaque genre.
17. Affichez les adhérents qui ont des emprunts en cours.
18. Affichez les livres qui n'ont jamais été empruntés (en utilisant LEFT JOIN).
19. Affichez les emprunts avec le nom de l'adhérent et le titre du livre, en incluant les dates d'emprunt et de retour prévue.
20. Affichez les livres avec leur titre et le nom de l'auteur, triés par titre.

**Requêtes avec fonctions d'agrégation et GROUP BY, HAVING:**

21. Comptez le nombre total d'adhérents.
22. Comptez le nombre total de livres.
23. Comptez le nombre d'emprunts effectués.
24. Comptez le nombre de livres par auteur.
25. Comptez le nombre de livres par genre.
26. Trouvez le genre avec le plus de livres.
27. Trouvez l'auteur avec le plus de livres.
28. Comptez le nombre d'emprunts par adhérent.
29. Affichez les adhérents qui ont emprunté plus d'un livre.
30. Trouvez le livre le plus emprunté (nécessite une sous-requête ou une jointure et un regroupement).
31. Calculez le nombre moyen d'exemplaires par livre.
32. Trouvez le livre avec le moins d'exemplaires.

**Requêtes avec LEFT JOIN, RIGHT JOIN, FULL OUTER JOIN (si supporté):**

33. Affichez tous les livres et leurs emprunts correspondants (même les livres non empruntés).
34. Affichez tous les adhérents et les livres qu'ils ont empruntés (même les adhérents sans emprunt).
35. Affichez tous les auteurs et leurs livres (même les auteurs sans livre).
36. Affichez tous les genres et les livres associés (même les genres sans livre).

**Requêtes avec sous-requêtes:**

37. Affichez les livres du même genre que 'Les Bouts de Bois de Dieu'.
38. Affichez les adhérents qui ont emprunté au moins un livre écrit par 'Sembène Ousmane'.
39. Affichez les livres qui n'ont jamais été empruntés.
40. Affichez les adhérents qui ont emprunté le livre avec le plus grand nombre d'exemplaires.
41. Affichez les livres dont le nombre d'exemplaires est supérieur à la moyenne du nombre d'exemplaires de tous les livres.

**Requêtes de modification de données:**

42. Ajoutez un nouvel exemplaire du livre 'Les Bouts de Bois de Dieu'.
43. Mettez à jour la date de retour effective de l'emprunt dont l'id est 2 à la date d'aujourd'hui.
44. Modifiez le genre du livre 'Anthologie de la nouvelle poésie nègre et malgache de langue française' à 'Poésie et Essai' (nécessite potentiellement une nouvelle table de relation si un livre peut avoir plusieurs genres, sinon choisissez l'un des deux).
45. Supprimez l'adhérent dont l'id est 4 (attention aux contraintes).
46. Supprimez tous les emprunts qui ont une date de retour effective antérieure au 1er janvier 2025.

**Requêtes avancées:**

47. Créez une vue qui affiche le titre du livre et le nom de l'adhérent pour tous les emprunts en cours.
48. Sélectionnez toutes les informations de cette vue.
49. Trouvez les livres qui ont été empruntés plus de deux fois.
50. Identifiez les adhérents qui ont emprunté des livres de tous les genres disponibles (requête plus complexe).
