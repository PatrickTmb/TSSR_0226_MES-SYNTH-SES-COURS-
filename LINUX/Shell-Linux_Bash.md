# Révision — Le Shell Linux / Bash

## 1. Définitions essentielles à connaître par cœur (piège classique à l'oral)

| Terme | Définition | Exemple |
|---|---|---|
| **Console** | Dispositif d'E/S **physique**, souvent le terminal primaire directement connecté à la machine | Écran + clavier branché en direct sur un serveur |
| **Terminal (TTY)** | Environnement d'E/S qui **émule** une console dans une interface graphique | gnome-terminal, Xterm, Tilix, MobaXterm, Terminal Windows |
| **Shell** | **Interpréteur** de lignes de commandes qui fournit des commandes pour interagir avec l'OS | bash, zsh, PowerShell, cmd |
| **Ligne de commande** | Instructions textuelles écrites dans un terminal, interprétées par un shell | `ls -la` |

⚠️ **Piège fréquent à l'oral** : confondre "terminal" et "shell". Le terminal est le **contenant** (fenêtre/émulateur), le shell est le **programme interprète** qui tourne dedans. On peut changer de terminal (Tilix, gnome-terminal) sans changer de shell (bash reste bash).

### Équivalences Windows / Linux à retenir
- Windows → Terminal Windows / PowerShell (GUI), ConHost (processus hôte console), shells : PowerShell, cmd
- Linux → gnome-terminal, Xterm, Konsole (GUI), terminaux virtuels `/dev/tty1`, `/dev/tty2`... (console), shells : sh, ksh, csh, tcsh, bash, zsh

## 2. Familles de commandes shell à citer en exemple à l'oral

- **Système de fichiers** : `cd`, `ls`, `pwd`
- **Manipulation de fichiers** : `cp`, `rm`, `mkdir`
- **Contenu de fichiers** : `cat`, `more`, `less`
- **Compression** : `tar`, `gzip`
- **Recherche** : `grep`, `find`
- **Droits** : `chmod`, `chown`
- **Montage** : `mount`, `umount`

💡 Outils d'aide à mentionner : `man <commande>` (manuel intégré) et le site **ExplainShell** (décomposition visuelle d'une commande).

⚠️ **Sécurité** : `chmod` et `rm` sont dangereuses car elles peuvent casser des permissions ou supprimer des données de façon irréversible (`rm` n'a pas de corbeille). Toujours tester en VM ou en environnement isolé.

## 3. Personnalisation du terminal — points clés

### Changer de terminal
- Raccourci standard : `Ctrl + Alt + T`
- On peut **installer plusieurs terminaux en parallèle** sur un système Unix (ex. Tilix en plus de gnome-terminal)
- Point important à l'oral : installer un nouveau terminal (ex. Tilix) **ne le rend pas terminal par défaut** automatiquement. Le raccourci clavier système continue de lancer l'ancien (gnome-terminal), sauf reconfiguration explicite.

### `eza` — alternative moderne à `ls`
- Nécessite l'ajout d'un dépôt APT signé (clé GPG dans `/etc/apt/keyrings/`)
- Apporte : icônes (nécessite `fonts-font-awesome`), couleurs, regroupement des dossiers en premier, affichage en arbre (`--tree`)
- Se configure via des **alias** dans `~/.bashrc` (`l`, `ll`, `la`, `l.`, `lt`)

### Le prompt (PS1)
Structure standard `user@host:path$` :
- `user` = utilisateur courant
- `@` = séparateur
- `host` = nom d'hôte
- `:` = séparateur
- `path` = répertoire courant
- `$` = fin de prompt (⚠️ devient `#` si l'utilisateur est root — bon réflexe visuel de sécurité à mentionner à l'oral)

Personnalisation via la variable **`PS1`** dans `~/.bashrc`, avec des **codes d'échappement ANSI** pour la couleur (`\[\033[01;32m\]`) et des séquences bash spéciales (`\u`=user, `\h`=host, `\w`=working dir).

⚠️ **Bonne pratique à citer à l'oral** : toujours faire une **sauvegarde** du fichier avant modification (`cp ~/.bashrc ~/.bashrcSave`), et **commenter** l'ancienne ligne plutôt que la supprimer, pour pouvoir revenir en arrière.

### Fichier `.bashrc`
- Fichier de configuration **par utilisateur**, exécuté à chaque ouverture de shell interactif non-connecté
- Contient alias, variables d'environnement, personnalisation du prompt
- Rechargement sans redémarrer le terminal : `source ~/.bashrc`

## 4. Points capitaux NON abordés dans ce cours mais attendus à l'oral TSSR

### a) Les fichiers de configuration du shell (au-delà de `.bashrc`)
- `.bash_profile` / `.profile` : exécutés à la **connexion** (login shell), contrairement à `.bashrc` (shell interactif non-login)
- `/etc/bash.bashrc` et `/etc/profile` : configuration **globale** (tous les utilisateurs), à distinguer des fichiers `~/.bashrc` (par utilisateur)
- `.bash_logout` : exécuté à la déconnexion

### b) Les variables d'environnement
- `$PATH` : liste des répertoires où le shell cherche les exécutables (`echo $PATH`, modification via `export PATH=$PATH:/nouveau/chemin`)
- `$HOME`, `$USER`, `$SHELL`, `$PWD`, `$OLDPWD`
- Différence `export` (variable d'environnement, héritée par les sous-processus) vs variable shell simple (locale au shell courant)

### c) Les redirections et pipes (fondamentaux, souvent demandés à l'oral)
- `>` (redirection stdout, écrase), `>>` (append), `<` (redirection stdin), `2>` (stderr), `2>&1` (fusion stderr/stdout)
- `|` (pipe) pour chaîner des commandes : `cat fichier | grep motif | wc -l`

### d) Les permissions Linux en détail
- Notation symbolique (`rwxr-xr-x`) vs notation octale (`755`)
- `chmod`, `chown`, `chgrp`, umask
- Utilisateur / groupe / autres (u/g/o)

### e) Processus et gestion de session
- `ps`, `top`/`htop`, `kill`, `jobs`, `fg`/`bg`, `&` (exécution en arrière-plan)
- Différence entre un shell **login** et **non-login**, **interactif** et **non-interactif**

### f) Les shells alternatifs et leur intérêt
- `zsh` (souvent avec Oh My Zsh), `fish` : autocomplétion avancée, suggestions
- Différence de syntaxe entre `bash` et `sh` (POSIX) — important pour l'écriture de scripts portables

### g) Notion de script shell
- Shebang `#!/bin/bash`
- Rendre un script exécutable : `chmod +x script.sh`
- Variables, boucles, conditions basiques dans un script

### h) Historique des commandes
- `history`, raccourci `Ctrl+R` (recherche inversée), fichier `~/.bash_history`

## 5. QCM d'entraînement

**Question 1.** Quelle est la différence fondamentale entre un terminal et un shell ?
- A) Il n'y a aucune différence, ce sont des synonymes
- B) Le terminal est l'interpréteur de commandes, le shell est l'interface graphique
- C) Le terminal est l'environnement d'entrée/sortie (émulateur), le shell est l'interpréteur de commandes qui s'exécute dedans
- D) Le terminal ne fonctionne que sous Windows, le shell que sous Linux

