# 📘 Bash - Introduction - Fiche de révision

## 1. Contexte et définitions clés

| Terme | Définition |
|---|---|
| **Shell** | Programme interface utilisateur en ligne de commande (CLI) entre l'utilisateur et le système d'exploitation. Exemples : `bash`, `sh`, `zsh`, `ksh` |
| **Bash** | *Bourne-Again SHell* — créé en **1989** par **Brian Fox**, du projet **GNU**. Nom dérivé du **Bourne shell** (Steve Bourne, 1977, shell par défaut d'Unix v7) |
| **Script** | Fichier texte contenant une suite d'instructions exécutées dans l'ordre par l'interpréteur |

> 🎯 **Point clé examen** : Bash est **à la fois** un interpréteur de commandes **et** un langage de programmation. C'est LA phrase à retenir si on te demande de définir Bash.

---

## 2. Structure d'un script Bash

- Un script Bash est un **fichier texte** classique, donc soumis aux droits Unix (`owner`, `group`, `others`).
- Droit **`read`** nécessaire pour **lire** le contenu.
- Droit **`execute`** nécessaire pour **exécuter** le script.
- Extension `.sh` : **convention**, pas une obligation technique.

### Le Shebang (`#!`)

- Première ligne du script, indique **quel interpréteur** utiliser.
- Contraction de *sharp* (`#`) + *bang* (`!`).
- Le plus courant : `#!/bin/bash`
- ⚠️ Le shebang n'est **pas exclusif à Bash** : il fonctionne aussi avec Python, Perl, sed, awk, etc.

Exemples de shebangs valides :
```bash
#!/bin/bash
#!/usr/bin/env bash
#!/bin/sh -x
#!/usr/bin/python -O
```

---

## 3. Appeler / exécuter un script

Deux méthodes principales :

| Méthode | Commande | Condition requise |
|---|---|---|
| Appel explicite de l'interpréteur | `bash chemin/vers/script.sh` | Droit `read` suffit |
| Appel direct (via chemin) | `./script.sh` | Droit `execute` (`+x`) obligatoire |

> 🎯 **Piège d'examen classique** : `./script.sh` nécessite le bit d'exécution (`chmod +x`), alors que `bash script.sh` ne le nécessite pas puisque c'est bash lui-même qui lit et interprète le fichier.

---

## 4. Les variables

### Syntaxe
- **Affectation** : `nom_variable="valeur"` → **pas d'espace** autour du `=`
- **Lecture** : `$nom_variable` ou `echo $nom_variable`

```bash
#!/bin/bash
name="Wilder"
echo "Hello $name !"
```

### Saisie utilisateur : `read`
```bash
read myName
echo "Hello $myName !"
```
`read` met en pause le script et attend une saisie clavier, stockée dans la variable indiquée.

> 🎯 **Point clé** : une variable est un pointeur vers un emplacement mémoire — elle permet de manipuler un "nom" plutôt qu'une valeur en dur, ce qui rend le script réutilisable.

---

## 5. Paramètres et arguments

| Notion | Définition |
|---|---|
| **Paramètre** | Variable prédéfinie du script (ex : `$1`, `$2`...) |
| **Argument** | Valeur fournie par l'utilisateur au moment de l'appel |

- `$0` → nom du script lui-même
- `$1` à `$9` → arguments positionnels (1er, 2e... argument passé)

```bash
#!/bin/bash
echo "Hello $1 !"
```
Appel : `bash hello.sh Wilder` → affiche `Hello Wilder !`

### Convention POSIX / GNU
- **Options courtes** : commencent par `-` (ex : `-x`)
- **Options longues (GNU)** : commencent par `--` (ex : `--verbose`)

> 🎯 **Piège d'examen** : bien distinguer **argument obligatoire** (positionnel, `$1`) et **option facultative** (`-x` / `--option`).

---

## 6. Les commentaires

- Commencent par `#` (sauf la toute première ligne = shebang, qui commence aussi par `#` mais a un rôle spécial).
- Toute ligne commençant par `#` est **ignorée** par l'interpréteur.

```bash
#!/bin/bash
# $1 : nom de l'utilisateur à saluer
echo "Hello $1 !"
```

---

## 7. Variables d'environnement

- Définies par l'OS, accessibles depuis **tout** script/programme.
- Convention de nommage : **MAJUSCULES**.
- Consultation : `printenv` (toutes) ou `printenv PATH` (une seule).
- Lecture identique aux variables classiques : `$NOM_VARIABLE`.

### Variables importantes à connaître

| Variable | Rôle |
|---|---|
| `$PATH` | Liste des répertoires (séparés par `:`) où le shell cherche les exécutables |
| `$HOME` | Chemin du répertoire personnel de l'utilisateur |
| `$USERNAME` | Nom de l'utilisateur courant |

### Modifier le PATH

```bash
PATH=$PATH:/home/wilder/bash-scripts/
```
⚠️ **Modification temporaire**, valable uniquement dans le shell courant (chaque shell a son propre environnement).

### Rendre la modification permanente
Ajouter dans `~/.bashrc` avec `export` :
```bash
export PATH="$HOME/scripts:$PATH"
```
`export` rend la variable disponible dans les **sous-shells**.

> 🎯 **Point clé examen** : différence entre variable **locale au shell** (perdue à la fermeture) et variable **exportée** (transmise aux sous-processus), et différence entre modification **temporaire** (ligne de commande) et **permanente** (`~/.bashrc`).

---

## 8. Cas d'usage pratique : automatisation

Toute commande tapée en CLI est potentiellement une ligne de script → base de l'**automatisation**.

```bash
#!/bin/bash
# Script to start all everyday tools at once
firefox
keepassxc
virtualbox
```

---

## 🧠 Synthèse ultra-condensée (à relire juste avant l'examen)

1. Bash = interpréteur de commandes **+** langage de programmation.
2. Script = fichier texte, besoin de `read` pour lire, `execute` pour lancer en `./`.
3. Shebang `#!/bin/bash` = 1re ligne, définit l'interpréteur, non exclusif à Bash.
4. Variable : `var="valeur"` (pas d'espace), lecture `$var`.
5. `read var` → saisie utilisateur interactive.
6. `$0` = nom du script, `$1`...`$9` = arguments positionnels.
7. Options POSIX : `-x` (courte) / GNU : `--option` (longue).
8. Commentaire = ligne commençant par `#`.
9. Variables d'environnement en MAJUSCULES, `printenv` pour les lister.
10. `$PATH` : chemins de recherche des exécutables ; `export` = rend une variable dispo pour les sous-shells ; `~/.bashrc` = persistance.

---

# 📝 QCM d'entraînement — Bash Introduction

*(1 seule bonne réponse par question sauf mention contraire)*

**Question 1.** Qui a créé Bash, et en quelle année ?
A) Steve Bourne, 1977
B) Brian Fox, 1989
C) Linus Torvalds, 1991
D) Richard Stallman, 1983

