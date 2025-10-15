---
title: Bash
draft: false
tags:
  - bash
  - shell
description: Interpréteur de commandes Unix, largement utilisé pour l'automatisation de tâches et la gestion du système.
---


# Note de Référence Complète sur le Shell Bash

## Introduction

Le **Bash (Bourne-Again SHell)** est un interpréteur de commandes et un langage de script puissant, omniprésent dans les systèmes d'exploitation de type Unix (Linux, macOS). Il permet d'automatiser des tâches, de gérer le système et de créer des outils complexes en combinant des commandes existantes. Cette note a pour but de couvrir tous les aspects essentiels pour maîtriser Bash.

---

### 1. Bases de la Syntaxe et Structure d'un Script

Un script Bash est un fichier texte contenant une série de commandes.

#### 1.1. Le Shebang
La première ligne d'un script Bash doit être le "shebang". Il indique au système d'exploitation quel interpréteur utiliser pour exécuter le fichier.

```bash
#!/bin/bash
```

#### 1.2. Commentaires
Les commentaires commencent par un `#`. Tout ce qui suit sur la même ligne est ignoré.

```bash
# Ceci est un commentaire. Il ne sera pas exécuté.
echo "Bonjour, Monde !" # Affiche un message.
```

#### 1.3. Exécution d'un Script
Pour exécuter un script, il doit avoir la permission d'exécution.

1.  **Rendre le script exécutable :**
    ```bash
    chmod +x mon_script.sh
    ```
2.  **Exécuter le script :**
    ```bash
    ./mon_script.sh
    ```

#### 1.4. Structure de base d'un script

```bash
#!/bin/bash

# =========================================================
# Description : Script d'exemple qui salue un utilisateur.
# Auteur      : Votre Nom
# Date        : 2023-10-27
# Usage       : ./mon_script.sh [nom]
# =========================================================

# Déclaration d'une variable
NOM_UTILISATEUR=${1:-"Invité"} # Utilise le premier argument, ou "Invité" par défaut

# Corps principal du script
echo "Bonjour, ${NOM_UTILISATEUR} !"
echo "Le script s'est exécuté avec succès."

# Fin du script avec un code de sortie explicite (0 = succès)
exit 0
```

---

### 2. Variables, Types et Manipulation de Chaînes

#### 2.1. Déclaration et Affectation
Il n'y a pas d'espaces autour du signe `=`. Par convention, les noms de variables globales/constantes sont en majuscules.

```bash
NOM="Alice"
AGE=30
FICHIER_LOG="/var/log/system.log"
```

#### 2.2. Utilisation des Variables
Utilisez le signe `$` pour accéder à la valeur d'une variable. Les accolades `${}` sont plus robustes et évitent les ambiguïtés.

```bash
echo "Utilisateur : $NOM"
echo "L'utilisateur est ${NOM}." # Préférable
```

#### 2.3. Variables Spéciales (Internes)
Bash fournit des variables spéciales très utiles :

| Variable | Description                                                     |
| :------- | :-------------------------------------------------------------- |
| `$0`     | Le nom du script.                                               |
| `$1`, `$2`, ... | Les arguments passés au script.                                 |
| `$#`     | Le nombre d'arguments passés au script.                         |
| `$@`     | Tous les arguments, sous forme de mots séparés.                 |
| `$*`     | Tous les arguments, sous forme d'une seule chaîne.              |
| `$?`     | Le code de retour de la dernière commande exécutée (0 = succès). |
| `$$`     | Le PID (Process ID) du script en cours.                         |
| `$!`     | Le PID du dernier processus lancé en arrière-plan.              |

**Exemple :**
```bash
#!/bin/bash
echo "Nom du script : $0"
echo "Nombre d'arguments : $#"
echo "Premier argument : $1"
echo "Tous les arguments : $@"
```

#### 2.4. Tableaux (Arrays)
Bash supporte les tableaux indexés.

