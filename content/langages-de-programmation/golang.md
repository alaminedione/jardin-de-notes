---
title: Go
draft: false
tags:
  - golang
  - programming
description: Langage de programmation compilé, concurrent et typé statiquement, conçu par Google.
---

# **Note de Référence Complète sur le Langage Go (Golang)**

Go est un langage de programmation open source créé par Google. Il est conçu pour être simple, efficace, fiable et particulièrement performant pour la programmation système et la gestion de la concurrence.

---

## 1. Structure d'un Programme Go

Tout programme exécutable en Go doit avoir un package `main` et une fonction `main()`.

- **Package** : L'unité de base de l'organisation du code.
- **Import** : Permet d'inclure d'autres packages.
- **Fonction `main`** : Le point d'entrée du programme.

**Exemple :**
```go
// Déclaration du package principal
package main

// Import du package pour le formatage des entrées/sorties
import "fmt"

// Point d'entrée du programme
func main() {
    fmt.Println("Hello, World!")
}
```
Pour exécuter : `go run main.go`

---

## 2. Syntaxe de Base et Types de Données

### 2.1. Variables, Constantes et Types Personnalisés

**Variables** :
- Déclaration longue : `var nom variable type`
- Déclaration courte (inférence de type) : `nom := valeur` (uniquement à l'intérieur des fonctions)

```go
package main

import "fmt"

func main() {
    // Déclaration longue avec initialisation
    var message string = "Bonjour"

    // Déclaration courte avec inférence de type
    nombre := 42

    // Déclaration de plusieurs variables
    var a, b int = 1, 2

    fmt.Println(message, nombre, a, b)
}
```

**Constantes** :
Déclarées avec le mot-clé `const`, leur valeur doit être connue à la compilation.

```go
const Pi = 3.14159
const (
    StatusOK = 200
    NotFound = 404
)
```

**Types personnalisés** :
On peut créer de nouveaux types à partir de types existants avec le mot-clé `type`.

```go
type Celsius float64
type IDUtilisateur int
```

### 2.2. Types de Données Primitifs

- **Nombres entiers** : `int`, `int8`, `int16`, `int32`, `int64` (signés) et `uint`, `uint8`, `uint16`, `uint32`, `uint64` (non signés). `int` a une taille dépendante de l'architecture (32 ou 64 bits).
- **Nombres à virgule flottante** : `float32`, `float64`.
- **Complexes** : `complex64`, `complex128`.
- **Booléens** : `bool` (`true` ou `false`).
- **Chaînes de caractères** : `string`. Les chaînes sont immuables.
- **Runes** : `rune`. C'est un alias pour `int32` et représente un point de code Unicode.

### 2.3. Structures de Contrôle

**`if / else`**
```go
x := 10
if x > 5 {
    fmt.Println("x est plus grand que 5")
} else {
    fmt.Println("x n'est pas plus grand que 5")
}
// Il est possible de déclarer une variable dans la condition
if y := 5; y < x {
    fmt.Println("y est plus petit que x")
}
```

**`for`** (la seule boucle en Go)
```go
// Boucle classique type "C"
for i := 0; i < 5; i++ {
    fmt.Println(i)
}

// Boucle type "while"
sum := 1
for sum < 100 {
    sum += sum
}

// Boucle infinie
// for { ... }

// Itération sur une collection (slice, map, etc.)
nums := []int{2, 3, 4}
for index, value := range nums {
    fmt.Printf("Index: %d, Valeur: %d\n", index, value)
}
```

**`switch`**
Le `break` est implicite à la fin de chaque `case`.

```go
jour := "mardi"
switch jour {
case "lundi":
    fmt.Println("Début de semaine")
case "mardi", "mercredi", "jeudi":
    fmt.Println("En semaine")
case "vendredi":
    fmt.Println("Bientôt le week-end")
default:
    fmt.Println("Week-end !")
}

// Switch sans expression (alternative à if/else if)
heure := 14
switch {
case heure < 12:
    fmt.Println("Matin")
case heure < 18:
    fmt.Println("Après-midi")
default:
    fmt.Println("Soir")
}
```

---

## 3. Types de Données Composés

### 3.1. Tableaux (Arrays)

Taille fixe, déterminée à la compilation. Peu utilisés directement, on leur préfère les slices.

```go
// Un tableau de 5 entiers
var a [5]int
a[4] = 100
fmt.Println(a) // [0 0 0 0 100]

// Déclaration et initialisation
b := [3]int{1, 2, 3}
fmt.Println(b) // [1 2 3]
```

### 3.2. Slices

Une "vue" dynamique et flexible sur un tableau sous-jacent. C'est le type de collection le plus utilisé en Go.

```go
// Création d'un slice
s := []int{10, 20, 30, 40, 50}

// Slicing : extraire une partie du slice
subSlice := s[1:4] // Éléments de l'index 1 à 3
fmt.Println(subSlice) // [20 30 40]

// Ajouter des éléments (peut réallouer un nouveau tableau)
s = append(s, 60)
fmt.Println(s) // [10 20 30 40 50 60]

// Utilisation de make pour créer un slice avec une longueur et une capacité
sliceAvecMake := make([]string, 3, 5) // longueur 3, capacité 5
fmt.Printf("len=%d cap=%d %v\n", len(sliceAvecMake), cap(sliceAvecMake), sliceAvecMake)
```

### 3.3. Maps

Table de hachage (dictionnaire) associant des clés à des valeurs.

```go
// Création d'une map de string vers int
m := make(map[string]int)

m["un"] = 1
m["deux"] = 2
fmt.Println(m) // map[deux:2 un:1]

// Accès à une valeur
valeur := m["un"]
fmt.Println(valeur) // 1

// Vérifier si une clé existe
v, ok := m["trois"]
if ok {
    fmt.Println("La clé existe, valeur :", v)
} else {
    fmt.Println("La clé 'trois' n'existe pas")
}

// Supprimer une clé
delete(m, "un")
```

### 3.4. Pointeeurs

Un pointeur contient l'adresse mémoire d'une variable.
- `&` : opérateur "adresse de".
- `*` : opérateur de déréférencement.

```go
i := 42
p := &i         // p contient l'adresse de i
fmt.Println(*p) // Lire la valeur de i à travers le pointeur
*p = 21         // Modifier la valeur de i à travers le pointeur
fmt.Println(i)  // Affiche 21
```

---

## 4. Fonctions et Méthodes

### 4.1. Fonctions

Les fonctions peuvent retourner plusieurs valeurs, ce qui est très courant pour la gestion des erreurs.

```go
// Fonction simple
func addition(a int, b int) int {
    return a + b
}

// Fonction avec plusieurs valeurs de retour
func diviser(a, b float64) (float64, error) {
    if b == 0 {
        return 0, fmt.Errorf("division par zéro impossible")
    }
    return a / b, nil // nil signifie "pas d'erreur"
}
```

### 4.2. Fonctions Anonymes (Lambdas) et Closures

Go supporte les fonctions anonymes, qui peuvent être assignées à des variables ou passées en paramètre. Une *closure* est une fonction qui "capture" les variables de son environnement lexical.

```go
// Fonction anonyme assignée à une variable
carré := func(x int) int {
    return x * x
}
fmt.Println(carré(5)) // 25

// Exemple de closure
func sequenceur() func() int {
    i := 0
    return func() int {
        i++
        return i
    }
}

nextInt := sequenceur()
fmt.Println(nextInt()) // 1
fmt.Println(nextInt()) // 2
```

### 4.3. `defer`

Le mot-clé `defer` exécute un appel de fonction juste avant que la fonction englobante ne retourne. Utile pour nettoyer des ressources (fermer des fichiers, des connexions, etc.). Les appels `defer` sont empilés (LIFO - Last In, First Out).

```go
func gestionFichier() {
    f, err := os.Open("fichier.txt")
    if err != nil { /* ... */ }
    defer f.Close() // f.Close() sera appelé à la fin de la fonction

    // ... travail avec le fichier ...
}
```

---

## 5. Programmation Orientée Objet avec Structs et Interfaces

Go n'a pas de classes. L'approche est basée sur les **structs**, les **méthodes** et les **interfaces**.

### 5.1. Structs

Une `struct` est une collection de champs typés. C'est le moyen de regrouper des données.

```go
type Personne struct {
    Nom    string
    Age    int
}

func main() {
    p := Personne{Nom: "Alice", Age: 30}
    fmt.Println(p.Nom) // Alice
}
```

### 5.2. Méthodes

Une méthode est une fonction avec un récepteur (receiver). Elle est attachée à un type spécifique.

```go
// p est le récepteur de la méthode
func (p Personne) Saluer() {
    fmt.Printf("Bonjour, je m'appelle %s et j'ai %d ans.\n", p.Nom, p.Age)
}

func main() {
    p := Personne{Nom: "Bob", Age: 25}
    p.Saluer()
}
```

### 5.3. Interfaces

Une interface est un contrat : un ensemble de signatures de méthodes. Un type *implémente* une interface de manière **implicite** s'il possède toutes les méthodes définies par l'interface.

```go
// Interface définissant un comportement
type Forme interface {
    Aire() float64
}

type Rectangle struct {
    Largeur, Hauteur float64
}

type Cercle struct {
    Rayon float64
}

// Rectangle implémente l'interface Forme
func (r Rectangle) Aire() float64 {
    return r.Largeur * r.Hauteur
}

// Cercle implémente l'interface Forme
func (c Cercle) Aire() float64 {
    return math.Pi * c.Rayon * c.Rayon
}

// Fonction qui utilise l'interface
func AfficherAire(f Forme) {
    fmt.Printf("L'aire est de %0.2f\n", f.Aire())
}

func main() {
    r := Rectangle{Largeur: 10, Hauteur: 5}
    c := Cercle{Rayon: 7}

    AfficherAire(r)
    AfficherAire(c)
}
```

---

## 6. Gestion des Erreurs

Go promeut une gestion explicite des erreurs. Les fonctions qui peuvent échouer retournent une valeur de type `error` comme dernière valeur de retour.

- Le type `error` est une interface simple avec une seule méthode : `Error() string`.
- Une valeur `nil` pour `error` signifie qu'il n'y a pas eu d'erreur.

**Le pattern standard :**
```go
valeur, err := fonctionQuiPeutEchouer()
if err != nil {
    // Gérer l'erreur (log, retour, etc.)
    log.Fatalf("Une erreur est survenue: %v", err)
}
// Utiliser la valeur

```

**`panic` et `recover`** :
- `panic` arrête le flux normal d'exécution et remonte la pile d'appels. À utiliser pour des erreurs véritablement exceptionnelles et non récupérables (ex: index hors des limites).
- `recover` permet de reprendre le contrôle d'une goroutine en panique. À utiliser avec `defer`. Rarement utilisé en dehors de la gestion de serveurs pour éviter un crash complet.

**Exemple complet**

```go
package main

import (
	"fmt"
    "errors"
)

func main() {
    result, err := div(10, 0)
    
    if err != nil { // err != nil signifie que l'erreur est non-nil donc que c'est une erreur
        fmt.Println(err)
    }
    fmt.Println(result)
}

func div(a, b int) (int, error) {
    if b == 0 {
        return 0, errors.New("division par zéro impossible")
    }
    return a / b, nil
}

```

---

## 7. Concurrence

La concurrence est une fonctionnalité centrale de Go.

### 7.1. Goroutines

Une goroutine est un "thread" léger géré par le runtime Go. Pour lancer une fonction dans une nouvelle goroutine, on utilise le mot-clé `go`.

```go
func dire(s string) {
    for i := 0; i < 3; i++ {
        time.Sleep(100 * time.Millisecond)
        fmt.Println(s)
    }
}

func main() {
    go dire("monde") // Lance une nouvelle goroutine
    dire("bonjour")  // S'exécute dans la goroutine principale

    // Sans un sleep ici, le programme principal se terminerait
    // avant que la goroutine "monde" ait le temps de s'exécuter.
    time.Sleep(500 * time.Millisecond)
}
```

#### 7.2. Channels (Canaux)

Les channels sont des conduits typés qui permettent aux goroutines de communiquer et de se synchroniser de manière sûre.
- `ch <- v` : Envoyer la valeur `v` dans le channel `ch`.
- `v := <-ch` : Recevoir une valeur de `ch` et l'assigner à `v`.

Les envois et réceptions sont bloquants par défaut.

```go
func main() {
    // Créer un channel de strings
    messages := make(chan string)

    // Goroutine qui envoie un message dans le channel
    go func() {
        time.Sleep(1 * time.Second)
        messages <- "ping"
    }()

    // La goroutine principale attend (bloque) de recevoir le message
    msg := <-messages
    fmt.Println(msg) // Affiche "ping" après 1 seconde
}
```

#### 7.3. `select`

Le `select` permet à une goroutine d'attendre sur plusieurs opérations de communication (channels). Il est similaire à un `switch` mais pour les channels.

```go
func main() {
    c1 := make(chan string)
    c2 := make(chan string)

    go func() {
        time.Sleep(1 * time.Second)
        c1 <- "un"
    }()
    go func() {
        time.Sleep(2 * time.Second)
        c2 <- "deux"
    }()

    // Attend le premier message qui arrive
    for i := 0; i < 2; i++ {
        select {
        case msg1 := <-c1:
            fmt.Println("Reçu", msg1)
        case msg2 := <-c2:
            fmt.Println("Reçu", msg2)
        }
    }
}
```

---

### 8. Gestion des Packages et Modules

- **Packages** : Un répertoire contenant un ou plusieurs fichiers Go avec la même déclaration `package`.
- **Modules Go** : Le système de gestion des dépendances. Un projet est un module.
  - `go.mod` : Fichier qui définit le module et ses dépendances.
  - `go get` : Commande pour ajouter/mettre à jour des dépendances.
  - `go mod tidy` : Nettoie les dépendances inutilisées.

Pour créer un nouveau module : `go mod init nom.du/module`

---

### 9. Outils Standards et Bibliothèques Intégrées

Go est fourni avec un outillage riche et une bibliothèque standard très complète.

#### 9.1. Outils de la Ligne de Commande

- `go run` : Compile et exécute un programme.
- `go build` : Compile le programme en un binaire exécutable.
- `go test` : Lance les tests du projet.
- `go fmt` : Formate le code source selon les conventions Go.
- `go vet` : Analyse le code pour détecter des erreurs suspectes.
- `go doc` : Affiche la documentation des packages.

#### 9.2. Bibliothèques Standard Clés

- `fmt` : Fonctions d'entrées/sorties formatées.
- `strings` : Fonctions pour manipuler des chaînes de caractères.
- `slices` : Fonctions pour manipuler des slices.
- `maps` : Fonctions pour manipuler des maps.
- `math` : Fonctions mathématiques.
- `strconv` : Fonctions pour convertir des chaînes de caractères en valeurs de type `int`, `float64`, etc.
- `errors` : Fonctions pour gérer les erreurs.
- `net/http` : Client et serveur HTTP.
- `io` et `io/ioutil` : Primitives d'E/S.
- `encoding/json` : Encodage et décodage JSON.
- `os` : Fonctions pour interagir avec le système d'exploitation.
- `sync` : Primitives de synchronisation (`Mutex`, `WaitGroup`, etc.).
- `time` : Fonctions pour la manipulation du temps.


---

### 10. Bonnes Pratiques et Conventions

- **Nommage** : Les identifiants (variables, fonctions, types) qui commencent par une majuscule sont **exportés** (publics). Ceux qui commencent par une minuscule sont privés au package.
- **Simplicité** : Préférer un code simple et lisible à une solution complexe. "Clear is better than clever."
- **Gestion des erreurs** : Toujours vérifier les erreurs retournées par les fonctions.
- **Interfaces courtes** : Préférer de petites interfaces ciblées (comme `io.Reader`) plutôt que de grosses interfaces monolithiques.
- **Composition sur héritage** : Utiliser l'intégration de structs (`embedding`) pour réutiliser du code.
- **Commentaires** : Écrire des commentaires clairs. `godoc` génère la documentation à partir de ces commentaires. Les commentaires de package sont importants.
- **`gofmt`** : Toujours formater son code avec `gofmt` pour une consistance stylistique à travers toute la communauté Go.