**Question 2.** Que signifie l'acronyme Bash ?
A) Basic Shell
B) Bourne-Again Shell
C) Binary Access Shell
D) Best Automated Shell

**Question 3.** Quel droit Unix est nécessaire pour exécuter un script via `./script.sh` ?
A) `read`
B) `write`
C) `execute`
D) Aucun droit particulier

**Question 4.** Quelle commande permet d'exécuter un script sans avoir le bit d'exécution activé ?
A) `./script.sh`
B) `exec script.sh`
C) `bash script.sh`
D) `chmod script.sh`

**Question 5.** Qu'est-ce que le "shebang" ?
A) Un commentaire spécial en fin de fichier
B) La première ligne d'un script qui définit l'interpréteur à utiliser
C) Une variable d'environnement
D) Un raccourci clavier du terminal

**Question 6.** (Plusieurs réponses possibles) Parmi les shebangs suivants, lesquels sont syntaxiquement valides ?
A) `#!/bin/bash`
B) `!#/bin/bash`
C) `#!/usr/bin/env bash`
D) `#!/usr/bin/python -O`

**Question 7.** Comment affecte-t-on la valeur "Paris" à une variable `ville` en Bash ?
A) `ville = "Paris"`
B) `ville="Paris"`
C) `$ville = "Paris"`
D) `var ville = "Paris"`

