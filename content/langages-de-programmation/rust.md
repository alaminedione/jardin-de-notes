# **Note de Référence sur le Langage de Programmation Rust**

Rust est un langage de programmation système axé sur trois objectifs principaux : la **sécurité**, la **vitesse** et la **concurrence**. Il y parvient sans avoir recours à un ramasse-miettes (garbage collector), grâce à un ensemble de règles strictes gérées par le compilateur, notamment le système de propriété (ownership).

---

## 1. Syntaxe de Base et Structure d'un Programme

Un programme Rust est composé de fonctions, de modules et d'autres éléments. Le point d'entrée est toujours la fonction `main`.

**Structure de base :**
```rust
// Ceci est un commentaire sur une ligne

/*
  Ceci est un commentaire
  sur plusieurs lignes.
*/

// La fonction principale, point d'entrée du programme
fn main() {
    // La macro println! affiche du texte à la console
    println!("Hello, world!");

    // Déclaration de variable immutable (par défaut)
    let x = 5;

    // Déclaration de variable mutable
    let mut y = 10;
    y = 20;

    // Les constantes doivent avoir un type explicite et sont toujours immutables
    const MAX_POINTS: u32 = 100_000;

    println!("x = {}, y = {}, MAX_POINTS = {}", x, y, MAX_POINTS);

    // Le "shadowing" (occultation) permet de redéclarer une variable
    let x = "cinq";
    println!("x est maintenant une chaîne de caractères : {}", x);
}
```

---

## 2. Types de Données

Rust est un langage à typage statique, ce qui signifie que le type de chaque variable doit être connu à la compilation.

### 2.1 Types Primitifs (Scalaires)