```bash
# Déclaration
serveurs=("web-prod-01" "db-prod-01" "api-prod-01")

# Accéder à un élément (l'indexation commence à 0)
echo "Serveur web : ${serveurs[0]}"

# Accéder à tous les éléments
echo "Tous les serveurs : ${serveurs[@]}"

# Ajouter un élément
serveurs+=("backup-01")

# Nombre d'éléments
echo "Nombre de serveurs : ${#serveurs[@]}"
```

#### 2.5. Manipulation de Chaînes de Caractères
Bash offre des outils puissants pour manipuler les chaînes sans appeler d'outils externes.

```bash
chaine="Le grand renard brun saute."

# Longueur
echo "Longueur : ${#chaine}" # -> 28

# Extraction (substring)
echo "Extraction : ${chaine:10:6}" # -> renard (à partir de l'index 10, sur 6 caractères)

# Remplacement (la première occurrence)
echo "Remplacement : ${chaine/brun/roux}" # -> Le grand renard roux saute.

# Remplacement (toutes les occurrences)
phrase="un un deux trois"
echo "Remplacement global : ${phrase//un/zéro}" # -> zéro zéro deux trois

# Supprimer un préfixe (non-greedy)
fichier="photo.jpg"
echo "Sans préfixe : ${fichier#*.}" # -> jpg

# Supprimer un préfixe (greedy)
chemin="/home/user/img/photo.jpg"
echo "Sans préfixe : ${chemin##*/}" # -> photo.jpg

# Supprimer un suffixe (non-greedy)
echo "Sans suffixe : ${fichier%.*}" # -> photo
```

---

### 3. Conditions, Boucles et Structures de Contrôle

#### 3.1. Structure `if`, `elif`, `else`
La syntaxe utilise `if`, `then`, `elif`, `else` et se termine par `fi`.

```bash
NB_FICHIERS=$(ls | wc -l)

if [[ $NB_FICHIERS -gt 10 ]]; then
  echo "Il y a beaucoup de fichiers."
elif [[ $NB_FICHIERS -eq 0 ]]; then
  echo "Le répertoire est vide."
else
  echo "Il y a quelques fichiers."
fi
```

**Note :** Préférez `[[ ... ]]` à `[ ... ]` ou `test`. C'est une version améliorée qui évite de nombreux pièges (ex: gestion des espaces) et offre plus de fonctionnalités (expressions régulières, `&&`, `||`).

#### 3.2. Opérateurs de test courants dans `[[ ... ]]`

| Type       | Opérateur | Description                               |
| :--------- | :-------- | :---------------------------------------- |
| **Fichiers** | `-e file` | Vrai si le fichier existe.                |
|            | `-f file` | Vrai si c'est un fichier régulier.        |
|            | `-d dir`  | Vrai si c'est un répertoire.              |
|            | `-s file` | Vrai si le fichier n'est pas vide.        |
|            | `-r file` | Vrai si le fichier est lisible.           |
|            | `-w file` | Vrai si le fichier est modifiable.        |
|            | `-x file` | Vrai si le fichier est exécutable.        |
| **Chaînes**  | `-z str`  | Vrai si la chaîne est vide.               |
|            | `-n str`  | Vrai si la chaîne n'est pas vide.         |
|            | `s1 == s2`| Vrai si les chaînes sont égales.          |
|            | `s1 != s2`| Vrai si les chaînes sont différentes.     |
| **Nombres**  | `n1 -eq n2` | Égal (`equal`)                            |
|            | `n1 -ne n2` | Non égal (`not equal`)                    |
|            | `n1 -gt n2` | Plus grand que (`greater than`)           |
|            | `n1 -ge n2` | Plus grand ou égal (`greater or equal`)   |
|            | `n1 -lt n2` | Plus petit que (`less than`)              |
|            | `n1 -le n2` | Plus petit ou égal (`less or equal`)      |
| **Logiques** | `&&`      | ET logique                                |
|            | `||`      | OU logique                                |

#### 3.3. Structure `case`
Utile pour des tests multiples sur une seule variable.

```bash
read -p "Entrez une action (start/stop/restart) : " action

case "$action" in
  start)
    echo "Démarrage du service..."
    ;;
  stop)
    echo "Arrêt du service..."
    ;;
  restart)
    echo "Redémarrage du service..."
    ;;
  *)
    echo "Action non reconnue : $action"
    exit 1
    ;;
esac
```