**Question 8.** Comment affiche-t-on le contenu de la variable `ville` ?
A) `echo ville`
B) `echo $ville`
C) `print(ville)`
D) `display ville`

**Question 9.** Quelle instruction permet de demander une saisie clavier à l'utilisateur et de la stocker dans une variable `nom` ?
A) `input nom`
B) `scan nom`
C) `read nom`
D) `get nom`

**Question 10.** Dans un script appelé ainsi : `./deploy.sh production --verbose`, que contient `$1` ?
A) `deploy.sh`
B) `production`
C) `--verbose`
D) Rien, `$1` n'existe qu'avec `read`

**Question 11.** Que contient la variable `$0` ?
A) Le premier argument passé au script
B) Le code de retour de la dernière commande
C) Le nom du script lui-même
D) Le PID du script

**Question 12.** Selon la convention POSIX/GNU, à quoi correspond une option comme `--verbose` ?
A) Une option courte obligatoire
B) Une option longue (format GNU)
C) Un argument positionnel
D) Une variable d'environnement

**Question 13.** Comment écrit-on un commentaire en Bash ?
A) `// commentaire`
B) `<!-- commentaire -->`
C) `# commentaire`
D) `/* commentaire */`

**Question 14.** Quelle commande permet de lister toutes les variables d'environnement ?
A) `env list`
B) `printenv`
C) `showvars`
D) `echo $ENV`

**Question 15.** À quoi sert la variable d'environnement `$PATH` ?
A) Elle contient le chemin du répertoire personnel
B) Elle liste les répertoires où le shell recherche les commandes exécutables
C) Elle stocke le nom d'utilisateur courant
D) Elle indique la version de Bash installée

**Question 16.** Si on modifie `$PATH` directement dans le terminal (sans `export` ni fichier de config), cette modification est :
A) Permanente pour tous les utilisateurs
B) Permanente uniquement pour l'utilisateur courant
C) Temporaire, valable uniquement dans le shell courant
D) Appliquée automatiquement à tous les scripts déjà en cours d'exécution

**Question 17.** Où doit-on ajouter une modification de variable d'environnement pour qu'elle soit permanente à chaque ouverture de shell ?
A) Dans `/etc/hosts`
B) Dans `~/.bashrc`
C) Dans le script lui-même uniquement
D) Dans `/var/log/bash.log`

**Question 18.** Quel mot-clé permet de rendre une variable disponible dans les sous-shells ?
A) `global`
B) `share`
C) `export`
D) `public`

**Question 19.** Quelle variable d'environnement contient le chemin du répertoire personnel de l'utilisateur ?
A) `$USER`
B) `$ROOT`
C) `$HOME`
D) `$PATH`

**Question 20.** Vrai ou Faux : le shebang `#!/bin/bash` ne fonctionne qu'avec des scripts Bash, jamais avec d'autres langages.
A) Vrai
B) Faux

---

## ✅ Corrigé

| Q | Réponse | Justification rapide |
|---|---|---|
| 1 | B | Brian Fox, 1989, projet GNU |
| 2 | B | Bourne-Again Shell |
| 3 | C | `execute` obligatoire pour `./script.sh` |
| 4 | C | `bash script.sh` lit le fichier via l'interpréteur, sans besoin du bit `+x` |
| 5 | B | 1re ligne définissant l'interpréteur |
| 6 | A, C, D | B est invalide (mauvais ordre des caractères) |
| 7 | B | Pas d'espace autour du `=` |
| 8 | B | `$` précède le nom pour lire la valeur |
| 9 | C | `read` est l'instruction Bash dédiée à la saisie |
| 10 | B | `$1` = premier argument = `production` |
| 11 | C | `$0` = nom du script |
| 12 | B | `--` = option longue façon GNU |
| 13 | C | `#` en début de ligne = commentaire |
| 14 | B | `printenv` |
| 15 | B | Chemins de recherche des exécutables |
| 16 | C | Chaque shell a son propre environnement, modification non persistée |
| 17 | B | `~/.bashrc` + `export` pour la persistance |
| 18 | C | `export` |
| 19 | C | `$HOME` |
| 20 | B (Faux) | Le shebang fonctionne aussi avec Python, Perl, sed, awk, etc. |

---
