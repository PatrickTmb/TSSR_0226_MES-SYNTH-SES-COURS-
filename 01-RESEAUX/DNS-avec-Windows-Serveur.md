# DNS avec Windows Server — Fiche de révision

> Support de révision pour examen oral TSSR — basé sur la quête "DNS avec Windows Server"

---

## 1. Rappels théoriques essentiels

### 1.1 À quoi sert le DNS ?

Le **DNS (Domain Name System)** est un service distribué mondialement qui traduit des **noms de domaine** lisibles par l'humain (ex. `www.exemple.com`) en **adresses IP** utilisées par les machines (ex. `192.0.2.1`).

**Analogie à retenir pour l'oral :** le DNS fonctionne comme un **annuaire téléphonique** — on cherche un nom, on obtient un numéro (une IP).

### 1.2 Vocabulaire clé

| Terme | Définition |
|---|---|
| **Requête DNS** | Demande de résolution envoyée par un client à un serveur DNS |
| **Serveur faisant autorité** | Détient les enregistrements officiels d'une zone DNS ; répond avec l'information exacte ou redirige |
| **Résolveur récursif** | Intermédiaire entre le client et les serveurs faisant autorité ; interroge en chaîne jusqu'à obtenir la réponse (image du "concierge d'hôtel") |
| **Zone DNS** | Ensemble de noms de domaine gérés par un même serveur/administrateur |
| **Cache DNS** | Stockage temporaire des résolutions déjà obtenues, pour accélérer les futures requêtes |
| **Port DNS** | **53** (UDP pour les requêtes standards, TCP pour les transferts de zone ou réponses volumineuses) |

⚠️ **Piège classique en QCM/oral :** ne pas confondre le port 53 (DNS) avec 67/68 (DHCP).

### 1.3 Les deux grands types de serveurs

1. **Serveur faisant autorité** : source de vérité pour une zone donnée.
2. **Résolveur (récursif)** : ne possède pas les enregistrements, mais interroge à la place du client.

### 1.4 Le trajet d'une requête DNS (à savoir raconter à l'oral)

1. L'utilisateur tape `www.example.com` dans son navigateur.
2. La requête part vers le **résolveur DNS** configuré (souvent celui du FAI).
3. Le résolveur interroge un **serveur racine**.
4. Le serveur racine renvoie vers les serveurs du **TLD** concerné (`.com`).
5. Le serveur du TLD indique quel serveur fait **autorité** sur `example.com`.
6. Le serveur faisant autorité renvoie l'**adresse IP** associée à `www.example.com`.
7. Le résolveur transmet l'IP au navigateur **et met en cache** la réponse.
8. Le navigateur envoie sa requête HTTP directement à l'IP obtenue.
9. Le serveur web répond et la page s'affiche.

**Mot-clé à utiliser à l'oral :** *résolution récursive en cascade* (racine → TLD → autorité).

---

## 2. Les éléments à configurer sur un serveur DNS

| Élément | Rôle |
|---|---|
| **Zone de recherche directe** (Forward Lookup Zone) | Résolution **nom → IP** |
| **Zone de recherche inversée** (Reverse Lookup Zone) | Résolution **IP → nom** |
| **Enregistrement A / AAAA** | Associe un nom à une adresse IPv4 / IPv6 |
| **Enregistrement PTR** | Résolution inverse (utilisé dans la zone inversée) |
| **Enregistrement CNAME** | Alias vers un nom qui possède déjà un enregistrement A |
| **Enregistrement MX** | Désigne le serveur de messagerie |
| **Enregistrement NS** | Désigne les serveurs faisant autorité sur la zone |
| **DNS forwarders (redirecteurs)** | Transmettent les requêtes vers un DNS externe pour les noms non gérés localement |

**Point important pour l'oral :** un même enregistrement A peut avoir plusieurs alias CNAME, mais **on ne crée pas plusieurs A pour la même IP si on peut faire un CNAME** — c'est la bonne pratique.