#### 3.4. Boucle `for`
Itère sur une liste d'éléments.

```bash
# Itérer sur une liste
for fruit in pomme banane orange; do
  echo "J'aime les ${fruit}s."
done

# Style C
for (( i=0; i<5; i++ )); do
  echo "Compteur : $i"
done

# Itérer sur les fichiers d'un répertoire
for fichier in /var/log/*.log; do
  if [[ -f "$fichier" ]]; then
    echo "Analyse du fichier : $fichier"
  fi
done
```

#### 3.5. Boucle `while`
S'exécute tant qu'une condition est vraie. Très courant pour lire un fichier ligne par ligne.

```bash
compteur=0
while [[ $compteur -lt 5 ]]; do
  echo "Le compteur est à $compteur"
  ((compteur++))
done

# Lire un fichier ligne par ligne
while IFS= read -r ligne; do
  echo "Ligne lue : $ligne"
done < "mon_fichier.txt"
```

#### 3.6. Boucle `until`
S'exécute jusqu'à ce qu'une condition devienne vraie (l'inverse de `while`).

```bash
# Attendre qu'un service soit prêt
until ping -c 1 mon-serveur.com &>/dev/null; do
  echo "Le serveur est indisponible, nouvelle tentative dans 5s..."
  sleep 5
done
echo "Le serveur est de nouveau en ligne !"
```

---

### 4. Fonctions et Paramètres

#### 4.1. Déclaration et Appel
Les fonctions permettent de regrouper du code réutilisable.

```bash
# Déclaration
saluer() {
  local nom=$1 # Premier argument, variable locale
  echo "Bonjour, $nom !"
}

# Appel
saluer "Alice" # -> Bonjour, Alice !
saluer "Bob"   # -> Bonjour, Bob !
```

#### 4.2. Paramètres et Retour de Valeur
Les fonctions accèdent à leurs arguments comme le script (`$1`, `$@`, etc.).

*   **Code de retour (`return`)** : Une fonction retourne un code de statut (un entier entre 0 et 255). `0` signifie succès.
*   **Retourner des données** : Pour retourner une chaîne de caractères ou d'autres données, la fonction doit l'écrire sur sa sortie standard (`echo` ou `printf`), et l'appelant la capture avec une substitution de commande `$(...)`.

```bash
# Fonction qui retourne une valeur via la sortie standard
get_date() {
  date "+%Y-%m-%d %H:%M:%S"
}

# Fonction qui retourne un code de statut
fichier_existe() {
  if [[ -f "$1" ]]; then
    return 0 # Succès
  else
    return 1 # Échec
  fi
}

# Utilisation
date_actuelle=$(get_date)
echo "Date capturée : $date_actuelle"

if fichier_existe "/etc/hosts"; then
  echo "Le fichier /etc/hosts existe."
else
  echo "Le fichier /etc/hosts n'existe pas."
fi
```

#### 4.3. Portée des variables (`local`)
Par défaut, les variables sont globales. Utilisez `local` pour limiter leur portée à la fonction. C'est une bonne pratique essentielle.

---

### 5. Gestion des Entrées/Sorties (E/S) et Redirections

Les processus Unix ont trois flux de données standards :

*   `stdin` (0) : Entrée standard (généralement le clavier).
*   `stdout` (1) : Sortie standard (généralement l'écran).
*   `stderr` (2) : Sortie d'erreur standard (généralement l'écran).

| Syntaxe          | Description                                                    |
| :--------------- | :------------------------------------------------------------- |
| `cmd > fichier`    | Redirige `stdout` vers `fichier` (écrase le contenu).        |
| `cmd >> fichier`   | Redirige `stdout` vers `fichier` (ajoute à la fin).          |
| `cmd < fichier`    | Utilise `fichier` comme `stdin` pour `cmd`.                    |
| `cmd 2> fichier`   | Redirige `stderr` vers `fichier`.                            |
| `cmd &> fichier`   | Redirige `stdout` et `stderr` vers `fichier` (syntaxe Bash). |
| `cmd > f 2>&1`   | Idem, syntaxe plus portable.                                 |
| `cmd1 | cmd2`     | **Pipe** : Redirige `stdout` de `cmd1` vers `stdin` de `cmd2`. |
| `cmd <<< "texte"`  | **Here String** : Passe "texte" comme `stdin`.                 |
| `<<EOF`          | **Here Document** : Permet de passer un bloc de texte multi-lignes. |