**Question 2.** Sur Linux, quel fichier de configuration est exécuté à chaque ouverture d'un shell interactif (pour définir alias et prompt) ?
- A) `/etc/passwd`
- B) `~/.bashrc`
- C) `~/.ssh/config`
- D) `/boot/grub/grub.cfg`

**Question 3.** Que fait la commande `source ~/.bashrc` ?
- A) Elle supprime le fichier `.bashrc`
- B) Elle recharge la configuration du fichier sans avoir à rouvrir le terminal
- C) Elle crée un nouvel utilisateur
- D) Elle installe un nouveau shell

**Question 4.** Quel raccourci clavier standard permet d'ouvrir un terminal sous Ubuntu/GNOME ?
- A) `Ctrl + Alt + Suppr`
- B) `Ctrl + Alt + T`
- C) `Alt + F4`
- D) `Ctrl + Shift + Echap`

**Question 5.** Pourquoi faut-il être prudent avec les commandes `chmod` et `rm` ?
- A) Elles nécessitent une connexion internet
- B) Elles ne fonctionnent que sur les systèmes 32 bits
- C) Elles peuvent rendre le système inutilisable ou supprimer des données de manière irréversible
- D) Elles sont dépréciées depuis Ubuntu 20.04

**Question 6.** Que représente `\w` dans une variable `PS1` ?
- A) Le nom d'utilisateur
- B) Le nom d'hôte
- C) Le répertoire de travail courant
- D) La date du jour

**Question 7.** Quel outil permet d'obtenir une explication détaillée et visuelle d'une commande shell sans utiliser `man` ?
- A) ExplainShell
- B) StackOverflow uniquement
- C) `apt search`
- D) `dpkg -l`

**Question 8.** Quelle est la bonne pratique avant de modifier le fichier `~/.bashrc` ?
- A) Le supprimer et en recréer un vide
- B) Le renommer en `.bashrc.old` puis continuer d'y écrire
- C) En faire une copie de sauvegarde (ex. `cp ~/.bashrc ~/.bashrcSave`)
- D) Aucune précaution n'est nécessaire, `.bashrc` est un fichier système protégé

**Question 9.** Quelle est la différence entre `.bashrc` et `.bash_profile` ?
- A) Il n'y a aucune différence
- B) `.bashrc` est utilisé pour un shell interactif non-login, `.bash_profile` pour un shell de connexion (login)
- C) `.bash_profile` est réservé à root
- D) `.bashrc` n'existe pas sous Linux

**Question 10.** Que permet de faire un pipe (`|`) en ligne de commande ?
- A) Rediriger la sortie d'erreur vers un fichier
- B) Chaîner la sortie d'une commande comme entrée d'une autre commande
- C) Lancer une commande en arrière-plan
- D) Créer un alias permanent

---

### Corrigé
1. C — 2. B — 3. B — 4. B — 5. C — 6. C — 7. A — 8. C — 9. B — 10. B

---