- **Entiers signés :** `i8`, `i16`, `i32`, `i64`, `i128`, `isize` (taille de l'architecture)
- **Entiers non signés :** `u8`, `u16`, `u32`, `u64`, `u128`, `usize`
- **Nombres à virgule flottante :** `f32`, `f64` (défaut)
- **Booléens :** `bool` (valeurs `true` ou `false`)
- **Caractères :** `char` (représente un caractère Unicode, sur 4 octets)

```rust
fn primitive_types() {
    let entier: i32 = -10;
    let flottant: f64 = 3.14;
    let est_vrai: bool = true;
    let caractere: char = '😻'; // Supporte Unicode
}
```

### 2.2 Types Composés

- **Tuples :** Une collection de valeurs de types différents, de taille fixe.
- **Tableaux (Arrays) :** Une collection de valeurs du même type, de taille fixe, allouée sur la pile (stack).

```rust
fn compound_types() {
    // Un tuple avec différents types
    let tup: (i32, f64, u8) = (500, 6.4, 1);
    
    // Déstructuration d'un tuple
    let (x, y, z) = tup;
    println!("La valeur de y est : {}", y); // Affiche 6.4

    // Accès par index
    let cinq_cents = tup.0;

    // Un tableau de 5 entiers i32
    let arr: [i32; 5] = [1, 2, 3, 4, 5];
    
    // Initialiser un tableau avec la même valeur
    let a = [3; 5]; // [3, 3, 3, 3, 3]

    // Accès par index
    let premier_element = arr[0];
}
```

---

## 3. Gestion de la Mémoire : Ownership, Borrowing et Lifetimes

C'est le concept central de Rust. Il garantit la sécurité de la mémoire sans garbage collector.

### 3.1 Ownership (Propriété)

1.  Chaque valeur en Rust a une variable qui est son **propriétaire** (owner).
2.  Il ne peut y avoir qu'**un seul propriétaire** à la fois.
3.  Lorsque le propriétaire sort de la portée (scope), la valeur est **détruite** (dropped).

```rust
fn ownership_example() {
    // s1 est le propriétaire de la String "hello"
    let s1 = String::from("hello"); 

    // La propriété de la valeur est "déplacée" (moved) de s1 à s2.
    // s1 n'est plus valide après cette ligne pour éviter les double-free.
    let s2 = s1; 

    // println!("s1 = {}", s1); // Erreur de compilation : value borrowed here after move

    println!("s2 = {}", s2); // Valide
} // s2 sort de la portée, sa mémoire est libérée.
```
*Les types simples comme les entiers implémentent le trait `Copy`, ils sont donc copiés au lieu d'être déplacés.*

### 3.2 Borrowing (Emprunt)

Pour utiliser une valeur sans en prendre la propriété, on peut l'**emprunter** via une **référence**.

- **Référence immuable (`&T`) :** On peut avoir plusieurs références immuables en même temps.
- **Référence mutable (`&mut T`) :** On ne peut avoir qu'une seule référence mutable à la fois dans une portée donnée.

**Règles :** Dans une portée donnée, vous pouvez avoir *soit* une référence mutable, *soit* un nombre quelconque de références immuables, mais pas les deux.

```rust
fn calculate_length(s: &String) -> usize { // s est une référence à une String
    s.len()
} // s sort de la portée, mais comme il ne possède pas la valeur, rien n'est libéré.

fn change_string(s: &mut String) {
    s.push_str(", world");
}

fn borrowing_example() {
    let mut s = String::from("hello");

    let len = calculate_length(&s); // Emprunt immuable
    println!("La longueur de '{}' est {}.", s, len);

    change_string(&mut s); // Emprunt mutable
    println!("{}", s); // Affiche "hello, world"
}
```

### 3.3 Lifetimes (Durées de Vie)

Les durées de vie sont une façon pour le compilateur de s'assurer que les références sont toujours valides. Dans la plupart des cas, le compilateur les infère, mais parfois des annotations explicites sont nécessaires.

**Objectif :** Empêcher les références pendantes (dangling references).

```rust
// 'a est un paramètre de durée de vie générique.
// Cette fonction dit que la référence retournée vivra aussi longtemps
// que la plus courte des durées de vie des références passées en argument.
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}

fn lifetime_example() {
    let string1 = String::from("abcd");
    let string2 = "xyz";

    let result = longest(string1.as_str(), string2);
    println!("La chaîne la plus longue est {}", result);
}
```

---

## 4. Fonctions, Closures et Macros

### 4.1 Fonctions

La syntaxe de déclaration utilise le mot-clé `fn`. Les types des arguments et de la valeur de retour doivent être déclarés. Rust utilise les expressions, la dernière expression d'une fonction est sa valeur de retour (sans `;`).

```rust
fn add(a: i32, b: i32) -> i32 {
    a + b // Pas de point-virgule, c'est une expression de retour
}
```

### 4.2 Closures

Ce sont des fonctions anonymes qui peuvent capturer des variables de leur environnement.

```rust
fn closure_example() {
    let x = 10;

    // Cette closure capture 'x' de son environnement
    let add_x = |y: i32| y + x;

    let result = add_x(5); // result = 15
    println!("Résultat : {}", result);
}
```

### 4.3 Macros

Les macros permettent d'écrire du code qui écrit du code (métaprogrammation). Elles sont identifiées par un `!`.

```rust
// println!, vec!, panic! sont des macros.
let my_vec = vec![1, 2, 3]; // Crée un Vec<i32>
```

---

## 5. Structures, Énumérations et Pattern Matching

### 5.1 Structures (`struct`)

Elles permettent de créer des types de données personnalisés en regroupant des champs.

```rust
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}

// Les méthodes sont définies dans un bloc `impl`
impl User {
    fn new(username: String, email: String) -> User {
        User {
            username,
            email,
            sign_in_count: 1,
            active: true,
        }
    }

    fn greet(&self) { // &self est une référence à l'instance
        println!("Bonjour, {}!", self.username);
    }
}

fn struct_example() {
    let mut user1 = User::new(
        String::from("user123"), 
        String::from("user@example.com")
    );
    user1.greet();
    user1.active = false;
}
```

### 5.2 Énumérations (`enum`)

Elles permettent de définir un type qui peut avoir une ou plusieurs variantes.

```rust
// Une enum peut contenir des données
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}

impl Message {
    fn call(&self) {
        // ...
    }
}
```

### 5.3 Pattern Matching (`match`)

L'instruction `match` est un puissant outil de contrôle de flux qui compare une valeur à une série de motifs et exécute le code correspondant. Le compilateur s'assure que tous les cas possibles sont traités (exhaustivité).

```rust
fn process_message(msg: Message) {
    match msg {
        Message::Quit => {
            println!("Le message est Quit");
        }
        Message::Move { x, y } => {
            println!("Déplacement vers x={}, y={}", x, y);
        }
        Message::Write(text) => {
            println!("Message texte : {}", text);
        }
        Message::ChangeColor(r, g, b) => {
            println!("Changement de couleur vers R={}, G={}, B={}", r, g, b);
        }
    }
}

// `if let` est un sucre syntaxique pour un `match` qui ne traite qu'un seul cas.
let some_value = Some(5);
if let Some(x) = some_value {
    println!("La valeur est {}", x);
}
```

---

## 6. Traits, Génériques et Programmation "Orientée Objet"

Rust n'est pas un langage orienté objet traditionnel (pas d'héritage de classes), mais il fournit des outils similaires.

