---
title: JavaScript
draft: false
tags:
  - javascript
  - programming
description: Langage de script principalement utilisé pour le développement web côté client, mais aussi côté serveur avec Node.js.
---

# Guide de Référence Complet : JavaScript Moderne

## Table des matières
1. Introduction et Fondamentaux
2. Types de Données
3. Variables et Portée (Scope)
4. Opérateurs et Expressions
5. Structures de Contrôle
6. Fonctions
7. Programmation Orientée Objet
8. Modules
9. Programmation Asynchrone
10. Collections et Structures de Données
11. Gestion des Erreurs
12. Bonnes Pratiques et Conventions

---

## 1. Introduction et Fondamentaux

### Structure d'un Programme JavaScript

JavaScript est un langage interprété, à typage dynamique, qui peut s'exécuter côté client (navigateur) ou côté serveur (Node.js). Un programme JavaScript est composé d'instructions séparées par des points-virgules (optionnels mais recommandés).

**Exemple de structure de base :**
```javascript
// Commentaire sur une ligne

/* 
   Commentaire 
   sur plusieurs lignes 
*/

// Instructions
console.log('Hello, World!');
const message = 'Bonjour';
```

### Mode Strict

Le mode strict (`'use strict';`) active un mode d'exécution plus rigoureux qui détecte les erreurs courantes.

```javascript
'use strict';

// Interdit l'utilisation de variables non déclarées
x = 10; // ReferenceError en mode strict
```

---

## 2. Types de Données

### Types Primitifs

JavaScript possède 7 types primitifs :

**1. Number** - Nombres entiers et décimaux
```javascript
const entier = 42;
const decimal = 3.14;
const negatif = -10;
const infini = Infinity;
const pasUnNombre = NaN; // Not a Number
```

**2. String** - Chaînes de caractères
```javascript
const simple = 'Texte avec guillemets simples';
const double = "Texte avec guillemets doubles";
const template = `Template literal avec ${simple}`;
const multiligne = `Texte sur
plusieurs lignes`;
```

**3. Boolean** - Valeurs logiques
```javascript
const vrai = true;
const faux = false;
```

**4. Undefined** - Variable déclarée mais non initialisée
```javascript
let variable;
console.log(variable); // undefined
```

**5. Null** - Absence intentionnelle de valeur
```javascript
const vide = null;
```

**6. Symbol** - Identifiant unique
```javascript
const sym1 = Symbol('description');
const sym2 = Symbol('description');
console.log(sym1 === sym2); // false (toujours uniques)
```

**7. BigInt** - Nombres entiers de taille arbitraire
```javascript
const grand = 9007199254740991n;
const tresgrand = BigInt("9007199254740992");
```

### Types Objets

**Object** - Structure de données clé-valeur
```javascript
const personne = {
  nom: 'Dupont',
  prenom: 'Jean',
  age: 30
};
```

**Array** - Liste ordonnée d'éléments
```javascript
const nombres = [1, 2, 3, 4, 5];
const mixte = [1, 'texte', true, null, { key: 'value' }];
```

### Vérification des Types

```javascript
typeof 42;              // "number"
typeof 'texte';         // "string"
typeof true;            // "boolean"
typeof undefined;       // "undefined"
typeof null;            // "object" (bug historique)
typeof {};              // "object"
typeof [];              // "object"
Array.isArray([]);      // true (pour détecter les tableaux)
```

---

## 3. Variables et Portée (Scope)

### Déclaration de Variables

**var** - Portée fonctionnelle (ancienne méthode, à éviter)
```javascript
var x = 10;
// Hoisting : la déclaration est remontée
console.log(y); // undefined (pas d'erreur)
var y = 20;
```

**let** - Portée de bloc, réassignable
```javascript
let compteur = 0;
compteur = 1; // OK

if (true) {
  let local = 'visible seulement ici';
}
// console.log(local); // ReferenceError
```

**const** - Portée de bloc, non réassignable
```javascript
const PI = 3.14159;
// PI = 3; // TypeError

// Attention : les objets/tableaux restent mutables
const personne = { nom: 'Alice' };
personne.nom = 'Bob'; // OK
personne.age = 25;    // OK
// personne = {};     // TypeError
```

### Portée (Scope)

**Portée globale**
```javascript
const global = 'accessible partout';

function test() {
  console.log(global); // OK
}
```

**Portée de fonction**
```javascript
function exemple() {
  const locale = 'seulement dans la fonction';
  console.log(locale); // OK
}
// console.log(locale); // ReferenceError
```

**Portée de bloc**
```javascript
{
  const bloc = 'seulement dans ce bloc';
  let autre = 'aussi limitée au bloc';
}
// console.log(bloc); // ReferenceError
```

**Chaîne de portée (Scope Chain)**
```javascript
const niveau1 = 'global';

function externe() {
  const niveau2 = 'externe';
  
  function interne() {
    const niveau3 = 'interne';
    console.log(niveau1, niveau2, niveau3); // Accès à tous
  }
  
  interne();
}
```

---

## 4. Opérateurs et Expressions

### Opérateurs Arithmétiques

```javascript
const a = 10, b = 3;

a + b;    // 13 (addition)
a - b;    // 7  (soustraction)
a * b;    // 30 (multiplication)
a / b;    // 3.333... (division)
a % b;    // 1  (modulo - reste)
a ** b;   // 1000 (exponentiation)

// Incrémentation/Décrémentation
let x = 5;
x++;      // x = 6 (post-incrémentation)
++x;      // x = 7 (pré-incrémentation)
x--;      // x = 6 (post-décrémentation)
--x;      // x = 5 (pré-décrémentation)
```

### Opérateurs de Comparaison

```javascript
5 == '5';    // true  (égalité avec conversion)
5 === '5';   // false (égalité stricte, types différents)
5 != '5';    // false
5 !== '5';   // true

10 > 5;      // true
10 >= 10;    // true
5 < 10;      // true
5 <= 5;      // true
```

### Opérateurs Logiques

```javascript
true && false;   // false (ET logique)
true || false;   // true  (OU logique)
!true;           // false (NON logique)

// Court-circuit
const result = null || 'défaut';  // 'défaut'
const value = true && 'valeur';   // 'valeur'
```

### Opérateur de Coalescence Nulle (??)

```javascript
const valeur1 = null ?? 'défaut';      // 'défaut'
const valeur2 = undefined ?? 'défaut'; // 'défaut'
const valeur3 = 0 ?? 'défaut';         // 0 (0 n'est pas null/undefined)
const valeur4 = '' ?? 'défaut';        // '' (chaîne vide valide)
```

### Opérateur Ternaire

```javascript
const age = 20;
const statut = age >= 18 ? 'majeur' : 'mineur';
```

### Opérateurs d'Affectation

```javascript
let x = 10;
x += 5;   // x = x + 5  → 15
x -= 3;   // x = x - 3  → 12
x *= 2;   // x = x * 2  → 24
x /= 4;   // x = x / 4  → 6
x %= 4;   // x = x % 4  → 2
x **= 3;  // x = x ** 3 → 8
```

### Opérateurs de Déstructuration et Spread

```javascript
// Spread dans les tableaux
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5]; // [1, 2, 3, 4, 5]

// Spread dans les objets
const obj1 = { a: 1, b: 2 };
const obj2 = { ...obj1, c: 3 }; // { a: 1, b: 2, c: 3 }

// Rest dans les paramètres
function somme(...nombres) {
  return nombres.reduce((acc, n) => acc + n, 0);
}
somme(1, 2, 3, 4); // 10
```

### Chaînage Optionnel (?.)

```javascript
const utilisateur = {
  nom: 'Alice',
  adresse: {
    ville: 'Paris'
  }
};

console.log(utilisateur?.adresse?.ville); // 'Paris'
console.log(utilisateur?.contact?.email); // undefined (pas d'erreur)
console.log(utilisateur.methode?.()); // Appel sécurisé de méthode
```

