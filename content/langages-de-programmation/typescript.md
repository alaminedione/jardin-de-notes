---
title: TypeScript
draft: false
tags:
  - typescript
  - programming
description: Sur-ensemble typé de JavaScript qui compile en JavaScript simple.
---


## **Note de Référence Complète sur TypeScript**

**Table des Matières**
1.  [Introduction à TypeScript](#1-introduction-à-typescript)
2.  [Syntaxe de Base et Structure d'un Programme](#2-syntaxe-de-base-et-structure-dun-programme)
3.  [Le Système de Types : Primitifs, Composés et Annotations](#3-le-système-de-types--primitifs-composés-et-annotations)
4.  [Interfaces, Types Personnalisés, Unions et Intersections](#4-interfaces-types-personnalisés-unions-et-intersections)
5.  [Classes et Programmation Orientée Objet (POO)](#5-classes-et-programmation-orientée-objet-poo)
6.  [Fonctions, Paramètres, Génériques et Types Utilitaires](#6-fonctions-paramètres-génériques-et-types-utilitaires)
7.  [Gestion des Modules : Import et Export](#7-gestion-des-modules--import-et-export)
8.  [Compatibilité et Intégration avec JavaScript](#8-compatibilité-et-intégration-avec-javascript)
9.  [Configuration Stricte et Options du Compilateur](#9-configuration-stricte-et-options-du-compilateur)
10. [Concepts Avancés : Décorateurs et Métaprogrammation](#10-concepts-avancés--décorateurs-et-métaprogrammation)
11. [Meilleures Pratiques et Conventions de Codage](#11-meilleures-pratiques-et-conventions-de-codage)

---

## **1. Introduction à TypeScript**

TypeScript est un **sur-ensemble (superset) de JavaScript**, développé et maintenu par Microsoft. Il ajoute un **typage statique optionnel** et d'autres fonctionnalités orientées objet au langage JavaScript. Le principal avantage de TypeScript est de permettre la détection d'erreurs de type pendant la phase de développement (compilation) plutôt qu'à l'exécution, ce qui rend le code plus robuste, prévisible et facile à maintenir, en particulier dans les projets de grande envergure.

Le code TypeScript est **transpilé** en code JavaScript standard, qui peut ensuite être exécuté par n'importe quel navigateur ou environnement JavaScript (comme Node.js).

---

## **2. Syntaxe de Base et Structure d'un Programme**

Tout code JavaScript valide est également du code TypeScript valide. La principale différence syntaxique est l'ajout **d'annotations de type**.

### **Annotation de Type**
On utilise les deux-points (`:`) suivis du nom du type pour annoter une variable, un paramètre de fonction ou une valeur de retour.

```typescript
// Déclaration d'une variable avec un type
let message: string = "Bonjour, TypeScript !";
let annee: number = 2023;
let estActif: boolean = true;

// Structure d'une fonction simple
function saluer(nom: string): void {
  console.log(`Bonjour, ${nom} !`);
}

saluer(message);
```

### **Compilation**
Le code TypeScript (fichier `.ts`) doit être compilé en JavaScript (fichier `.js`) à l'aide du compilateur TypeScript (`tsc`).
1.  Installation : `npm install -g typescript`
2.  Compilation : `tsc mon_fichier.ts` (génère `mon_fichier.js`)

---

## **3. Le Système de Types : Primitifs, Composés et Annotations**

TypeScript enrichit le système de types de JavaScript.

### **Types Primitifs**
-   `string` : Chaînes de caractères (`"hello"`, `'world'`, `` `template` ``).
-   `number` : Nombres (entiers et flottants : `10`, `3.14`).
-   `boolean` : Booléens (`true`, `false`).
-   `null` : Représente une absence intentionnelle de valeur.
-   `undefined` : Représente une variable qui n'a pas encore été assignée.
-   `symbol` : Identifiant unique et immuable (introduit en ES6).
-   `bigint` : Pour les très grands entiers.

### **Types Composés**
-   **Tableaux (`Array`)** : Il existe deux syntaxes.
    ```typescript
    let nombres: number[] = [1, 2, 3];
    let noms: Array<string> = ["Alice", "Bob"];
    ```

-   **Tuples** : Un tableau avec une longueur et des types de données fixes pour chaque élément.
    ```typescript
    let utilisateur: [string, number];
    utilisateur = ["Alice", 25]; // OK
    // utilisateur = [25, "Alice"]; // Erreur
    ```

-   **Énumérations (`Enum`)** : Pour créer un ensemble de constantes nommées.
    ```typescript
    enum Couleur { Rouge, Vert, Bleu }
    let c: Couleur = Couleur.Vert; // c vaut 1 par défaut

    enum Statut { Pending = "PENDING", Approved = "APPROVED", Rejected = "REJECTED" }
    let s: Statut = Statut.Approved; // s vaut "APPROVED"
    ```

### **Types Spéciaux**
-   **`any`** : Désactive la vérification de type. À utiliser avec parcimonie, car il annule les avantages de TypeScript.
    ```typescript
    let variableMagique: any = 4;
    variableMagique = "Je suis maintenant une chaîne"; // OK
    ```
-   **`unknown`** : Une alternative plus sûre à `any`. Vous devez effectuer une vérification de type avant d'utiliser une variable de type `unknown`.
    ```typescript
    let valeurInconnue: unknown = "Ceci pourrait être n'importe quoi";
    if (typeof valeurInconnue === "string") {
      console.log(valeurInconnue.toUpperCase()); // OK après vérification
    }
    ```
-   **`void`** : Indique l'absence de valeur de retour pour une fonction.
-   **`never`** : Indique qu'une fonction ne se termine jamais normalement (par exemple, elle lève toujours une exception ou contient une boucle infinie).

---

## **4. Interfaces, Types Personnalisés, Unions et Intersections**

Ces constructions permettent de définir des structures de données complexes et flexibles.

### **Interfaces**
Une interface est un contrat qui définit la forme (les propriétés et méthodes) d'un objet.

```typescript
interface Utilisateur {
  id: number;
  nom: string;
  email?: string; // Propriété optionnelle avec '?'
  readonly dateCreation: Date; // Propriété en lecture seule
  direBonjour(): string;
}

let user: Utilisateur = {
  id: 1,
  nom: "John Doe",
  dateCreation: new Date(),
  direBonjour: () => `Bonjour, je suis ${user.nom}`
};
```

### **Types Personnalisés (`type`)**
L'alias de type permet de donner un nom à n'importe quel type, qu'il soit primitif, objet, union, etc.

```typescript
type ID = string | number;
type Point = {
  x: number;
  y: number;
};

let userId: ID = "abc-123";
let p1: Point = { x: 10, y: 20 };
```
**Interface vs Type** : Les interfaces sont "ouvertes" (on peut les étendre par fusion de déclarations), tandis que les types sont "fermés". Utilisez les interfaces pour définir des formes d'objets ou des contrats de classes, et les types pour des unions, intersections ou des types plus complexes.

### **Types Union (`|`)**
Un type union permet à une variable d'accepter plusieurs types différents.

```typescript
function afficherId(id: string | number) {
  if (typeof id === "string") {
    console.log(id.toUpperCase()); // Traitement spécifique au string
  } else {
    console.log(id); // Traitement spécifique au number
  }
}
```

### **Types Intersection (`&`)**
Un type intersection combine plusieurs types en un seul, qui possède toutes les propriétés des types combinés.

```typescript
interface Personne {
  nom: string;
}
interface Employe {
  codeEmploye: number;
}

type PersonneEmploye = Personne & Employe;

let employe: PersonneEmploye = {
  nom: "Jane Doe",
  codeEmploye: 123
};
```

---

## **5. Classes et Programmation Orientée Objet (POO)**

TypeScript prend en charge la POO avec des classes, de l'héritage, des modificateurs d'accès et des interfaces.

### **Classes et Constructeur**
```typescript
class Animal {
  // Modificateurs d'accès: public (défaut), private, protected
  private nom: string;
  
  constructor(nom: string) {
    this.nom = nom;
  }

  public seDeplacer(distance: number = 0): void {
    console.log(`${this.nom} s'est déplacé de ${distance}m.`);
  }
}
```

### **Héritage (`extends`)**
```typescript
class Chien extends Animal {
  constructor(nom: string) {
    super(nom); // Appelle le constructeur de la classe parente
  }

  aboyer(): void {
    console.log("Wouaf !");
  }
}

const monChien = new Chien("Rex");
monChien.seDeplacer(10);
monChien.aboyer();
```

### **Implémentation d'Interfaces (`implements`)**
Une classe peut implémenter une interface pour garantir qu'elle respecte un contrat spécifique.

```typescript
interface Loggable {
  log(): void;
}

class Produit implements Loggable {
  constructor(public nom: string, public prix: number) {}

  log(): void {
    console.log(`Produit: ${this.nom}, Prix: ${this.prix}€`);
  }
}
```

### **Classes Abstraites**
Les classes abstraites ne peuvent pas être instanciées directement et peuvent contenir des membres abstraits que les classes filles doivent implémenter.

```typescript
abstract class Figure {
  abstract calculerAire(): number;

  afficherAire(): void {
    console.log("L'aire est : " + this.calculerAire());
  }
}

class Cercle extends Figure {
  constructor(public rayon: number) {
    super();
  }

  calculerAire(): number {
    return Math.PI * this.rayon ** 2;
  }
}
```

---

## **6. Fonctions, Paramètres, Génériques et Types Utilitaires**

### **Typage des Fonctions**
```typescript
// Annotation des paramètres et de la valeur de retour
function additionner(a: number, b: number): number {
  return a + b;
}

// Type pour une fonction
let maFonction: (x: number, y: number) => number;
maFonction = additionner;
```

### **Paramètres de Fonctions**
-   **Optionnels (`?`)** : Doivent être les derniers.
-   **Par défaut** : Attribue une valeur si non fournie.
-   **Rest (`...`)** : Regroupe les paramètres restants dans un tableau.

```typescript
function construireNom(prenom: string, nom?: string, initiales: string = "N/A"): string {
  return `${prenom} ${nom || ''} (${initiales})`;
}

function somme(...nombres: number[]): number {
  return nombres.reduce((total, num) => total + num, 0);
}
```

### **Génériques (`Generics`)**
Les génériques permettent de créer des composants (fonctions, classes, interfaces) réutilisables qui peuvent fonctionner avec différents types tout en conservant la sécurité de type.

```typescript
// Fonction générique
function premiereElement<T>(arr: T[]): T | undefined {
  return arr[0];
}

let premierNombre = premiereElement([1, 2, 3]); // Type inféré : number
let premierString = premiereElement(["a", "b", "c"]); // Type inféré : string

// Interface générique
interface Conteneur<T> {
  contenu: T;
}

let boiteDeChaine: Conteneur<string> = { contenu: "texte" };
```

### **Types Utilitaires (Utility Types)**
TypeScript fournit des types utilitaires pour manipuler les types existants.
-   `Partial<T>` : Rend toutes les propriétés de `T` optionnelles.
-   `Readonly<T>` : Rend toutes les propriétés de `T` en lecture seule.
-   `Pick<T, K>` : Crée un type en sélectionnant un ensemble de propriétés `K` de `T`.
-   `Omit<T, K>` : Crée un type en omettant un ensemble de propriétés `K` de `T`.
-   `Record<K, T>` : Crée un type d'objet dont les clés sont de type `K` et les valeurs de type `T`.

```typescript
interface Todo {
  titre: string;
  description: string;
  estFait: boolean;
}

// Rend toutes les propriétés de Todo optionnelles
function updateTodo(todo: Todo, fieldsToUpdate: Partial<Todo>) {
  return { ...todo, ...fieldsToUpdate };
}

// Ne contient que 'titre' et 'estFait'
type TodoPreview = Pick<Todo, "titre" | "estFait">;
```

---

## **7. Gestion des Modules : Import et Export**

TypeScript utilise la syntaxe des modules ES6 pour organiser le code en fichiers réutilisables.

### **Exportation (`export`)**
-   **Exportations Nommées** : Exporter plusieurs éléments d'un module.
    ```typescript
    // Fichier: math.ts
    export const PI = 3.14;
    export function ajouter(a: number, b: number): number {
      return a + b;
    }
    ```
-   **Exportation par Défaut (`export default`)** : Exporter une seule entité principale.
    ```typescript
    // Fichier: MonComposant.ts
    export default class MonComposant {
      // ...
    }
    ```

### **Importation (`import`)**
```typescript
// Fichier: main.ts

// Importations nommées
import { PI, ajouter } from './math';
console.log(ajouter(PI, 2));

// Importation par défaut
import Composant from './MonComposant';
const instance = new Composant();
```

---

## **8. Compatibilité et Intégration avec JavaScript**

TypeScript est conçu pour une intégration transparente avec l'écosystème JavaScript.

-   **Adoption Graduelle** : Vous pouvez renommer un fichier `.js` en `.ts` et commencer à ajouter des types progressivement.
-   **Fichiers de Déclaration (`.d.ts`)** : Pour utiliser des bibliothèques JavaScript existantes qui n'ont pas été écrites en TypeScript, on utilise des fichiers de déclaration de type. Ces fichiers décrivent les types et les signatures des fonctions de la bibliothèque.
-   **DefinitelyTyped** : La communauté maintient un immense référentiel de fichiers de déclaration pour des milliers de bibliothèques JS sous le scope `@types` sur npm.
    ```bash
    # Exemple pour installer les types de Node.js et de React
    npm install --save-dev @types/node @types/react
    ```

---

## **9. Configuration Stricte et Options du Compilateur**

Le comportement du compilateur TypeScript est contrôlé par le fichier `tsconfig.json` à la racine du projet.

### **Fichier `tsconfig.json` de base**
```json
{
  "compilerOptions": {
    "target": "ES2018",         // Version JavaScript cible
    "module": "commonjs",      // Système de modules
    "outDir": "./dist",        // Dossier de sortie pour les fichiers .js
    "rootDir": "./src",        // Dossier source des fichiers .ts
    "strict": true,            // Active toutes les options de vérification stricte
    "esModuleInterop": true    // Améliore l'interopérabilité avec les modules CommonJS
  },
  "include": ["src/**/*"]      // Fichiers à inclure dans la compilation
}
```

### **Gestion Stricte des Erreurs (`strict: true`)**
Activer `strict: true` est une meilleure pratique. Cela active un ensemble d'options qui renforcent la sécurité du type, notamment :
-   `noImplicitAny`: Lève une erreur sur les variables et paramètres avec un type `any` implicite.
-   `strictNullChecks`: Différencie `null` et `undefined` des autres types. Vous devez explicitement vérifier leur présence.
-   `strictFunctionTypes`: Vérifie la covariance des paramètres de fonction.
-   `noImplicitThis`: Lève une erreur sur l'utilisation de `this` avec un type `any` implicite.

---

## **10. Concepts Avancés : Décorateurs et Métaprogrammation**

Les décorateurs sont une fonctionnalité expérimentale qui permet d'annoter et de modifier des classes et leurs membres (méthodes, propriétés). Ils sont largement utilisés dans des frameworks comme Angular et NestJS.

Pour les utiliser, activez l'option `experimentalDecorators` dans `tsconfig.json`.

### **Exemple de Décorateur de Méthode**
```typescript
// Un décorateur est une fonction
function logExecutionTime(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
  const originalMethod = descriptor.value;

  descriptor.value = function(...args: any[]) {
    const start = performance.now();
    const result = originalMethod.apply(this, args);
    const end = performance.now();
    console.log(`La méthode ${propertyKey} a mis ${end - start}ms à s'exécuter.`);
    return result;
  };

  return descriptor;
}

class Calculateur {
  @logExecutionTime
  calculComplexe() {
    // Simule une opération longue
    for (let i = 0; i < 1e7; i++) {}
    console.log("Calcul terminé.");
  }
}

const calc = new Calculateur();
calc.calculComplexe();
// Affiche: "Calcul terminé."
// Affiche: "La méthode calculComplexe a mis Xms à s'exécuter."
```

---

## **11. Meilleures Pratiques et Conventions de Codage**

1.  **Activer le Mode `strict`** : C'est la recommandation la plus importante pour tirer pleinement parti de TypeScript.
2.  **Éviter `any`** : N'utilisez `any` qu'en dernier recours. Préférez `unknown` si le type est réellement inconnu.
3.  **Utiliser l'Inférence de Type** : Laissez TypeScript inférer les types autant que possible pour alléger le code.
    ```typescript
    let nom = "Alice"; // Inutile d'écrire let nom: string
    ```
4.  **Préférer `interface` pour les Formes d'Objets** : Utilisez les interfaces pour définir des contrats pour les objets et les classes.
5.  **Utiliser `type` pour les Unions et Intersections** : Les alias de type sont plus adaptés pour combiner des types.
6.  **Utiliser `ESLint`** : Intégrez `eslint` avec le plugin `@typescript-eslint` pour maintenir une qualité de code cohérente et détecter les problèmes potentiels.
7.  **Organiser le Code en Modules** : Structurez votre application en petits modules cohésifs et réutilisables.
8.  **Tirer parti des `readonly`** : Utilisez `readonly` pour les propriétés qui ne doivent pas être modifiées après leur initialisation, favorisant ainsi l'immutabilité.