---

## 3. Procédure d'installation et configuration (Windows Server)

### 3.1 Pré-requis
- Le serveur doit avoir une **adresse IP statique** (obligatoire : un serveur DNS ne peut pas voir son adresse changer).

### 3.2 Installation du rôle DNS
1. Ouvrir le **Server Manager**
2. **Manage → Add Roles and Features**
3. Next → garder *Role-based or feature-based installation* → Next
4. Garder le serveur sélectionné → Next
5. Cocher **DNS Server**
6. Accepter **Add Features** (outils d'administration)
7. Next (x3) → **Install** → **Close**
8. Rafraîchir (icône flèches circulaires) jusqu'à voir **DNS** apparaître dans le panneau gauche

### 3.3 Ouvrir la console DNS
- Icône DNS dans le panneau gauche du Server Manager, **ou**
- Clic droit sur le serveur → **DNS Manager**, **ou**
- **Tools → DNS**

### 3.4 Créer une zone directe (Forward Lookup Zone)
1. Clic droit sur *Forward Lookup Zones* → **New Zone**
2. Next → **Primary zone** (par défaut) → Next
3. Nom de zone (ex. `wilders.lan`)
4. Next… → **Finish**

### 3.5 Créer une zone inversée (Reverse Lookup Zone)
1. Clic droit sur *Reverse Lookup Zones* → **New Zone**
2. Next → **Primary zone** → Next
3. **IPv4 Reverse Lookup Zone** → Next
4. Renseigner le **Network ID** (début de la plage IP, ex. `172.16.10`)
5. Next… → **Finish**

### 3.6 Créer un enregistrement A
- Clic droit sur la zone directe → **New Host (A or AAAA)**
- Renseigner le nom DNS + l'adresse IP cible

### 3.7 Créer un enregistrement CNAME
- Clic droit sur la zone directe → **New Alias (CNAME)**
- L'alias doit pointer vers un nom qui possède déjà un enregistrement A

### 3.8 Tester le service
- Faire un **ping** depuis un client vers le nom DNS → doit répondre avec la résolution du nom
- Faire un **nslookup** pour vérifier la résolution directe et inverse

---

## 4. Points essentiels à retenir pour l'oral

- 🔑 Le DNS traduit **noms ↔ IP** ; sans lui il faudrait retenir des adresses IP pour naviguer.
- 🔑 Différence **serveur faisant autorité** vs **résolveur récursif** (source de vérité vs intermédiaire).
- 🔑 Le port utilisé est le **53**.
- 🔑 Une zone directe résout **nom → IP**, une zone inverse résout **IP → nom** (utile pour la traçabilité, les logs mail, etc.).
- 🔑 Un enregistrement **A** pointe vers une IP ; un **CNAME** pointe vers un autre nom (alias).
- 🔑 Le serveur DNS doit avoir une **IP fixe**.
- 🔑 Bonnes pratiques de sécurité/gestion en lab : faire un **snapshot** avant toute modification risquée.
- 🔑 Savoir dérouler de mémoire les **9 étapes de résolution DNS** (navigateur → résolveur → racine → TLD → autorité → réponse → cache → HTTP → affichage).
- 🔑 Le cache DNS a une durée de vie définie (**TTL**), ce qui accélère les résolutions suivantes.

---

## 5. QCM de révision (10 questions)

**Question 1.** Sur quel port écoute un serveur DNS pour les requêtes standards ?
A) 50
B) 53
C) 67
D) 68

**Question 2.** Que traduit le DNS ?
A) Des adresses MAC en adresses IP
B) Des noms de domaine en adresses IP
C) Des adresses IP en noms d'utilisateurs
D) Des ports en protocoles

**Question 3.** Quel type de serveur DNS détient les enregistrements officiels d'une zone ?
A) Le résolveur récursif
B) Le serveur faisant autorité
C) Le serveur forwarder
D) Le serveur DHCP