---

## 5. Structures de Contrôle

### Conditions

**if / else if / else**
```javascript
const temperature = 25;

if (temperature > 30) {
  console.log('Il fait très chaud');
} else if (temperature > 20) {
  console.log('Il fait bon');
} else if (temperature > 10) {
  console.log('Il fait frais');
} else {
  console.log('Il fait froid');
}
```

**switch**
```javascript
const jour = 'lundi';

switch (jour) {
  case 'lundi':
  case 'mardi':
  case 'mercredi':
  case 'jeudi':
  case 'vendredi':
    console.log('Jour de semaine');
    break;
  case 'samedi':
  case 'dimanche':
    console.log('Week-end');
    break;
  default:
    console.log('Jour invalide');
}
```

### Boucles

**for**
```javascript
for (let i = 0; i < 5; i++) {
  console.log(i); // 0, 1, 2, 3, 4
}
```

**for...of** - Itère sur les valeurs
```javascript
const fruits = ['pomme', 'banane', 'orange'];

for (const fruit of fruits) {
  console.log(fruit);
}
```

**for...in** - Itère sur les clés (propriétés)
```javascript
const personne = { nom: 'Alice', age: 30 };

for (const cle in personne) {
  console.log(`${cle}: ${personne[cle]}`);
}
```

**while**
```javascript
let compteur = 0;

while (compteur < 5) {
  console.log(compteur);
  compteur++;
}
```

**do...while**
```javascript
let i = 0;

do {
  console.log(i);
  i++;
} while (i < 5);
```

### Contrôle de Flux

**break** - Sort de la boucle
```javascript
for (let i = 0; i < 10; i++) {
  if (i === 5) break;
  console.log(i); // 0, 1, 2, 3, 4
}
```

**continue** - Passe à l'itération suivante
```javascript
for (let i = 0; i < 5; i++) {
  if (i === 2) continue;
  console.log(i); // 0, 1, 3, 4 (saute 2)
}
```

---

## 6. Fonctions

### Déclaration de Fonction

```javascript
function addition(a, b) {
  return a + b;
}

const resultat = addition(5, 3); // 8
```

### Expression de Fonction

```javascript
const multiplication = function(a, b) {
  return a * b;
};

multiplication(4, 5); // 20
```

### Fonctions Fléchées (Arrow Functions)

```javascript
// Syntaxe complète
const division = (a, b) => {
  return a / b;
};

// Syntaxe courte (return implicite)
const carre = x => x * x;

// Sans paramètre
const saluer = () => console.log('Bonjour');

// Un seul paramètre (parenthèses optionnelles)
const double = n => n * 2;

// Retour d'objet (parenthèses nécessaires)
const creerPersonne = (nom, age) => ({ nom, age });
```

### Paramètres

**Paramètres par défaut**
```javascript
function salutation(nom = 'Visiteur', message = 'Bonjour') {
  return `${message}, ${nom}!`;
}

salutation(); // "Bonjour, Visiteur!"
salutation('Alice'); // "Bonjour, Alice!"
salutation('Bob', 'Salut'); // "Salut, Bob!"
```

**Paramètres rest**
```javascript
function somme(...nombres) {
  return nombres.reduce((acc, n) => acc + n, 0);
}

somme(1, 2, 3, 4, 5); // 15
```

**Déstructuration des paramètres**
```javascript
function afficherPersonne({ nom, age, ville = 'Paris' }) {
  console.log(`${nom}, ${age} ans, habite à ${ville}`);
}

afficherPersonne({ nom: 'Alice', age: 30 }); 
// "Alice, 30 ans, habite à Paris"
```

### Closures

Une closure est une fonction qui a accès aux variables de sa portée externe, même après que la fonction externe ait terminé son exécution.

```javascript
function creerCompteur() {
  let compte = 0;
  
  return {
    incrementer: function() {
      compte++;
      return compte;
    },
    decrementer: function() {
      compte--;
      return compte;
    },
    obtenir: function() {
      return compte;
    }
  };
}

const compteur = creerCompteur();
console.log(compteur.incrementer()); // 1
console.log(compteur.incrementer()); // 2
console.log(compteur.decrementer()); // 1
console.log(compteur.obtenir());     // 1
```

**Cas d'usage pratique : Fonction factory**
```javascript
function creerMultiplicateur(facteur) {
  return function(nombre) {
    return nombre * facteur;
  };
}

const doubler = creerMultiplicateur(2);
const tripler = creerMultiplicateur(3);

console.log(doubler(5));  // 10
console.log(tripler(5));  // 15
```

### IIFE (Immediately Invoked Function Expression)

```javascript
(function() {
  const prive = 'variable privée';
  console.log('Exécutée immédiatement');
})();

// Avec paramètres
(function(nom) {
  console.log(`Bonjour ${nom}`);
})('Alice');
```

### Fonctions de Rappel (Callbacks)

```javascript
function traiterDonnees(donnees, callback) {
  const resultat = donnees.map(x => x * 2);
  callback(resultat);
}

traiterDonnees([1, 2, 3], function(resultat) {
  console.log(resultat); // [2, 4, 6]
});
```

### Méthodes de Fonction

```javascript
function presentation(salutation, ponctuation) {
  return `${salutation}, je suis ${this.nom}${ponctuation}`;
}

const personne = { nom: 'Alice' };

// call - arguments individuels
presentation.call(personne, 'Bonjour', '!'); 
// "Bonjour, je suis Alice!"

// apply - arguments en tableau
presentation.apply(personne, ['Salut', '.']); 
// "Salut, je suis Alice."

// bind - crée une nouvelle fonction avec this fixé
const presentationAlice = presentation.bind(personne);
presentationAlice('Hello', '!'); // "Hello, je suis Alice!"
```

---

## 7. Programmation Orientée Objet

### Classes ES6+

**Déclaration de classe**
```javascript
class Personne {
  // Constructeur
  constructor(nom, age) {
    this.nom = nom;
    this.age = age;
  }
  
  // Méthode d'instance
  sePresenter() {
    return `Je m'appelle ${this.nom} et j'ai ${this.age} ans.`;
  }
  
  // Méthode statique
  static espece() {
    return 'Homo sapiens';
  }
  
  // Getter
  get infos() {
    return `${this.nom} (${this.age} ans)`;
  }
  
  // Setter
  set anniversaire(annee) {
    this.age = new Date().getFullYear() - annee;
  }
}

const alice = new Personne('Alice', 30);
console.log(alice.sePresenter());
console.log(Personne.espece()); // Méthode statique
console.log(alice.infos); // Getter
alice.anniversaire = 1993; // Setter
```

### Héritage

```javascript
class Animal {
  constructor(nom) {
    this.nom = nom;
  }
  
  parler() {
    return `${this.nom} fait un bruit.`;
  }
}

class Chien extends Animal {
  constructor(nom, race) {
    super(nom); // Appelle le constructeur parent
    this.race = race;
  }
  
  parler() {
    return `${this.nom} aboie!`;
  }
  
  description() {
    return `${this.nom} est un ${this.race}`;
  }
}

const chien = new Chien('Rex', 'Labrador');
console.log(chien.parler()); // "Rex aboie!"
console.log(chien.description()); // "Rex est un Labrador"
```

### Propriétés et Méthodes Privées

