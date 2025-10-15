---
title: DOM (Document Object Model)
draft: false
tags:
  - dom
  - javascript
description: Langage de script principalement utilisé pour le développement web côté client, mais aussi côté serveur avec Node.js.
---


# Note de Référence : Le Document Object Model (DOM) en JavaScript

## Introduction : Qu'est-ce que le DOM ?

Le **Document Object Model (DOM)** est une interface de programmation (API) pour les documents HTML et XML. Il représente la structure d'un document comme un arbre de nœuds, où chaque nœud correspond à une partie du document (un élément, un attribut, un texte, etc.).

Le DOM n'est pas JavaScript lui-même, mais une norme du W3C. JavaScript est le langage le plus couramment utilisé pour interagir avec le DOM dans les navigateurs web. Grâce au DOM, JavaScript peut accéder, lire et manipuler dynamiquement le contenu, la structure et le style d'une page web.

**Analogie clé :** Pensez à votre document HTML comme un arbre généalogique. Le DOM est la représentation de cet arbre en mémoire, et JavaScript vous donne les outils pour naviguer dans cet arbre, modifier ses branches (éléments) et interagir avec elles.

---

## 1. La Structure du DOM et la Hiérarchie des Nœuds

Le DOM représente un document sous forme d'une structure arborescente de **nœuds** (`Node`).

- **Nœud Racine :** Le nœud `document` est à la racine de l'arbre.
- **Nœuds Éléments (`Element`) :** Chaque balise HTML (comme `<body>`, `<div>`, `<p>`, `<a>`) devient un nœud élément.
- **Nœuds Texte (`Text`) :** Le contenu textuel à l'intérieur d'un élément est un nœud texte.
- **Nœuds Attributs (`Attribute`) :** Les attributs des balises (comme `class` ou `href`) sont aussi des nœuds, bien qu'ils soient plus souvent manipulés comme des propriétés des nœuds éléments.
- **Nœuds Commentaires (`Comment`) :** Les commentaires HTML (`<!-- ... -->`) sont des nœuds.

### Hiérarchie :
- **Parent (`parentNode`) :** Un nœud qui en contient un autre.
- **Enfant (`childNodes`) :** Les nœuds directement contenus dans un autre nœud.
- **Frère/Sœur (`sibling`) :** Les nœuds qui ont le même parent.

```html
<!-- Exemple de structure HTML -->
<html>
<head>
  <title>Titre de la page</title>
</head>
<body>
  <h1 id="main-title">Titre Principal</h1>
  <p>Un paragraphe avec un <a href="#">lien</a>.</p>
  <!-- Un commentaire -->
</body>
</html>
```
Cette structure est représentée par le DOM comme un arbre de nœuds interconnectés.

---

## 2. Sélectionner des Éléments (Accès au DOM)

Pour manipuler un élément, il faut d'abord le sélectionner.

### 2.1. Sélection d'un seul élément

- **`document.getElementById(id)`**
  - Sélectionne l'unique élément ayant l'ID spécifié. Très rapide.
  - **Retourne :** Un objet `Element` ou `null` si non trouvé.
  ```javascript
  const mainTitle = document.getElementById('main-title');
  ```

- **`document.querySelector(selector)`**
  - Sélectionne le **premier** élément correspondant au sélecteur CSS spécifié. Très flexible.
  - **Retourne :** Un objet `Element` ou `null`.
  ```javascript
  // Sélectionne le premier paragraphe
  const firstParagraph = document.querySelector('p');
  // Sélectionne le lien dans le paragraphe avec la classe 'intro'
  const link = document.querySelector('p.intro a');
  ```

### 2.2. Sélection de plusieurs éléments

- **`document.getElementsByClassName(className)`**
  - Sélectionne tous les éléments ayant la classe spécifiée.
  - **Retourne :** Une `HTMLCollection` (live/vivante) des éléments. "Live" signifie que si vous ajoutez un nouvel élément avec cette classe, la collection se mettra à jour automatiquement.
  ```javascript
  const buttons = document.getElementsByClassName('btn');
  ```

- **`document.getElementsByTagName(tagName)`**
  - Sélectionne tous les éléments avec le nom de balise spécifié.
  - **Retourne :** Une `HTMLCollection` (live/vivante).
  ```javascript
  const allParagraphs = document.getElementsByTagName('p');
  ```