### 6.1 Traits

Un trait définit un ensemble de méthodes qu'un type doit implémenter, similaire à une interface.

```rust
pub trait Summary {
    fn summarize(&self) -> String;

    // Implémentation par défaut
    fn default_summary(&self) -> String {
        String::from("(Lire la suite...)")
    }
}

pub struct NewsArticle {
    pub headline: String,
    pub author: String,
}

// Implémentation du trait Summary pour NewsArticle
impl Summary for NewsArticle {
    fn summarize(&self) -> String {
        format!("{}, par {}", self.headline, self.author)
    }
}
```

### 6.2 Génériques (Generics)

Les génériques permettent d'écrire du code qui fonctionne avec plusieurs types de données.

```rust
// T est un paramètre de type générique
// 'PartialOrd' est une contrainte de trait (trait bound)
// qui signifie que le type T doit pouvoir être comparé.
fn largest<T: PartialOrd>(list: &[T]) -> &T {
    let mut largest = &list[0];
    for item in list {
        if item > largest {
            largest = item;
        }
    }
    largest
}

fn generic_example() {
    let number_list = vec![34, 50, 25, 100, 65];
    println!("Le plus grand nombre est {}", largest(&number_list));
    
    let char_list = vec!['y', 'm', 'a', 'q'];
    println!("Le plus grand char est {}", largest(&char_list));
}
```

### 6.3 Programmation "Orientée Objet" en Rust

- **Encapsulation :** Les modules (`mod`) et le mot-clé `pub` contrôlent la visibilité.
- **Polymorphisme :** Les **objets de trait** (`Box<dyn Trait>`) permettent d'utiliser différentes implémentations d'un même trait de manière interchangeable.

---

## 7. Gestion des Erreurs : `Result` et `Option`

Rust n'a pas d'exceptions. Les erreurs sont gérées comme des valeurs retournées par les fonctions.

### 7.1 `Option<T>`

Utilisé lorsqu'une valeur peut être présente ou absente.
- `Some(T)` : Une valeur `T` est présente.
- `None` : Aucune valeur.

```rust
fn find_first_a(s: &str) -> Option<usize> {
    for (i, c) in s.char_indices() {
        if c == 'a' {
            return Some(i);
        }
    }
    None
}
```

### 7.2 `Result<T, E>`

Utilisé pour les opérations qui peuvent échouer.
- `Ok(T)` : L'opération a réussi et retourne une valeur `T`.
- `Err(E)` : L'opération a échoué et retourne une erreur `E`.

L'opérateur `?` propage les erreurs de manière concise.

```rust
use std::fs::File;
use std::io::Read;

// L'opérateur '?' propage l'erreur si l'une des opérations échoue.
fn read_username_from_file() -> Result<String, std::io::Error> {
    let mut f = File::open("hello.txt")?;
    let mut s = String::new();
    f.read_to_string(&mut s)?;
    Ok(s)
}
```

---

## 8. Concurrence

Rust garantit la sécurité de la concurrence à la compilation, prévenant ainsi les "data races".

### 8.1 Threads

Les threads permettent d'exécuter du code en parallèle. Les canaux (`channels`) sont utilisés pour communiquer entre les threads.

```rust
use std::thread;
use std::sync::mpsc; // multiple producer, single consumer

fn thread_example() {
    let (tx, rx) = mpsc::channel();

    thread::spawn(move || {
        let val = String::from("salut");
        tx.send(val).unwrap();
        // println!("{}", val); // Erreur: val a été déplacé dans le thread principal
    });

    let received = rx.recv().unwrap();
    println!("Reçu: {}", received);
}
```

### 8.2 `async/await` et Futures

Pour la concurrence asynchrone non bloquante.
- `async fn` : Crée une fonction qui retourne un `Future`.
- `.await` : Attend la résolution d'un `Future` sans bloquer le thread.

Un *runtime* asynchrone (ex: `tokio`, `async-std`) est nécessaire pour exécuter le code `async`.