```javascript
class CompteBancaire {
  #solde = 0; // Propriété privée
  
  constructor(soldeInitial) {
    this.#solde = soldeInitial;
  }
  
  #validerMontant(montant) { // Méthode privée
    return montant > 0;
  }
  
  deposer(montant) {
    if (this.#validerMontant(montant)) {
      this.#solde += montant;
      return true;
    }
    return false;
  }
  
  retirer(montant) {
    if (this.#validerMontant(montant) && this.#solde >= montant) {
      this.#solde -= montant;
      return true;
    }
    return false;
  }
  
  get solde() {
    return this.#solde;
  }
}

const compte = new CompteBancaire(1000);
compte.deposer(500);
console.log(compte.solde); // 1500
// console.log(compte.#solde); // SyntaxError : propriété privée
```

### Composition vs Héritage

```javascript
// Composition : préférée à l'héritage multiple
const peutNager = {
  nager() {
    return `${this.nom} nage`;
  }
};

const peutVoler = {
  voler() {
    return `${this.nom} vole`;
  }
};

class Canard {
  constructor(nom) {
    this.nom = nom;
    Object.assign(this, peutNager, peutVoler);
  }
}

const donald = new Canard('Donald');
console.log(donald.nager()); // "Donald nage"
console.log(donald.voler()); // "Donald vole"
```

### Prototypes

```javascript
// Avant ES6, utilisation de prototypes
function Voiture(marque, modele) {
  this.marque = marque;
  this.modele = modele;
}

Voiture.prototype.demarrer = function() {
  return `${this.marque} ${this.modele} démarre`;
};

const voiture = new Voiture('Toyota', 'Corolla');
console.log(voiture.demarrer());

// Vérification
console.log(voiture instanceof Voiture); // true
console.log(Object.getPrototypeOf(voiture) === Voiture.prototype); // true
```

---

## 8. Modules

### Export

**Export nommé**
```javascript
// mathUtils.js
export const PI = 3.14159;

export function addition(a, b) {
  return a + b;
}

export class Calculatrice {
  multiplier(a, b) {
    return a * b;
  }
}

// Export groupé
const CONSTANTE = 42;
function helper() { }
export { CONSTANTE, helper };
```

**Export par défaut**
```javascript
// logger.js
export default class Logger {
  log(message) {
    console.log(message);
  }
}

// OU avec fonction
export default function log(message) {
  console.log(message);
}
```

### Import

**Import nommé**
```javascript
// Importer des exports spécifiques
import { addition, PI } from './mathUtils.js';

// Renommer lors de l'import
import { addition as add } from './mathUtils.js';

// Importer tout
import * as Math from './mathUtils.js';
console.log(Math.PI);
console.log(Math.addition(2, 3));
```

**Import par défaut**
```javascript
import Logger from './logger.js';

const logger = new Logger();
logger.log('Message');
```

**Import mixte**
```javascript
import Logger, { PI, addition } from './module.js';
```

**Import dynamique**
```javascript
// Chargement conditionnel
async function chargerModule() {
  if (condition) {
    const module = await import('./module.js');
    module.fonction();
  }
}

// Import à la demande
button.addEventListener('click', async () => {
  const { afficher } = await import('./ui.js');
  afficher();
});
```

### Réexport

```javascript
// index.js - Point d'entrée centralisé
export { addition, soustraction } from './operations.js';
export { default as Logger } from './logger.js';
export * from './utils.js';
```

---

## 9. Programmation Asynchrone

### Callbacks

```javascript
function recupererDonnees(callback) {
  setTimeout(() => {
    const donnees = { id: 1, nom: 'Produit' };
    callback(donnees);
  }, 1000);
}

recupererDonnees(function(resultat) {
  console.log(resultat);
});
```

### Promesses (Promises)

**Création d'une promesse**
```javascript
const promesse = new Promise((resolve, reject) => {
  const succes = true;
  
  setTimeout(() => {
    if (succes) {
      resolve('Opération réussie');
    } else {
      reject('Erreur survenue');
    }
  }, 1000);
});
```

**Utilisation des promesses**
```javascript
promesse
  .then(resultat => {
    console.log(resultat);
    return 'Nouvelle valeur';
  })
  .then(valeur => {
    console.log(valeur);
  })
  .catch(erreur => {
    console.error(erreur);
  })
  .finally(() => {
    console.log('Terminé, peu importe le résultat');
  });
```

**Chaînage de promesses**
```javascript
function etape1() {
  return Promise.resolve('Résultat 1');
}

function etape2(donnees) {
  return Promise.resolve(donnees + ' -> Résultat 2');
}

function etape3(donnees) {
  return Promise.resolve(donnees + ' -> Résultat 3');
}

etape1()
  .then(etape2)
  .then(etape3)
  .then(resultat => console.log(resultat))
  .catch(erreur => console.error(erreur));
```

### Méthodes Statiques des Promesses

**Promise.all** - Attend que toutes les promesses soient résolues
```javascript
const promesse1 = Promise.resolve(3);
const promesse2 = new Promise(resolve => setTimeout(() => resolve(42), 1000));
const promesse3 = Promise.resolve('foo');

Promise.all([promesse1, promesse2, promesse3])
  .then(valeurs => {
    console.log(valeurs); // [3, 42, 'foo']
  });
```

**Promise.race** - Retourne la première promesse résolue/rejetée
```javascript
const p1 = new Promise(resolve => setTimeout(() => resolve('rapide'), 500));
const p2 = new Promise(resolve => setTimeout(() => resolve('lent'), 1000));

Promise.race([p1, p2])
  .then(valeur => console.log(valeur)); // "rapide"
```

**Promise.allSettled** - Attend toutes les promesses (réussies ou échouées)
```javascript
const promesses = [
  Promise.resolve(1),
  Promise.reject('erreur'),
  Promise.resolve(3)
];

Promise.allSettled(promesses)
  .then(resultats => {
    resultats.forEach(result => console.log(result.status));
    // "fulfilled", "rejected", "fulfilled"
  });
```

**Promise.any** - Retourne la première promesse réussie
```javascript
const p1 = Promise.reject('erreur 1');
const p2 = new Promise(resolve => setTimeout(() => resolve('succès'), 500));
const p3 = Promise.reject('erreur 2');

Promise.any([p1, p2, p3])
  .then(valeur => console.log(valeur)); // "succès"
```

### Async/Await

**Fonction asynchrone**
```javascript
async function recupererUtilisateur(id) {
  // Simulation d'un appel API
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve({ id, nom: 'Alice', age: 30 });
    }, 1000);
  });
}

async function afficherUtilisateur() {
  try {
    const utilisateur = await recupererUtilisateur(1);
    console.log(utilisateur);
  } catch (erreur) {
    console.error('Erreur:', erreur);
  }
}

afficherUtilisateur();
```

**Gestion d'erreurs avec async/await**
```javascript
async function traiterDonnees() {
  try {
    const donnees1 = await fetch('/api/endpoint1').then(r => r.json());
    const donnees2 = await fetch('/api/endpoint2').then(r => r.json());
    return { donnees1, donnees2 };
  } catch (erreur) {
    console.error('Échec de récupération:', erreur);
    throw erreur;
  } finally {
    console.log('Nettoyage effectué');
  }
}
```

**Parallélisation avec async/await**
```javascript
async function recupererPlusieursUtilisateurs() {
  // Exécution séquentielle (lente)
  const user1 = await recupererUtilisateur(1);
  const user2 = await recupererUtilisateur(2);
  
  // Exécution parallèle (rapide)
  const [utilisateur1, utilisateur2] = await Promise.all([
    recupererUtilisateur(1),
    recupererUtilisateur(2)
  ]);
  
  return [utilisateur1, utilisateur2];
}
```

**Async/await dans les boucles**
```javascript
// Séquentiel
async function traiterSequentiellement(ids) {
  const resultats = [];
  for (const id of ids) {
    const resultat = await recupererUtilisateur(id);
    resultats.push(resultat);
  }
  return resultats;
}

// Parallèle
async function traiterEnParallele(ids) {
  const promesses = ids.map(id => recupererUtilisateur(id));
  return await Promise.all(promesses);
}
```