**Exemples :**
```bash
# Lister les fichiers et enregistrer dans un fichier
ls -l > liste_fichiers.txt

# Enregistrer les logs et les erreurs dans le même fichier
./mon_script.sh &> script.log

# Compter le nombre de processus "nginx"
ps aux | grep nginx | wc -l

# Utiliser un Here Document pour un script SQL
sqlplus user/pass@db <<EOF
  SELECT * FROM employees WHERE department = 'IT';
  EXIT;
EOF
```

---

### 6. Expressions Régulières et Traitement de Texte

#### 6.1. `grep` : Filtrer du texte
Recherche des motifs dans un texte.

```bash
# Chercher une erreur dans un fichier de log (insensible à la casse)
grep -i "error" /var/log/syslog

# Afficher les lignes qui ne contiennent PAS "DEBUG"
grep -v "DEBUG" app.log

# Compter le nombre d'occurrences
grep -c "connected" access.log

# Utiliser les expressions régulières étendues (-E) pour trouver des adresses IP
grep -E "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b" access.log
```

#### 6.2. `sed` : Éditeur de flux
Modifie le texte à la volée.

```bash
# Remplacer la première occurrence de "old" par "new" sur chaque ligne
sed 's/old/new/' fichier.txt

# Remplacer toutes les occurrences (avec le flag 'g' pour global)
sed 's/old/new/g' fichier.txt

# Supprimer les lignes contenant "pattern"
sed '/pattern/d' fichier.txt

# Modifier le fichier sur place (avec précaution !)
sed -i.bak 's/localhost/127.0.0.1/g' config.ini
```

#### 6.3. `awk` : Traitement de texte par colonnes
Un langage de programmation puissant pour manipuler des données structurées.

```bash
# Afficher la première et la troisième colonne d'un fichier, séparées par des espaces
ls -l | awk '{print $1, $9}'

# Calculer l'espace disque total utilisé (colonne 5)
df -h | awk 'NR>1 {sum+=$5} END {print sum "G"}'
```

#### 6.4. Expressions régulières dans Bash `[[ ... ]]`
L'opérateur `=~` permet d'utiliser des expressions régulières directement dans une condition.

```bash
email="test@example.com"
regex="^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$"

if [[ $email =~ $regex ]]; then
  echo "Adresse e-mail valide."
else
  echo "Adresse e-mail invalide."
fi
```

---

### 7. Commandes Essentielles et Utilitaires Courants

| Commande      | Description                                                    |
| :------------ | :------------------------------------------------------------- |
| `ls`, `cd`, `pwd` | Navigation dans les répertoires.                             |
| `cp`, `mv`, `rm`  | Copier, déplacer, supprimer des fichiers/répertoires.        |
| `mkdir`, `rmdir`  | Créer, supprimer des répertoires.                            |
| `touch`, `cat`    | Créer un fichier vide, afficher le contenu d'un fichier.     |
| `head`, `tail`    | Afficher le début/la fin d'un fichier (`tail -f` pour suivre). |
| `find`          | Rechercher des fichiers/répertoires (`find . -name "*.log"`).  |
| `chmod`, `chown`  | Changer les permissions, changer le propriétaire.            |
| `ps`, `kill`      | Lister les processus, envoyer un signal à un processus.      |
| `df`, `du`        | Afficher l'espace disque (système, répertoire).              |
| `curl`, `wget`    | Télécharger des fichiers depuis le réseau.                   |
| `tar`, `gzip`     | Archiver et compresser des fichiers.                         |
| `ssh`           | Se connecter à distance de manière sécurisée.                |
| `date`          | Afficher/formater la date et l'heure.                        |

---

### 8. Gestion des Processus et des Tâches

