# Bash : Mes premiers scripts - Fiche de révision enrichie

> Support de révision pour préparation à l'oral - Quête "Bash : Mes premiers scripts"

---

## 1. Résumé enrichi du cours

### 1.1 Qu'est-ce que Bash ?

Bash (**Bourne Again Shell**) est développé par la **Free Software Foundation (FSF)** dans le cadre du **GNU Project**. C'est :
- le **shell par défaut** de la plupart des distributions GNU/Linux ;
- à la fois un **interpréteur de commandes** ET un **langage de programmation** ;
- exécutable via différents terminaux (GNOME Terminal, iTerm2 sur macOS) et même sous Windows via **WSL** (Sous-système Windows pour Linux).

Le **prompt** (`$` en général) indique que le shell attend une commande.

**À retenir pour l'oral** : Bash sert d'interface entre l'utilisateur et le système — il exécute d'autres programmes installés sur le système.

### 1.2 Le scripting Bash

Un script Bash regroupe dans un fichier texte des commandes qui seraient normalement tapées manuellement. Intérêt principal : **l'automatisation** de tâches répétitives, gain de temps considérable pour les administrateurs systèmes.

### 1.3 Les variables

- Une variable est un **pointeur** vers un emplacement mémoire.
- **Portée locale** : une variable définie dans un script/terminal n'existe que dans ce contexte.
- **Affectation** : `nom_variable=valeur` (⚠️ pas d'espace autour du `=`)
- **Lecture** : préfixer par `$` → `echo $nom_variable`
- **Non typées** : une variable peut contenir un entier, une chaîne, le résultat d'une commande, ou la valeur d'une autre variable.
- **Saisie utilisateur** avec `read` :
  ```bash
  read -p "Votre âge : " age_user && echo "Vous avez $age_user ans"
  ```

**Point clé à l'oral** : contrairement à des langages typés (C, Java...), Bash ne type pas les variables — tout est traité comme une chaîne de caractères par défaut.

### 1.4 Les opérateurs de comparaison

**Entiers** (dans `[ ]`) :

| Opérateur | Signification |
|---|---|
| `-eq` | égal à |
| `-ne` | différent de |
| `-gt` | plus grand que |
| `-lt` | plus petit que |
| `-ge` | plus grand ou égal |
| `-le` | plus petit ou égal |

**Entiers** (dans `(( ))`, syntaxe arithmétique) : `<`, `<=`, `>`, `>=`

**Chaînes de caractères** :

| Opérateur | Signification |
|---|---|
| `=` ou `==` | égal à |
| `!=` | différent de |
| `<` / `>` | ordre alphabétique ASCII |
| `-n` | chaîne non vide |
| `-z` | chaîne vide |

⚠️ **Piège classique à l'oral** : ne pas confondre les opérateurs numériques (`-eq`, `-lt`...) et les opérateurs de chaînes (`=`, `!=`). `if [ 10 = 9 ]` est syntaxiquement valide mais compare des **chaînes**, pas des nombres.

### 1.5 Les conditions

- Structure : `if ... then ... else ... fi` (pas d'indentation obligatoire, pas d'accolades).
- Le `if` teste le **code de retour** de la commande (0 = succès).
- Commande `test` = équivalent de `[ ]` → `[ -f fichier ]` et `test -f fichier` sont strictement identiques.
- **Tests sur fichiers** : `-f` (fichier existe), `-d` (répertoire existe), etc.
- **Tests sur chaînes** : `-z` (vide), `=` (égalité)
- **Tests arithmétiques** : `-lt`, `-eq`, etc.

### 1.6 Les opérateurs logiques

- **ET logique** : `&&` → les deux conditions doivent être vraies
  ```bash
  if [ $prenom = "john" ] && [ $nom = "doe" ]
  ```
- **OU logique** : `||` → au moins une condition doit être vraie
  ```bash
  if [ $jour = "samedi" ] || [ $jour = "dimanche" ]
  ```

### 1.7 Les boucles

**Boucle `for`** — nombre d'itérations **connu à l'avance**, parcourt une liste :
```bash
for utilisateur in $liste_utilisateur
    do echo "Bonjour " $utilisateur
done
```

**Boucle `while`** — nombre d'itérations **indéterminé**, s'exécute tant qu'une condition est vraie :
```bash
while [ $i -lt 10 ]
do echo "La valeur de i =" $i
    let i=$i+1
done
```

**À retenir pour l'oral** : `for` = itération sur une collection connue ; `while` = itération conditionnelle, potentiellement infinie si la condition ne devient jamais fausse (risque de boucle infinie à mentionner).

---

## 2. Points capitaux non abordés dans ce cours (à connaître pour aller plus loin)

Ces notions complètent naturellement ce cours et sont très souvent demandées à l'oral TSSR, car elles sont indispensables dans la pratique quotidienne d'un admin sys/réseau.

### 2.1 Le shebang et l'exécution d'un script
```bash
#!/bin/bash
```
Indique l'interpréteur à utiliser. Pour rendre un script exécutable :
```bash
chmod +x mon_script.sh
./mon_script.sh
```
Alternative sans droits d'exécution : `bash mon_script.sh`

### 2.2 Les codes de retour (`$?`)
Chaque commande retourne un code de sortie (0 = succès, ≠0 = échec), accessible via `$?`. C'est ce que teste réellement un `if`.
```bash
ls /chemin/inexistant
echo $?   # affichera un code différent de 0
```

### 2.3 Les arguments d'un script
| Variable | Signification |
|---|---|
| `$0` | nom du script |
| `$1`, `$2`... | arguments positionnels |
| `$#` | nombre d'arguments |
| `$@` | tous les arguments (liste) |
| `$*` | tous les arguments (chaîne unique) |

### 2.4 La structure `case`
Alternative au `if/elif` multiple, très utilisée pour les menus (comme l'exercice de la machine à café) :
```bash
case $choix in
    café) echo "Un café servi" ;;
    thé) echo "Un thé servi" ;;
    x) echo "Sortie" ;;
    *) echo "Choix invalide" ;;
esac
```

### 2.5 La boucle `until`
Inverse du `while` : s'exécute **jusqu'à ce que** la condition devienne vraie.
```bash
until [ $i -ge 10 ]
do echo $i; let i=$i+1
done
```

### 2.6 Les fonctions
Permettent de factoriser du code :
```bash
ma_fonction() {
    echo "Bonjour $1"
}
ma_fonction "Jean"
```

### 2.7 Les tableaux (arrays)
```bash
mon_tableau=("Jean" "Paul" "Pierre")
echo ${mon_tableau[0]}       # Jean
echo ${mon_tableau[@]}       # tous les éléments
echo ${#mon_tableau[@]}      # nombre d'éléments
```

### 2.8 Les redirections et pipes
| Symbole | Rôle |
|---|---|
| `>` | redirige la sortie standard (écrase) |
| `>>` | redirige en ajoutant (append) |
| `2>` | redirige les erreurs |
| `&>` | redirige sortie + erreurs |
| `\|` | pipe : envoie la sortie d'une commande en entrée d'une autre |

### 2.9 Débogage d'un script
```bash
bash -x mon_script.sh    # exécution pas à pas, affiche chaque commande
set -e                   # arrête le script à la première erreur
set -x                   # active le mode debug dans le script
```

### 2.10 L'automatisation via `cron`
Un script n'a d'intérêt en administration système que s'il peut être **planifié**. Lien direct avec `crontab -e` pour exécuter un script à intervalle régulier — compétence clé pour un TSSR.

### 2.11 Bonnes pratiques
- Toujours **quoter** ses variables : `"$variable"` plutôt que `$variable` (évite les problèmes avec les espaces).
- Utiliser `shellcheck` pour linter ses scripts.
- Commenter avec `#`.
- Préférer `[[ ]]` (test amélioré Bash) à `[ ]` (POSIX) quand la portabilité n'est pas requise.

---

## 3. QCM d'entraînement

*(10 questions — une seule bonne réponse par question sauf mention contraire)*

**Q1.** Quel caractère faut-il utiliser pour récupérer la valeur d'une variable en Bash ?
A) `%variable`
B) `$variable`
C) `&variable`
D) `#variable`