### Générateurs et Itérateurs

**Fonction génératrice**
```javascript
function* compteur() {
  let i = 0;
  while (i < 3) {
    yield i++;
  }
}

const gen = compteur();
console.log(gen.next()); // { value: 0, done: false }
console.log(gen.next()); // { value: 1, done: false }
console.log(gen.next()); // { value: 2, done: false }
console.log(gen.next()); // { value: undefined, done: true }

// Utilisation avec for...of
for (const valeur of compteur()) {
  console.log(valeur); // 0, 1, 2
}
```

**Générateurs asynchrones**
```javascript
async function* genererDonnees() {
  yield await Promise.resolve(1);
  yield await Promise.resolve(2);
  yield await Promise.resolve(3);
}

(async () => {
  for await (const valeur of genererDonnees()) {
    console.log(valeur); // 1, 2, 3
  }
})();
```

---

## 10. Collections et Structures de Données

### Tableaux (Arrays)

**Création et accès**
```javascript
const fruits = ['pomme', 'banane', 'orange'];
const vide = [];
const mixte = [1, 'texte', true, { cle: 'valeur' }];

console.log(fruits[0]); // 'pomme'
console.log(fruits.length); // 3
```

**Méthodes de modification**
```javascript
const arr = [1, 2, 3];

// Ajout/suppression en fin
arr.push(4);           // [1, 2, 3, 4]
arr.pop();             // [1, 2, 3]

// Ajout/suppression en début
arr.unshift(0);        // [0, 1, 2, 3]
arr.shift();           // [1, 2, 3]

// Splice - ajouter/supprimer à n'importe quelle position
arr.splice(1, 1);      // Supprime 1élément à l'index 1 → [1, 3]
arr.splice(1, 0, 2);   // Insère 2 à l'index 1 → [1, 2, 3]
arr.splice(1, 2, 'a', 'b'); // Remplace 2 éléments → [1, 'a', 'b']
```

**Méthodes de transformation**
```javascript
const nombres = [1, 2, 3, 4, 5];

// map - transforme chaque élément
const doubles = nombres.map(n => n * 2); // [2, 4, 6, 8, 10]

// filter - filtre les éléments
const pairs = nombres.filter(n => n % 2 === 0); // [2, 4]

// reduce - réduit à une seule valeur
const somme = nombres.reduce((acc, n) => acc + n, 0); // 15

// reduceRight - réduit de droite à gauche
const concatene = ['a', 'b', 'c'].reduceRight((acc, v) => acc + v); // 'cba'

// flatMap - map puis aplatit d'un niveau
const resultats = [1, 2, 3].flatMap(x => [x, x * 2]); // [1, 2, 2, 4, 3, 6]
```

**Méthodes de recherche**
```javascript
const arr = [10, 20, 30, 40, 50];

// find - premier élément correspondant
const trouve = arr.find(n => n > 25); // 30

// findIndex - index du premier élément correspondant
const index = arr.findIndex(n => n > 25); // 2

// findLast - dernier élément correspondant (ES2023)
const dernier = arr.findLast(n => n > 25); // 50

// findLastIndex - index du dernier élément correspondant (ES2023)
const dernierIndex = arr.findLastIndex(n => n > 25); // 4

// includes - vérifie la présence
arr.includes(30); // true

// indexOf - premier index
arr.indexOf(30); // 2

// lastIndexOf - dernier index
[1, 2, 3, 2, 1].lastIndexOf(2); // 3

// some - au moins un élément correspond
arr.some(n => n > 40); // true

// every - tous les éléments correspondent
arr.every(n => n > 0); // true
```

**Méthodes de copie et combinaison**
```javascript
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];

// concat - combine des tableaux
const combine = arr1.concat(arr2); // [1, 2, 3, 4, 5, 6]

// slice - copie une portion
const portion = arr1.slice(1, 3); // [2, 3]
const copie = arr1.slice(); // Copie complète

// flat - aplatit les tableaux imbriqués
const imbrique = [1, [2, [3, [4]]]];
imbrique.flat();    // [1, 2, [3, [4]]]
imbrique.flat(2);   // [1, 2, 3, [4]]
imbrique.flat(Infinity); // [1, 2, 3, 4]

// toSpliced, toSorted, toReversed - versions non-mutantes (ES2023)
const original = [3, 1, 2];
const trie = original.toSorted(); // [1, 2, 3], original inchangé
const inverse = original.toReversed(); // [2, 1, 3], original inchangé
```

**Méthodes de tri et ordre**
```javascript
const nombres = [3, 1, 4, 1, 5, 9, 2];

// sort - trie (mute le tableau)
nombres.sort((a, b) => a - b); // [1, 1, 2, 3, 4, 5, 9]

// reverse - inverse (mute le tableau)
nombres.reverse(); // [9, 5, 4, 3, 2, 1, 1]

// Tri de chaînes
const mots = ['banane', 'Ananas', 'cerise'];
mots.sort(); // ['Ananas', 'banane', 'cerise'] (sensible à la casse)
mots.sort((a, b) => a.localeCompare(b)); // Tri insensible à la casse
```

**Méthodes d'itération**
```javascript
const arr = ['a', 'b', 'c'];

// forEach - exécute une fonction pour chaque élément
arr.forEach((element, index) => {
  console.log(`${index}: ${element}`);
});

// entries - itérateur [index, valeur]
for (const [index, valeur] of arr.entries()) {
  console.log(index, valeur);
}

// keys - itérateur des index
for (const index of arr.keys()) {
  console.log(index); // 0, 1, 2
}

// values - itérateur des valeurs
for (const valeur of arr.values()) {
  console.log(valeur); // 'a', 'b', 'c'
}
```

**Méthodes utiles**
```javascript
const arr = [1, 2, 3];

// join - convertit en chaîne
arr.join('-'); // '1-2-3'

// fill - remplit avec une valeur
arr.fill(0); // [0, 0, 0]
arr.fill(5, 1, 3); // [0, 5, 5] (de l'index 1 à 3 exclus)

// copyWithin - copie une section à un autre endroit
[1, 2, 3, 4, 5].copyWithin(0, 3); // [4, 5, 3, 4, 5]

// at - accès avec index négatif
arr.at(-1); // Dernier élément
arr.at(-2); // Avant-dernier élément
```

**Déstructuration de tableaux**
```javascript
const [premier, deuxieme, ...reste] = [1, 2, 3, 4, 5];
console.log(premier);  // 1
console.log(deuxieme); // 2
console.log(reste);    // [3, 4, 5]

// Ignorer des éléments
const [a, , c] = [1, 2, 3];
console.log(a, c); // 1, 3

// Valeurs par défaut
const [x = 0, y = 0] = [1];
console.log(x, y); // 1, 0
```

### Objets (Objects)

**Création et accès**
```javascript
// Notation littérale
const personne = {
  nom: 'Alice',
  age: 30,
  'clé avec espaces': 'valeur'
};

// Accès par point
console.log(personne.nom); // 'Alice'

// Accès par crochets
console.log(personne['age']); // 30
console.log(personne['clé avec espaces']); // 'valeur'

// Propriétés calculées
const cle = 'profession';
const obj = {
  [cle]: 'développeur',
  ['valeur_' + 123]: 'dynamique'
};
```

**Méthodes raccourcies**
```javascript
const nom = 'Bob';
const age = 25;

// Raccourci de propriété
const utilisateur = { nom, age }; // { nom: 'Bob', age: 25 }

// Raccourci de méthode
const objet = {
  methode() {
    return 'Bonjour';
  }
};
```