- **`document.querySelectorAll(selector)`**
  - Sélectionne tous les éléments correspondant au sélecteur CSS.
  - **Retourne :** Une `NodeList` (statique). "Statique" signifie qu'elle ne se met pas à jour si le DOM change. C'est souvent plus prévisible. On peut utiliser `forEach` directement sur une `NodeList`.
  ```javascript
  const allListItems = document.querySelectorAll('ul.main-list li');
  allListItems.forEach(item => {
    console.log(item.textContent);
  });
  ```

> **Note :** `HTMLCollection` et `NodeList` ressemblent à des tableaux mais n'en sont pas. Pour les convertir en vrais tableaux, utilisez `Array.from(collection)`.

---

## 3. Naviguer dans l'Arbre DOM

Une fois un élément sélectionné, vous pouvez naviguer vers ses parents, enfants ou frères.

- **`element.parentNode` / `element.parentElement`**
  - `parentNode` peut être n'importe quel type de nœud (y compris `document`).
  - `parentElement` est toujours un `HTMLElement` (ou `null`). C'est souvent plus pratique.

- **`element.childNodes` / `element.children`**
  - `childNodes` retourne une `NodeList` de tous les nœuds enfants (y compris les nœuds texte et commentaires).
  - `children` retourne une `HTMLCollection` contenant uniquement les nœuds **éléments** enfants. C'est généralement ce que l'on veut.

- **`element.firstChild` / `element.firstElementChild`**
- **`element.lastChild` / `element.lastElementChild`**
  - Les versions `...Element...` ignorent les nœuds texte et commentaires.

- **`element.nextSibling` / `element.nextElementSibling`**
- **`element.previousSibling` / `element.previousElementSibling`**
  - De même, les versions `...ElementSibling` se déplacent uniquement entre les nœuds éléments.

```javascript
const list = document.querySelector('ul');

// Parent
console.log(list.parentElement); // Le conteneur de la liste

// Enfants
const listItems = list.children; // HTMLCollection des <li>
console.log(listItems[0]); // Le premier <li>

// Frères
const secondItem = listItems[1];
console.log(secondItem.previousElementSibling); // Le premier <li>
console.log(secondItem.nextElementSibling);     // Le troisième <li>
```

---

## 4. Manipuler les Éléments

### 4.1. Modifier le Contenu

- **`element.innerHTML`**
  - Lit ou définit le contenu HTML d'un élément. Puissant mais **dangereux** si utilisé avec des données utilisateur (risque d'attaques XSS).
  ```javascript
  const container = document.getElementById('content');
  container.innerHTML = '<h2>Nouveau titre</h2><p>Contenu dynamique.</p>';
  ```

- **`element.textContent`**
  - Lit ou définit le contenu textuel brut d'un élément et de ses descendants. Les balises HTML sont interprétées comme du texte. **Plus sûr et souvent plus performant que `innerHTML`**.
  ```javascript
  const title = document.getElementById('main-title');
  title.textContent = 'Titre mis à jour';
  ```

### 4.2. Modifier les Attributs

- **`element.getAttribute(name)`** : Récupère la valeur d'un attribut.
- **`element.setAttribute(name, value)`** : Définit la valeur d'un attribut.
- **`element.hasAttribute(name)`** : Vérifie si un attribut existe.
- **`element.removeAttribute(name)`** : Supprime un attribut.

```javascript
const link = document.querySelector('a');
link.setAttribute('href', 'https://www.nouvelle-destination.com');
link.setAttribute('target', '_blank');
console.log(link.getAttribute('href')); // Affiche l'URL
```

Pour les attributs courants (`id`, `src`, `href`, `class`), on peut aussi utiliser l'accès direct via les propriétés :
```javascript
const image = document.querySelector('img');
image.src = 'nouvelle-image.jpg';
image.alt = 'Description de la nouvelle image';
```

### 4.3. Modifier les Styles et les Classes

#### Styles en ligne (inline)
À utiliser avec parcimonie pour des modifications dynamiques spécifiques.
```javascript
const element = document.getElementById('my-element');
element.style.color = 'blue';
element.style.backgroundColor = 'lightgray'; // Propriétés CSS en camelCase
element.style.fontSize = '1.5rem';
```

#### Classes CSS (méthode recommandée)
Il est préférable de manipuler les classes CSS plutôt que les styles en ligne pour séparer le style (CSS) du comportement (JS).

- **`element.classList`**
  - **`add('className')`** : Ajoute une classe.
  - **`remove('className')`** : Retire une classe.
  - **`toggle('className')`** : Ajoute la classe si elle est absente, la retire si elle est présente.
  - **`contains('className')`** : Vérifie la présence d'une classe.