**Q2.** Que fait la commande suivante ?
```bash
read -p "Nom : " nom
```
A) Elle affiche la variable `nom`
B) Elle stocke une saisie utilisateur dans la variable `nom` après avoir affiché "Nom : "
C) Elle crée un fichier nommé `nom`
D) Elle supprime la variable `nom`

**Q3.** Quel opérateur permet de tester si deux **entiers** sont égaux dans un `if [ ]` ?
A) `=`
B) `==`
C) `-eq`
D) `-ne`

**Q4.** Quel opérateur permet de tester si une chaîne de caractères est **vide** ?
A) `-n`
B) `-z`
C) `-eq`
D) `-f`

**Q5.** Quelle structure Bash correspond à l'opérateur logique **ET** ?
A) `||`
B) `&&`
C) `!`
D) `&`

**Q6.** Quelle boucle choisir pour parcourir une liste de valeurs dont le nombre est connu à l'avance ?
A) `while`
B) `until`
C) `for`
D) `case`

**Q7.** Que teste réellement l'instruction `if` en Bash ?
A) La valeur d'une variable booléenne
B) Le code de retour de la commande exécutée
C) Le contenu d'un fichier de log
D) La présence d'un utilisateur connecté

**Q8.** Quelle commande rend un script exécutable ?
A) `bash +x script.sh`
B) `chmod +x script.sh`
C) `exec script.sh`
D) `run script.sh`

**Q9.** Dans un script, que représente la variable `$1` ?
A) Le nom du script
B) Le code de retour
C) Le premier argument passé au script
D) Le nombre total d'arguments

**Q10.** (Plusieurs réponses possibles) Quels éléments sont vrais concernant les variables en Bash ?
A) Elles sont typées (entier, chaîne, etc.)
B) Elles ne sont pas typées
C) Il ne faut pas mettre d'espace autour du `=` lors de l'affectation
D) On peut stocker le résultat d'une saisie utilisateur avec `read`

---

## 4. Corrigé du QCM

| Question | Réponse(s) |
|---|---|
| Q1 | B |
| Q2 | B |
| Q3 | C |
| Q4 | B |
| Q5 | B |
| Q6 | C |
| Q7 | B |
| Q8 | B |
| Q9 | C |
| Q10 | B, C, D |

---

## 5. Questions types "oral" à s'entraîner à répondre à voix haute

1. Quelle est la différence entre `for` et `while` ? Donne un cas d'usage concret pour chacune.
2. Pourquoi dit-on que les variables Bash ne sont pas typées ? Quelle conséquence pratique cela a-t-il ?
3. Explique la différence entre `-eq` et `=`. Que se passe-t-il si on les confond ?
4. Comment un script sait-il si la commande précédente a réussi ou échoué ?
5. Pourquoi est-il recommandé de quoter ses variables (`"$var"`) dans un script ?
6. Comment automatiserais-tu l'exécution d'un script toutes les nuits à 2h du matin ?