**Méthodes Object**
```javascript
const obj = { a: 1, b: 2, c: 3 };

// Object.keys - tableau des clés
Object.keys(obj); // ['a', 'b', 'c']

// Object.values - tableau des valeurs
Object.values(obj); // [1, 2, 3]

// Object.entries - tableau [clé, valeur]
Object.entries(obj); // [['a', 1], ['b', 2], ['c', 3]]

// Object.fromEntries - inverse de entries
const entrees = [['x', 10], ['y', 20]];
Object.fromEntries(entrees); // { x: 10, y: 20 }

// Object.assign - copie/fusionne des objets
const cible = { a: 1 };
Object.assign(cible, { b: 2 }, { c: 3 }); // { a: 1, b: 2, c: 3 }

// Spread operator (préféré)
const fusionne = { ...obj, d: 4 }; // { a: 1, b: 2, c: 3, d: 4 }

// Object.freeze - rend immuable
const immuable = Object.freeze({ x: 1 });
// immuable.x = 2; // Erreur en mode strict

// Object.seal - empêche ajout/suppression de propriétés
const scelle = Object.seal({ x: 1 });
scelle.x = 2; // OK
// scelle.y = 3; // Erreur

// Object.hasOwn - vérifie la propriété propre (ES2022)
Object.hasOwn(obj, 'a'); // true
Object.hasOwn(obj, 'toString'); // false (hérité)

// Object.getOwnPropertyNames - toutes les propriétés propres
Object.getOwnPropertyNames(obj); // ['a', 'b', 'c']

// Object.create - crée avec prototype spécifique
const proto = { saluer() { return 'Bonjour'; } };
const nouveau = Object.create(proto);
nouveau.saluer(); // 'Bonjour'
```

**Déstructuration d'objets**
```javascript
const personne = {
  nom: 'Alice',
  age: 30,
  adresse: {
    ville: 'Paris',
    pays: 'France'
  }
};

// Déstructuration simple
const { nom, age } = personne;

// Renommage
const { nom: prenom } = personne;

// Valeurs par défaut
const { profession = 'Inconnu' } = personne;

// Déstructuration imbriquée
const { adresse: { ville } } = personne;

// Rest dans les objets
const { nom: n, ...reste } = personne;
console.log(reste); // { age: 30, adresse: {...} }
```

**Descripteurs de propriétés**
```javascript
const obj = {};

Object.defineProperty(obj, 'nom', {
  value: 'Alice',
  writable: false,      // Non modifiable
  enumerable: true,     // Visible dans for...in
  configurable: false   // Non supprimable
});

// Object.defineProperties - définir plusieurs propriétés
Object.defineProperties(obj, {
  age: {
    value: 30,
    writable: true
  },
  ville: {
    value: 'Paris',
    enumerable: true
  }
});

// Obtenir le descripteur
Object.getOwnPropertyDescriptor(obj, 'nom');
```

### Map

Structure clé-valeur où les clés peuvent être de n'importe quel type.

```javascript
// Création
const map = new Map();

// Ajout d'éléments
map.set('cle1', 'valeur1');
map.set(42, 'nombre');
map.set({ id: 1 }, 'objet comme clé');

// Création avec itérable
const map2 = new Map([
  ['nom', 'Alice'],
  ['age', 30]
]);

// Accès
map.get('cle1'); // 'valeur1'

// Vérification
map.has('cle1'); // true

// Suppression
map.delete('cle1');

// Taille
map.size; // nombre d'éléments

// Effacer tout
map.clear();

// Itération
for (const [cle, valeur] of map) {
  console.log(`${cle}: ${valeur}`);
}

map.forEach((valeur, cle) => {
  console.log(`${cle}: ${valeur}`);
});

// Méthodes d'itération
map.keys();    // Itérateur des clés
map.values();  // Itérateur des valeurs
map.entries(); // Itérateur [clé, valeur]

// Conversion
const objetDepuisMap = Object.fromEntries(map);
const mapDepuisObjet = new Map(Object.entries(objet));
```

**Cas d'usage : Map vs Object**
```javascript
// Map : clés de tout type, ordre préservé, meilleures performances
const map = new Map();
map.set(1, 'un');
map.set('1', 'un string');
map.set(true, 'vrai');

// Object : clés string/symbol uniquement
const obj = {
  1: 'un',           // Converti en string
  '1': 'écrase',     // Même que ci-dessus
  true: 'vrai'       // Converti en string 'true'
};
```

### Set

Collection de valeurs uniques.

```javascript
// Création
const set = new Set();

// Ajout
set.add(1);
set.add(2);
set.add(2); // Ignoré (déjà présent)

// Création avec itérable
const set2 = new Set([1, 2, 3, 2, 1]); // Set {1, 2, 3}

// Vérification
set.has(1); // true

// Suppression
set.delete(1);

// Taille
set.size;

// Effacer
set.clear();

// Itération
for (const valeur of set) {
  console.log(valeur);
}

set.forEach(valeur => {
  console.log(valeur);
});

// Conversion en tableau
const tableau = [...set];
const tableau2 = Array.from(set);

// Éliminer les doublons d'un tableau
const nombres = [1, 2, 2, 3, 3, 4];
const uniques = [...new Set(nombres)]; // [1, 2, 3, 4]
```

**Opérations sur les ensembles**
```javascript
const setA = new Set([1, 2, 3]);
const setB = new Set([2, 3, 4]);

// Union
const union = new Set([...setA, ...setB]); // {1, 2, 3, 4}

// Intersection
const intersection = new Set([...setA].filter(x => setB.has(x))); // {2, 3}

// Différence
const difference = new Set([...setA].filter(x => !setB.has(x))); // {1}

// Différence symétrique
const diffSym = new Set([
  ...[...setA].filter(x => !setB.has(x)),
  ...[...setB].filter(x => !setA.has(x))
]); // {1, 4}
```

### WeakMap et WeakSet

Collections avec références faibles (permettent le garbage collection).

```javascript
// WeakMap - clés doivent être des objets
const weakMap = new WeakMap();
let obj = { id: 1 };

weakMap.set(obj, 'données associées');
console.log(weakMap.get(obj)); // 'données associées'

// Quand obj n'a plus de référence, l'entrée est supprimée automatiquement
obj = null; // L'entrée dans weakMap sera collectée

// WeakSet - valeurs doivent être des objets
const weakSet = new WeakSet();
let element = { nom: 'test' };

weakSet.add(element);
console.log(weakSet.has(element)); // true

element = null; // L'élément sera collecté

// Cas d'usage : données privées
const donneesPrive = new WeakMap();

class Personne {
  constructor(nom) {
    donneesPrive.set(this, { nom });
  }
  
  getNom() {
    return donneesPrive.get(this).nom;
  }
}
```

### Tableaux Typés (Typed Arrays)

Tableaux pour données binaires brutes.

```javascript
// Création
const buffer = new ArrayBuffer(16); // 16 octets
const int32 = new Int32Array(buffer); // Vue 32-bit
const int8 = new Int8Array(buffer);  // Vue 8-bit

// Types disponibles
const uint8 = new Uint8Array(4);     // 0 à 255
const int16 = new Int16Array(4);     // -32768 à 32767
const float32 = new Float32Array(4); // Nombres flottants
const float64 = new Float64Array(4); // Double précision

// Utilisation
uint8[0] = 255;
uint8[1] = 128;

// Méthodes similaires aux Array
uint8.map(x => x * 2);
uint8.filter(x => x > 100);
```

---

## 11. Gestion des Erreurs

### Try...Catch...Finally

```javascript
try {
  // Code susceptible de générer une erreur
  const resultat = riskyOperation();
  console.log(resultat);
} catch (erreur) {
  // Gestion de l'erreur
  console.error('Une erreur est survenue:', erreur.message);
} finally {
  // Exécuté dans tous les cas
  console.log('Nettoyage effectué');
}
```

### Types d'Erreurs Natives