**Question 4.** Que permet une zone de recherche inversée (Reverse Lookup Zone) ?
A) De résoudre un nom en IP
B) De résoudre une IP en nom
C) De créer des alias
D) De gérer les serveurs mail

**Question 5.** Quel type d'enregistrement permet de créer un alias vers un nom déjà associé à une IP ?
A) A
B) PTR
C) CNAME
D) MX

**Question 6.** Quel pré-requis est indispensable avant d'installer le rôle DNS sur un serveur Windows ?
A) Avoir une adresse IP dynamique
B) Avoir une adresse IP statique
C) Avoir installé Active Directory
D) Avoir désactivé le pare-feu

**Question 7.** Dans la console DNS Manager, comment crée-t-on un enregistrement A ?
A) Clic droit sur la zone → New Alias (CNAME)
B) Clic droit sur la zone → New Host (A or AAAA)
C) Clic droit sur le serveur → New Zone
D) Clic droit sur Reverse Lookup Zones → New PTR

**Question 8.** Quel enregistrement DNS désigne le ou les serveurs de messagerie d'un domaine ?
A) NS
B) MX
C) CNAME
D) AAAA

**Question 9.** Que fait un résolveur DNS lorsqu'il ne trouve pas l'information demandée en cache ?
A) Il renvoie une erreur immédiatement
B) Il transmet la requête à un ou plusieurs serveurs faisant autorité
C) Il crée automatiquement un nouvel enregistrement
D) Il redémarre le service DNS

**Question 10.** Avant de faire une manipulation risquée sur une VM Windows Server en lab, quelle bonne pratique faut-il appliquer ?
A) Désinstaller le rôle DNS
B) Faire un snapshot de la machine
C) Supprimer la zone DNS existante
D) Redémarrer le serveur en mode sans échec

---

## 6. Corrigé du QCM

| N° | Réponse | Explication |
|---|---|---|
| 1 | **B) 53** | Port standard des requêtes DNS (UDP pour la résolution classique, TCP pour les transferts de zone) |
| 2 | **B) Des noms de domaine en adresses IP** | C'est la fonction centrale du DNS |
| 3 | **B) Le serveur faisant autorité** | Il détient et gère les enregistrements de la zone |
| 4 | **B) De résoudre une IP en nom** | À l'inverse de la zone directe (nom → IP) |
| 5 | **C) CNAME** | Un CNAME est un alias pointant vers un nom déjà résolu par un A |
| 6 | **B) Avoir une adresse IP statique** | Un serveur DNS ne doit pas changer d'adresse, sous peine de rompre la résolution |
| 7 | **B) Clic droit sur la zone → New Host (A or AAAA)** | Procédure standard dans DNS Manager |
| 8 | **B) MX** | Mail Exchanger : désigne le(s) serveur(s) de messagerie |
| 9 | **B) Il transmet la requête à un ou plusieurs serveurs faisant autorité** | C'est le principe de la résolution récursive |
| 10 | **B) Faire un snapshot de la machine** | Bonne pratique avant toute modification risquée en environnement de lab |

---

## 7. Rappel du challenge pratique (pour préparer l'oral)

Contexte : serveur DNS à l'IP `172.16.10.5`, faisant autorité sur `wilders.lan`.

À réaliser :
- ✅ Zone DNS directe `wilders.lan`
- ✅ Zone DNS indirecte associée à la plage `172.16.10.0/24`
- ✅ Un enregistrement **A** pour le serveur
- ✅ Un alias **CNAME** (ex. `dns.wilders.lan`)
- ✅ Vérification : ping depuis un client vers les 2 noms DNS
- ✅ Vérification : résultat de `nslookup` depuis le client

**Critères d'acceptation à citer à l'oral :**
- Résolution de nom **et** résolution inverse fonctionnelles, depuis le serveur et depuis un client du réseau
- Le serveur est joignable via **2 noms distincts**
- Documentation claire, reproductible, avec captures d'écran légendées