```javascript
const panel = document.getElementById('panel');
panel.classList.add('visible');
panel.classList.remove('hidden');
panel.classList.toggle('highlight');

if (panel.classList.contains('visible')) {
  console.log("Le panneau est visible.");
}
```

---

## 5. Création et Suppression Dynamique de Nœuds

### 5.1. Créer des Nœuds

- **`document.createElement(tagName)`** : Crée un nouvel élément.
- **`document.createTextNode(text)`** : Crée un nœud texte.

### 5.2. Ajouter des Nœuds

- **`parent.appendChild(child)`** : Ajoute `child` comme dernier enfant de `parent`.
- **`parent.insertBefore(newNode, referenceNode)`** : Insère `newNode` avant `referenceNode` dans `parent`.
- **`element.append(...nodes)`** : Méthode moderne pour ajouter des nœuds ou du texte à la fin d'un élément.
- **`element.prepend(...nodes)`** : Méthode moderne pour ajouter des nœuds ou du texte au début d'un élément.

```javascript
// Créer un nouvel élément <li>
const newItem = document.createElement('li');

// Lui donner du contenu
newItem.textContent = 'Nouvel item';

// Lui ajouter une classe
newItem.classList.add('list-item');

// Le sélectionner et l'ajouter à une liste <ul> existante
const list = document.querySelector('ul#main-list');
list.appendChild(newItem); // Ajoute à la fin de la liste
```

### 5.3. Supprimer et Remplacer des Nœuds

- **`parent.removeChild(child)`** : Supprime `child` de `parent`.
- **`element.remove()`** : Méthode moderne et simple pour qu'un élément se supprime lui-même.
- **`parent.replaceChild(newChild, oldChild)`** : Remplace `oldChild` par `newChild`.

```javascript
// Supprimer un élément
const itemToDelete = document.querySelector('#item-2');
if (itemToDelete) {
  itemToDelete.remove(); // Plus simple
  // Alternative : itemToDelete.parentNode.removeChild(itemToDelete);
}
```

---

## 6. Gestion des Événements

Le DOM événementiel permet de rendre les pages interactives en répondant aux actions de l'utilisateur (clics, frappes au clavier, etc.).

### 6.1. Le Modèle d'Événements : Bubbling et Capturing

Quand un événement se produit sur un élément (ex: un clic sur un bouton), il traverse le DOM en deux phases :
1.  **Phase de Capture (Capturing) :** L'événement descend de la racine (`window`) jusqu'à l'élément cible.
2.  **Phase de Bouillonnement (Bubbling) :** L'événement remonte de l'élément cible jusqu'à la racine.

Par défaut, tous les écouteurs d'événements fonctionnent en phase de **bubbling**.

### 6.2. Ajouter un Écouteur d'Événements

La méthode `addEventListener` est la manière moderne et recommandée de gérer les événements.

**Syntaxe :** `element.addEventListener(type, listener, [options])`
- `type` : Le nom de l'événement (ex: `'click'`, `'mouseover'`, `'keydown'`).
- `listener` : La fonction (callback) à exécuter quand l'événement se produit.
- `options` (optionnel) : Un objet pour configurer l'écouteur (`capture`, `once`, `passive`).

```javascript
const myButton = document.getElementById('my-btn');

function handleClick(event) {
  console.log('Bouton cliqué !');
  console.log('Cible de l\'événement :', event.target); // L'élément sur lequel on a cliqué
  console.log('Élément courant :', event.currentTarget); // L'élément qui a l'écouteur (myButton)
}

myButton.addEventListener('click', handleClick);
```

### 6.3. Supprimer un Écouteur d'Événements

Pour supprimer un écouteur, vous devez passer **exactement la même fonction** que celle utilisée pour l'ajout.

```javascript
myButton.removeEventListener('click', handleClick);
```
> **Important :** Cela ne fonctionne pas avec les fonctions anonymes. Vous devez utiliser une fonction nommée ou une référence.

### 6.4. L'Objet `Event`

La fonction `listener` reçoit un objet `Event` en argument, qui contient des informations cruciales sur l'événement.
- **`event.target`** : L'élément qui a déclenché l'événement à l'origine.
- **`event.currentTarget`** : L'élément sur lequel l'écouteur est attaché.
- **`event.preventDefault()`** : Empêche le comportement par défaut de l'événement (ex: un lien ne naviguera pas, un formulaire ne sera pas soumis).
- **`event.stopPropagation()`** : Arrête la propagation de l'événement (il ne "bouillonnera" pas plus haut dans l'arbre DOM).