```javascript
// Error - erreur générique
throw new Error('Message d'erreur');

// SyntaxError - erreur de syntaxe
try {
  eval('x = ;'); // Syntaxe invalide
} catch (e) {
  console.log(e instanceof SyntaxError); // true
}

// ReferenceError - référence invalide
try {
  console.log(variableInexistante);
} catch (e) {
  console.log(e instanceof ReferenceError); // true
}

// TypeError - type inapproprié
try {
  null.method();
} catch (e) {
  console.log(e instanceof TypeError); // true
}

// RangeError - valeur hors limites
try {
  const arr = new Array(-1);
} catch (e) {
  console.log(e instanceof RangeError); // true
}

// URIError - URI mal formé
try {
  decodeURIComponent('%');
} catch (e) {
  console.log(e instanceof URIError); // true
}
```

### Erreurs Personnalisées

```javascript
class ValidationError extends Error {
  constructor(message) {
    super(message);
    this.name = 'ValidationError';
  }
}

class DatabaseError extends Error {
  constructor(message, code) {
    super(message);
    this.name = 'DatabaseError';
    this.code = code;
  }
}

// Utilisation
function validerEmail(email) {
  if (!email.includes('@')) {
    throw new ValidationError('Email invalide');
  }
  return true;
}

try {
  validerEmail('test');
} catch (erreur) {
  if (erreur instanceof ValidationError) {
    console.log('Erreur de validation:', erreur.message);
  } else {
    console.log('Erreur inconnue:', erreur);
  }
}
```

### Gestion d'Erreurs Asynchrones

**Avec Promesses**
```javascript
fetch('https://api.example.com/data')
  .then(response => {
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    return response.json();
  })
  .then(data => console.log(data))
  .catch(erreur => {
    console.error('Erreur lors de la récupération:', erreur);
  });
```

**Avec Async/Await**
```javascript
async function recupererDonnees() {
  try {
    const response = await fetch('https://api.example.com/data');
    
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    
    const data = await response.json();
    return data;
  } catch (erreur) {
    console.error('Erreur:', erreur.message);
    throw erreur; // Relancer si nécessaire
  }
}
```

### Patterns de Gestion d'Erreurs

**Pattern Result/Either**
```javascript
function diviser(a, b) {
  if (b === 0) {
    return { success: false, error: 'Division par zéro' };
  }
  return { success: true, value: a / b };
}

const resultat = diviser(10, 2);
if (resultat.success) {
  console.log('Résultat:', resultat.value);
} else {
  console.error('Erreur:', resultat.error);
}
```

**Retry Pattern**
```javascript
async function retryOperation(operation, maxRetries = 3, delay = 1000) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await operation();
    } catch (erreur) {
      if (i === maxRetries - 1) throw erreur;
      await new Promise(resolve => setTimeout(resolve, delay));
    }
  }
}

// Utilisation
retryOperation(() => fetch('https://api.example.com/data'))
  .then(response => console.log('Succès'))
  .catch(erreur => console.error('Échec après plusieurs tentatives'));
```

**Global Error Handler**
```javascript
// Navigateur
window.addEventListener('error', (event) => {
  console.error('Erreur globale:', event.error);
  // Envoyer à un service de logging
});

window.addEventListener('unhandledrejection', (event) => {
  console.error('Promesse rejetée non gérée:', event.reason);
});

// Node.js
process.on('uncaughtException', (erreur) => {
  console.error('Exception non capturée:', erreur);
  process.exit(1);
});

process.on('unhandledRejection', (raison, promesse) => {
  console.error('Promesse rejetée non gérée:', raison);
});
```

---

## 12. Bonnes Pratiques et Conventions

### Conventions de Nommage

```javascript
// camelCase pour les variables et fonctions
const utilisateurActif = true;
function calculerTotal() { }

// PascalCase pour les classes
class UtilisateurService { }

// SCREAMING_SNAKE_CASE pour les constantes
const API_URL = 'https://api.example.com';
const MAX_TENTATIVES = 3;

// Noms descriptifs et explicites
// ❌ Mauvais
const d = new Date();
function calc(a, b) { return a + b; }

// ✅ Bon
const dateCreation = new Date();
function calculerSomme(nombre1, nombre2) { return nombre1 + nombre2; }

// Préfixes booléens
const isActive = true;
const hasPermission = false;
const canEdit = true;
const shouldUpdate = false;

// Noms de fonctions actifs
function obtenirUtilisateur() { }
function creerProduit() { }
function validerFormulaire() { }
function transformerDonnees() { }
```

### Structure et Organisation du Code

```javascript
// Ordre recommandé dans un fichier
// 1. Imports
import { fonction } from './module';

// 2. Constantes
const CONSTANTE = 'valeur';

// 3. Fonctions utilitaires
function helper() { }

// 4. Composant/Classe principal
class Principal { }

// 5. Export
export default Principal;
```

### Utilisation de const/let

```javascript
// ✅ Préférer const par défaut
const configuration = { api: 'url' };
const utilisateurs = [];

// ✅ Utiliser let uniquement si réassignation nécessaire
let compteur = 0;
compteur++;

// ❌ Éviter var
var ancienne = 'ne pas utiliser';
```

### Fonctions Pures

```javascript
// ✅ Fonction pure : même entrée = même sortie, sans effet de bord
function addition(a, b) {
  return a + b;
}

// ❌ Fonction impure : modifie l'état externe
let total = 0;
function additionImpure(n) {
  total += n;
  return total;
}

// ✅ Version pure
function additionPure(total, n) {
  return total + n;
}
```

### Éviter les Effets de Bord

```javascript
// ❌ Mutation directe
function ajouterElement(tableau, element) {
  tableau.push(element);
  return tableau;
}

// ✅ Création d'une nouvelle copie
function ajouterElement(tableau, element) {
  return [...tableau, element];
}

// ❌ Mutation d'objet
function mettreAJour(objet, cle, valeur) {
  objet[cle] = valeur;
  return objet;
}

// ✅ Immutabilité
function mettreAJour(objet, cle, valeur) {
  return { ...objet, [cle]: valeur };
}
```

### Comparaisons et Égalité

```javascript
// ✅ Toujours utiliser === et !== (égalité stricte)
if (valeur === 42) { }
if (texte !== '') { }

// ❌ Éviter == et != (conversion de type implicite)
if (valeur == '42') { } // Ne pas faire

// Vérifications de nullité
if (valeur === null || valeur === undefined) { }
// Ou plus court
if (valeur == null) { } // Acceptable pour null/undefined

// ✅ Vérification avec l'opérateur de coalescence
const resultat = valeur ?? 'défaut';
```

### Gestion Asynchrone

```javascript
// ✅ Préférer async/await
async function recupererDonnees() {
  try {
    const response = await fetch(url);
    const data = await response.json();
    return data;
  } catch (erreur) {
    console.error(erreur);
  }
}

// ❌ Callback hell (éviter)
fetch(url, (response) => {
  parse(response, (data) => {
    save(data, (result) => {
      // Pyramide de l'enfer
    });
  });
});

// ✅ Parallélisation quand possible
const [users, posts, comments] = await Promise.all([
  fetchUsers(),
  fetchPosts(),
  fetchComments()
]);
```

### Destructuration et Spread

```javascript
// ✅ Utiliser la destructuration
const { nom, age } = utilisateur;
const [premier, second] = tableau;

// ✅ Utiliser spread pour copier
const copieTableau = [...original];
const copieObjet = { ...original };

// ✅ Rest parameters
function somme(...nombres) {
  return nombres.reduce((a, b) => a + b, 0);
}
```

### Template Literals

```javascript
// ✅ Template literals pour concaténation
const message = `Bonjour ${nom}, vous avez ${age} ans`;

// ❌ Éviter la concaténation avec +
const message = 'Bonjour ' + nom + ', vous avez ' + age + ' ans';

// ✅ Multiligne
const html = `
  <div>
    <h1>${titre}</h1>
    <p>${contenu}</p>
  </div>