```rust
// Cet exemple nécessite la dépendance `tokio`.
// async fn some_async_task() -> String {
//     // Simule une opération I/O
//     tokio::time::sleep(std::time::Duration::from_secs(1)).await;
//     "Tâche terminée".to_string()
// }
//
// #[tokio::main]
// async fn main() {
//     let result = some_async_task().await;
//     println!("{}", result);
// }
```

---

## 9. Manipulation des Collections Standards

### 9.1 Vecteurs (`Vec<T>`)

Liste de taille variable, allouée sur le tas (heap).

```rust
let mut v: Vec<i32> = Vec::new();
v.push(5);
v.push(6);

let v2 = vec![1, 2, 3]; // Macro pratique

// Accès sécurisé
match v2.get(2) {
    Some(val) => println!("Le troisième élément est {}", val),
    None => println!("Il n'y a pas de troisième élément."),
}

// Itération
for i in &v2 {
    println!("{}", i);
}
```

### 9.2 `HashMap<K, V>`

Table de hachage pour stocker des paires clé-valeur.

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();
scores.insert(String::from("Bleu"), 10);
scores.insert(String::from("Jaune"), 50);

let team_name = String::from("Bleu");
let score = scores.get(&team_name); // Renvoie un Option<&i32>
```

### 9.3 Chaînes de caractères (`String` et `&str`)

- `String` : Type de chaîne de caractères alloué sur le tas, de taille variable, mutable, et propriétaire de ses données (UTF-8).
- `&str` : Slice de chaîne, une référence immuable à une séquence de caractères UTF-8. Les littéraux de chaîne (`"hello"`) sont de type `&'static str`.

### 9.4 Slices (`&[T]`)

Une vue (référence) sur une partie contiguë d'une collection (comme un tableau ou un vecteur).

```rust
let a = [1, 2, 3, 4, 5];
let slice: &[i32] = &a[1..3]; // Contient une référence à [2, 3]
```

---

## 10. Gestion des Modules, Packages et Cargo

### 10.1 Cargo

Cargo est l'outil de gestion de projet et de build de Rust.
- `Cargo.toml` : Fichier de configuration du projet (dépendances, métadonnées).
- Commandes principales :
  - `cargo new my_project` : Crée un nouveau projet.
  - `cargo build` : Compile le projet.
  - `cargo run` : Compile et exécute.
  - `cargo test` : Lance les tests.
  - `cargo check` : Vérifie la compilation sans produire d'exécutable.
  - `cargo add <crate>` : Ajoute une dépendance.

### 10.2 Packages (Crates) et Modules (`mod`)

- **Crate** : Une unité de compilation. Peut être une librairie ou un binaire. Un projet Cargo est un package, qui contient au moins une crate.
- **Module** : Permet d'organiser le code au sein d'une crate. Le mot-clé `pub` rend les éléments publics.

**Structure de fichiers :**
```
my_project/
├── Cargo.toml
└── src/
    ├── main.rs       // Racine de la crate binaire
    ├── lib.rs        // Racine d'une crate librairie
    └── network/
        ├── mod.rs    // Déclaration du module 'network'
        └── client.rs // Sous-module
```

**Utilisation dans le code :**
```rust
// Dans src/main.rs
mod network; // Déclare le module network (cherche network.rs ou network/mod.rs)

// Utilise la fonction connect du sous-module client
use network::client::connect;

fn main() {
    connect();
}

// Dans src/network/mod.rs
pub mod client; // Déclare et rend public le sous-module client

// Dans src/network/client.rs
pub fn connect() {
    println!("Connexion...");
}
```

---

## 11. Bonnes Pratiques et Idiomes

- **Privilégier `Result` et `Option` :** Ne pas utiliser `panic!` pour les erreurs attendues. `unwrap()` et `expect()` sont acceptables dans les tests ou les prototypes.
- **Utiliser les itérateurs :** Les méthodes comme `map`, `filter`, `fold` sont souvent plus expressives et performantes que les boucles manuelles.
- **Dériver les traits communs :** Utiliser `#[derive(Debug, Clone, Copy, PartialEq, Eq)]` sur les structs et enums quand c'est pertinent.
- **Utiliser Clippy :** `cargo clippy` est un linter qui détecte les anti-patterns et suggère des améliorations idiomatiques.
- **Formater le code :** Utiliser `cargo fmt` pour maintenir un style de code cohérent.
- **Documenter le code public :** Utiliser les commentaires de documentation (`///` pour les items, `//!` pour les modules) pour générer de la documentation avec `cargo doc`.