```javascript
const form = document.querySelector('form');
form.addEventListener('submit', function(event) {
  event.preventDefault(); // Empêche le rechargement de la page
  console.log('Formulaire soumis via JS !');
});
```

---

## 7. Interfaces DOM Importantes

- **`EventTarget`** : Interface de base implémentée par les objets pouvant recevoir des événements (comme `Node` et `window`). Fournit `addEventListener()` et `removeEventListener()`.
- **`Node`** : Interface de base pour tous les types de nœuds du DOM (`parentNode`, `childNodes`, `textContent`).
- **`Element`** : Hérite de `Node`. Représente un élément HTML et ajoute des propriétés et méthodes spécifiques (`id`, `classList`, `getAttribute()`, `querySelector()`).
- **`HTMLElement`** : Hérite de `Element`. Représente n'importe quel élément HTML et ajoute des propriétés communes comme `style`, `title`, `onclick`.
- **`Document`** : Hérite de `Node`. Représente la page entière et est le point d'entrée pour accéder au contenu (`getElementById`, `createElement`, etc.).

---

## 8. Gestion des Formulaires

Le DOM facilite l'interaction avec les éléments de formulaire.

```html
<form id="user-form">
  <input type="text" name="username" value="JohnDoe">
  <input type="checkbox" name="subscribe" checked>
  <button type="submit">Envoyer</button>
</form>
```
```javascript
const form = document.getElementById('user-form');

// Accéder aux valeurs
const usernameInput = form.elements.username;
const subscribeCheckbox = form.elements.subscribe;

console.log(usernameInput.value); // "JohnDoe"
console.log(subscribeCheckbox.checked); // true

// Écouter les changements
usernameInput.addEventListener('input', (event) => {
  // Se déclenche à chaque frappe
  console.log('Valeur actuelle :', event.target.value);
});

// Gérer la soumission
form.addEventListener('submit', (event) => {
  event.preventDefault();
  alert(`Bienvenue, ${usernameInput.value}!`);
});
```

---

## 9. Bonnes Pratiques et Optimisation

Manipuler le DOM peut être coûteux en termes de performance. Le navigateur doit recalculer les styles et la disposition de la page (**reflow/repaint**) à chaque modification.

1.  **Minimiser l'Accès au DOM :** Stockez les éléments fréquemment utilisés dans des variables au lieu de les requêter à chaque fois.
    ```javascript
    // Mauvais
    document.querySelector('.container').style.width = '100px';
    document.querySelector('.container').style.height = '100px';

    // Bon
    const container = document.querySelector('.container');
    container.style.width = '100px';
    container.style.height = '100px';
    ```

2.  **Utiliser `DocumentFragment` pour les Modifications en Masse :** Un `DocumentFragment` est un conteneur de nœuds "hors DOM". Vous pouvez y ajouter de nombreux éléments, puis ajouter le fragment entier au DOM en une seule opération, ce qui ne provoque qu'un seul reflow.
    ```javascript
    const list = document.querySelector('ul');
    const fragment = document.createDocumentFragment();

    for (let i = 0; i < 10; i++) {
      const li = document.createElement('li');
      li.textContent = `Item ${i}`;
      fragment.appendChild(li); // Ajout au fragment (pas de reflow)
    }

    list.appendChild(fragment); // Un seul reflow pour 10 éléments
    ```

3.  **Délégation d'Événements (Event Delegation) :** Au lieu d'attacher un écouteur à chaque enfant d'une liste, attachez un seul écouteur au parent. Grâce au bubbling, vous pouvez intercepter l'événement sur le parent et utiliser `event.target` pour savoir quel enfant a été cliqué. C'est beaucoup plus performant, surtout pour les listes dynamiques.
    ```javascript
    const listContainer = document.getElementById('list-container');

    listContainer.addEventListener('click', (event) => {
      // Vérifier si l'élément cliqué est bien un <li>
      if (event.target && event.target.nodeName === 'LI') {
        console.log('Clic sur l\'item :', event.target.textContent);
      }
    });
    ```

4.  **Préférer `textContent` à `innerHTML`** lorsque vous n'insérez que du texte pour des raisons de performance et de sécurité.

5.  **Préférer `classList`** à la manipulation directe de `element.className` car c'est une API plus propre et plus puissante.