`;
```

### Commentaires Efficaces

```javascript
// ✅ Commentaires pour expliquer le "pourquoi", pas le "quoi"
// Désactivation temporaire du cache pour résoudre le bug #1234
cache.disable();

// ❌ Commentaire inutile (le code est auto-descriptif)
// Incrémente i
i++;

// ✅ JSDoc pour documenter les fonctions
/**
 * Calcule le prix total avec TVA
 * @param {number} prixHT - Prix hors taxe
 * @param {number} tauxTVA - Taux de TVA (ex: 0.2 pour 20%)
 * @returns {number} Prix TTC
 */
function calculerPrixTTC(prixHT, tauxTVA) {
  return prixHT * (1 + tauxTVA);
}

// ✅ TODO pour marquer le travail à faire
// TODO: Ajouter validation des données
// FIXME: Corriger le calcul pour les nombres négatifs
// NOTE: Cette fonction sera dépréciée en v2.0
```

### Validation et Defensive Programming

```javascript
// ✅ Valider les entrées
function diviser(a, b) {
  if (typeof a !== 'number' || typeof b !== 'number') {
    throw new TypeError('Les arguments doivent être des nombres');
  }
  if (b === 0) {
    throw new Error('Division par zéro impossible');
  }
  return a / b;
}

// ✅ Valeurs par défaut
function creerUtilisateur(nom, age = 18, role = 'utilisateur') {
  return { nom, age, role };
}

// ✅ Guard clauses (retours anticipés)
function traiterCommande(commande) {
  if (!commande) return null;
  if (!commande.valide) return null;
  if (commande.montant <= 0) return null;
  
  // Logique principale
  return processOrder(commande);
}
```

### Performance

```javascript
// ✅ Mise en cache des résultats coûteux
const cache = new Map();

function calculComplexe(n) {
  if (cache.has(n)) {
    return cache.get(n);
  }
  
  const resultat = /* calcul coûteux */;
  cache.set(n, resultat);
  return resultat;
}

// ✅ Debouncing pour les événements fréquents
function debounce(func, delai) {
  let timeout;
  return function(...args) {
    clearTimeout(timeout);
    timeout = setTimeout(() => func.apply(this, args), delai);
  };
}

const rechercheDebounced = debounce(rechercher, 300);
input.addEventListener('input', rechercheDebounced);

// ✅ Éviter les opérations dans les boucles
// ❌ Mauvais
for (let i = 0; i < arr.length; i++) {
  // arr.length recalculé à chaque itération
}

// ✅ Bon
const longueur = arr.length;
for (let i = 0; i < longueur; i++) {
  // longueur mise en cache
}
```

### Style de Code Moderne

```javascript
//✅ Fonctions fléchées concises
const double = n => n * 2;
const filtrerPairs = arr => arr.filter(n => n % 2 === 0);

// ✅ Chaînage de méthodes
const resultat = donnees
  .filter(item => item.actif)
  .map(item => item.valeur)
  .reduce((acc, val) => acc + val, 0);

// ✅ Optional chaining et nullish coalescing
const ville = utilisateur?.adresse?.ville ?? 'Non spécifié';

// ✅ Déstructuration dans les paramètres
function afficherProfil({ nom, age, email = 'Non fourni' }) {
  console.log(`${nom}, ${age} ans, ${email}`);
}

// ✅ Modules ES6
// Exporter
export const constante = 42;
export function utilitaire() { }
export default class Principal { }

// Importer
import Principal, { constante, utilitaire } from './module';
```

### Éviter les Anti-Patterns

```javascript
// ❌ Modifier les prototypes natifs
Array.prototype.monMethode = function() { }; // Ne jamais faire

// ❌ Variables globales excessives
window.maVariable = 'valeur'; // Pollue l'espace global

// ❌ Utilisation de eval()
eval('console.log("dangereux")'); // Risque de sécurité

// ❌ With statement
with (obj) {
  // Code confus et performant mal
}

// ❌ Callback hell
fetch(url, (data) => {
  process(data, (result) => {
    save(result, (saved) => {
      // Pyramide difficile à maintenir
    });
  });
});

// ✅ Utiliser async/await à la place
async function traiter() {
  const data = await fetch(url);
  const result = await process(data);
  const saved = await save(result);
  return saved;
}

// ❌ Ignorer les erreurs silencieusement
try {
  operation();
} catch (e) {
  // Rien - erreur perdue
}

// ✅ Toujours gérer ou propager les erreurs
try {
  operation();
} catch (erreur) {
  console.error('Erreur dans operation:', erreur);
  throw erreur; // Ou gérer appropriement
}
```

### Principes SOLID Appliqués à JavaScript

**Single Responsibility Principle**
```javascript
// ❌ Classe avec trop de responsabilités
class Utilisateur {
  constructor(nom, email) {
    this.nom = nom;
    this.email = email;
  }
  
  sauvegarder() { /* DB logic */ }
  envoyerEmail() { /* Email logic */ }
  valider() { /* Validation logic */ }
}

// ✅ Responsabilités séparées
class Utilisateur {
  constructor(nom, email) {
    this.nom = nom;
    this.email = email;
  }
}

class UtilisateurRepository {
  sauvegarder(utilisateur) { /* DB logic */ }
}

class EmailService {
  envoyerEmail(destinataire, message) { /* Email logic */ }
}

class UtilisateurValidator {
  valider(utilisateur) { /* Validation logic */ }
}
```

**Open/Closed Principle**
```javascript
// ✅ Ouvert à l'extension, fermé à la modification
class CalculateurPrix {
  calculer(produit, strategiePrix) {
    return strategiePrix.calculer(produit);
  }
}

class PrixNormal {
  calculer(produit) {
    return produit.prixBase;
  }
}

class PrixPromo {
  calculer(produit) {
    return produit.prixBase * 0.8;
  }
}

// Ajout d'une nouvelle stratégie sans modifier le code existant
class PrixVIP {
  calculer(produit) {
    return produit.prixBase * 0.7;
  }
}
```

**Dependency Injection**
```javascript
// ❌ Dépendance rigide
class ServiceCommande {
  constructor() {
    this.db = new Database(); // Dépendance directe
  }
}

// ✅ Injection de dépendance
class ServiceCommande {
  constructor(database) {
    this.db = database; // Injecté depuis l'extérieur
  }
}

// Utilisation
const db = new Database();
const service = new ServiceCommande(db);
```

### Tests et Testabilité

```javascript
// ✅ Code testable : fonctions pures
function calculerRemise(prixTotal, pourcentage) {
  return prixTotal * (pourcentage / 100);
}

// Test simple
console.assert(calculerRemise(100, 10) === 10);

// ✅ Séparer la logique de l'I/O
// Logique pure
function validerEmail(email) {
  return email.includes('@') && email.includes('.');
}

// I/O séparé
async function traiterInscription(email) {
  if (!validerEmail(email)) {
    throw new Error('Email invalide');
  }
  return await saveToDatabase(email);
}

// ✅ Utiliser des interfaces claires
class UtilisateurService {
  constructor(repository, emailService) {
    this.repository = repository;
    this.emailService = emailService;
  }
  
  async creerUtilisateur(donnees) {
    const utilisateur = new Utilisateur(donnees);
    await this.repository.sauvegarder(utilisateur);
    await this.emailService.envoyerBienvenue(utilisateur.email);
    return utilisateur;
  }
}
```

### Sécurité