#### 8.1. Tâches en arrière-plan
Ajoutez `&` à la fin d'une commande pour la lancer en arrière-plan.

```bash
sleep 60 &
echo "La commande 'sleep' s'exécute en arrière-plan avec le PID $!"
```

#### 8.2. `jobs`, `fg`, `bg`
- `jobs` : Liste les tâches en arrière-plan.
- `fg %N` : Ramène la tâche N au premier plan.
- `bg %N` : Fait continuer une tâche stoppée en arrière-plan.

#### 8.3. `kill` et les Signaux
`kill` envoie un signal à un processus.

- `kill PID` : Envoie le signal `SIGTERM` (15), une demande polie d'arrêt.
- `kill -9 PID` : Envoie le signal `SIGKILL` (9), une terminaison forcée.
- `kill -1 PID` ou `kill -SIGHUP PID` : Envoie `SIGHUP`, souvent utilisé pour relire un fichier de configuration.

#### 8.4. `trap` : Intercepter les signaux
`trap` permet d'exécuter une commande lorsqu'un signal est reçu par le script. Très utile pour le nettoyage (suppression de fichiers temporaires).

```bash
#!/bin/bash

# Fichier temporaire
FICHIER_TEMP=$(mktemp)

# Fonction de nettoyage
cleanup() {
  echo "Nettoyage en cours..."
  rm -f "$FICHIER_TEMP"
  echo "Fichiers temporaires supprimés."
}

# Intercepter les signaux de sortie, d'interruption (Ctrl+C) et de terminaison
trap cleanup EXIT SIGINT SIGTERM

echo "Script en cours... Fichier temporaire créé : $FICHIER_TEMP"
echo "Faites Ctrl+C pour tester le trap."
sleep 60
```

---

### 9. Gestion des Erreurs et Débogage

#### 9.1. Code de Retour (`$?`)
Vérifiez toujours le code de retour des commandes critiques.

```bash
cp source.txt dest.txt
if [[ $? -ne 0 ]]; then
  echo "Erreur lors de la copie du fichier !"
  exit 1
fi
```

#### 9.2. Options `set` pour des scripts robustes
Placez cette ligne au début de vos scripts pour les rendre plus sûrs :

```bash
set -euo pipefail
```
- `set -e` : Le script s'arrête immédiatement si une commande échoue.
- `set -u` : Le script s'arrête si une variable non définie est utilisée.
- `set -o pipefail` : Le code de retour d'un pipeline est celui de la dernière commande du pipeline à avoir échoué (au lieu de celui de la toute dernière commande).

#### 9.3. Débogage
- **Trace d'exécution (`-x`)** : Affiche chaque commande avant de l'exécuter.
  ```bash
  bash -x mon_script.sh
  ```
  Ou activez-le à l'intérieur du script :
  ```bash
  set -x # Activer le mode débogage
  # ... code à déboguer ...
  set +x # Désactiver
  ```
- **Vérification de syntaxe (`-n`)** : Lit le script sans l'exécuter, utile pour trouver des erreurs de syntaxe.
  ```bash
  bash -n mon_script.sh
  ```

---

### 10. Bonnes Pratiques

1.  **Toujours utiliser le shebang** `#!/bin/bash`.
2.  **Utiliser `set -euo pipefail`** au début de vos scripts.
3.  **Mettre les variables entre guillemets doubles (`"$variable"`)** pour éviter les problèmes avec les espaces et les caractères spéciaux.
4.  **Utiliser `$(...)` pour la substitution de commande** au lieu des backticks `` (`...`) car c'est plus lisible et peut être imbriqué.
5.  **Préférer `[[ ... ]]` à `[ ... ]`** pour les tests conditionnels.
6.  **Utiliser `local` pour les variables dans les fonctions** pour éviter de polluer l'espace de noms global.
7.  **Ajouter des commentaires** pour expliquer les parties complexes de votre code.
8.  **Écrire des fonctions** pour le code réutilisable.
9.  **Vérifier les codes de retour** des commandes importantes.
10. **Créer une fonction `usage()`** pour expliquer comment utiliser votre script.

---