```javascript
// ✅ Sanitiser les entrées utilisateur
function sanitizeInput(input) {
  return input
    .trim()
    .replace(/[<>]/g, '') // Enlever balises HTML basiques
    .substring(0, 1000); // Limiter la longueur
}

// ✅ Éviter XSS lors de l'insertion dans le DOM
// ❌ Dangereux
element.innerHTML = userInput;

// ✅ Sûr
element.textContent = userInput;
// ou
const div = document.createElement('div');
div.textContent = userInput;

// ✅ Utiliser Content Security Policy (CSP)
// En-tête HTTP : Content-Security-Policy: default-src 'self'

// ✅ Valider les données côté serveur et client
function validerAge(age) {
  const ageNum = parseInt(age, 10);
  if (isNaN(ageNum) || ageNum < 0 || ageNum > 150) {
    throw new Error('Âge invalide');
  }
  return ageNum;
}

// ✅ Utiliser des tokens CSRF pour les requêtes sensibles
async function envoyerFormulaire(donnees, csrfToken) {
  return fetch('/api/action', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'X-CSRF-Token': csrfToken
    },
    body: JSON.stringify(donnees)
  });
}

// ✅ Ne jamais stocker de données sensibles côté client
// ❌ Dangereux
localStorage.setItem('password', userPassword);

// ✅ Utiliser des tokens sécurisés
localStorage.setItem('authToken', secureToken); // Token, pas mot de passe
```

### Documentation du Code

```javascript
/**
 * Classe représentant un panier d'achat
 * @class
 */
class Panier {
  /**
   * Crée un panier
   * @constructor
   * @param {string} utilisateurId - ID de l'utilisateur
   */
  constructor(utilisateurId) {
    this.utilisateurId = utilisateurId;
    this.articles = [];
  }
  
  /**
   * Ajoute un article au panier
   * @param {Object} article - L'article à ajouter
   * @param {string} article.id - ID du produit
   * @param {number} article.quantite - Quantité
   * @param {number} article.prix - Prix unitaire
   * @returns {boolean} true si l'ajout a réussi
   * @throws {Error} Si la quantité est invalide
   */
  ajouterArticle(article) {
    if (article.quantite <= 0) {
      throw new Error('La quantité doit être positive');
    }
    this.articles.push(article);
    return true;
  }
  
  /**
   * Calcule le total du panier
   * @returns {number} Le montant total
   */
  calculerTotal() {
    return this.articles.reduce((total, article) => {
      return total + (article.prix * article.quantite);
    }, 0);
  }
}

/**
 * @typedef {Object} Produit
 * @property {string} id - ID unique du produit
 * @property {string} nom - Nom du produit
 * @property {number} prix - Prix en euros
 * @property {string} [description] - Description optionnelle
 */

/**
 * Récupère un produit par son ID
 * @async
 * @param {string} id - ID du produit
 * @returns {Promise<Produit>} Le produit trouvé
 * @throws {Error} Si le produit n'existe pas
 */
async function obtenirProduit(id) {
  const response = await fetch(`/api/produits/${id}`);
  if (!response.ok) {
    throw new Error('Produit non trouvé');
  }
  return response.json();
}
```

### Formatage et Style

```javascript
// ✅ Indentation cohérente (2 ou 4 espaces)
function exemple() {
  if (condition) {
    console.log('Indenté');
  }
}

// ✅ Espaces autour des opérateurs
const somme = a + b;
const condition = x === y;

// ✅ Point-virgule en fin de ligne (ou pas, mais cohérent)
const a = 1;
const b = 2;

// ✅ Longueur de ligne raisonnable (80-120 caractères)
const messageComplet = `Ceci est un message long qui devrait être 
  divisé sur plusieurs lignes pour rester lisible`;

// ✅ Organisation des imports
// 1. Dépendances externes
import React from 'react';
import axios from 'axios';

// 2. Imports internes
import { config } from './config';
import { utils } from './utils';

// 3. Imports relatifs
import MonComposant from './MonComposant';

// ✅ Grouper les déclarations similaires
const x = 1;
const y = 2;
const z = 3;

let compteur = 0;
let index = 0;

// ✅ Ligne vide entre les sections logiques
function fonction1() {
  // Code
}

function fonction2() {
  // Code
}
```

### Configuration ESLint (Exemple)

```javascript
// .eslintrc.json
{
  "extends": "eslint:recommended",
  "env": {
    "browser": true,
    "es2021": true,
    "node": true
  },
  "parserOptions": {
    "ecmaVersion": 2021,
    "sourceType": "module"
  },
  "rules": {
    "semi": ["error", "always"],
    "quotes": ["error", "single"],
    "no-var": "error",
    "prefer-const": "error",
    "no-unused-vars": "warn",
    "eqeqeq": ["error", "always"],
    "curly": "error",
    "arrow-spacing": "error",
    "no-console": "warn"
  }
}
```

### Prettier Configuration (Exemple)

```javascript
// .prettierrc
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 80,
  "arrowParens": "avoid"
}
```

---

## Résumé des Points Essentiels

### À Faire (Best Practices)
- ✅ Utiliser `const` par défaut, `let` si nécessaire, jamais `var`
- ✅ Préférer `===` et `!==` pour les comparaisons
- ✅ Utiliser les fonctions fléchées pour les callbacks courts
- ✅ Privilégier `async/await` pour le code asynchrone
- ✅ Destructurer les objets et tableaux quand approprié
- ✅ Utiliser les template literals pour les chaînes
- ✅ Écrire des fonctions pures et éviter les effets de bord
- ✅ Valider les entrées et gérer les erreurs explicitement
- ✅ Commenter le "pourquoi", pas le "quoi"
- ✅ Organiser le code en modules réutilisables
- ✅ Utiliser des noms de variables descriptifs
- ✅ Implémenter le principe de responsabilité unique

### À Éviter (Anti-Patterns)
- ❌ Modifier les prototypes natifs
- ❌ Utiliser `eval()` ou `with`
- ❌ Créer des variables globales excessives
- ❌ Ignorer les erreurs silencieusement
- ❌ Écrire des fonctions avec trop de responsabilités
- ❌ Utiliser `==` au lieu de `===`
- ❌ Muter les paramètres ou l'état externe
- ❌ Imbriquer profondément les callbacks (callback hell)
- ❌ Mélanger code synchrone et asynchrone sans clarté
- ❌ Oublier la gestion d'erreurs dans le code async

### Outils Recommandés
- **ESLint** : Linter pour détecter les erreurs et faire respecter le style
- **Prettier** : Formateur de code automatique
- **Babel** : Transpiler pour supporter les anciennes versions de JS
- **TypeScript** : Sur-ensemble typé de JavaScript (optionnel)
- **Jest/Mocha** : Frameworks de test
- **Webpack/Vite** : Bundlers pour modules

---

## Ressources Complémentaires

### Documentation Officielle
- **MDN Web Docs** : https://developer.mozilla.org/fr/docs/Web/JavaScript
- **ECMAScript Specification** : https://tc39.es/ecma262/

### Guides de Style Populaires
- **Airbnb JavaScript Style Guide**
- **Google JavaScript Style Guide**
- **StandardJS**

### Concepts Avancés à Explorer
- **Proxy et Reflect** : Métaprogrammation
- **Web Workers** : Multithreading côté client
- **Service Workers** : Applications progressives (PWA)
- **WebAssembly** : Intégration avec JavaScript
- **Design Patterns** : Singleton, Factory, Observer, etc.
- **Functional Programming** : Composition, currying, monades
- **Reactive Programming** : RxJS et observables
- **Memory Management** : Garbage collection, memory leaks

---

Cette note de référence couvre l'essentiel de JavaScript moderne. Elle est conçue pour être consultée régulièrement et mise à jour au fil de l'évolution du langage. JavaScript continue d'évoluer avec de nouvelles fonctionnalités ajoutées chaque année via le processus TC39.

**Version du guide : Basée sur ES2023 (ECMAScript 14)**
