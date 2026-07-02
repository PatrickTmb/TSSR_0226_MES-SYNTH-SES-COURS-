 Révision — DHCP avec Windows Server

## 🎯 Objectifs de la quête
- Comprendre le fonctionnement du protocole DHCP
- Savoir configurer le service DHCP sur un serveur Windows

---

## 1. Le rôle du service DHCP

- **DHCP (Dynamic Host Configuration Protocol)** = extension de BOOTP, défini par les RFC 2131 et 2132.
- Il permet d'attribuer **dynamiquement** une configuration IP complète aux clients : adresse IP, masque, passerelle, DNS, WINS...
- On parle de **bail DHCP** : la configuration IP est prêtée pour une **durée déterminée**.
- Alternative à l'adressage manuel/statique, qui devient vite ingérable à grande échelle.

### Pourquoi utiliser DHCP ? (à retenir pour l'examen)
| Avantage | Explication |
|---|---|
| Fiabilité / simplicité | Évite les conflits d'adresses IP |
| Gestion centralisée | Un seul point de configuration réseau |
| Mise à jour facile | Changer un paramètre (ex: passerelle) sur le serveur suffit → propagé au renouvellement du bail |
| Économie d'adresses | Utilisé par les FAI ; seules les machines connectées consomment une IP |
| Mobilité | Facilite la gestion des postes itinérants |
| Souplesse | Peut aussi gérer de l'**adressage statique via réservation MAC ↔ IP** (RFC 3046) |

---

## 2. Fonctionnement : le processus DORA ⭐ (question quasi certaine à l'examen)

Un client sans IP doit obtenir un bail en **4 étapes** :

1. **DISCOVER** : le client diffuse une demande de bail (broadcast). Source = `0.0.0.0`, destination = `255.255.255.255`.
2. **OFFER** : le(s) serveur(s) DHCP répondent avec une proposition d'IP, durée de bail et adresse du serveur.
3. **REQUEST** : le client choisit la première offre reçue et diffuse une demande confirmant le serveur choisi → les autres serveurs retirent leur offre.
4. **ACK** : le serveur retenu accuse réception et accorde le bail ; le client peut utiliser l'adresse.

> 🔑 **Moyen mnémotechnique : DORA = Discover, Offer, Request, Acknowledge**

---

## 3. Renouvellement du bail

- À **50%** de la durée du bail écoulée → le client tente un renouvellement auprès du serveur d'origine (unicast).
- Sans réponse, à **87,5%** → le client diffuse une demande à **tous** les serveurs DHCP (broadcast REQUEST).
- Réponses possibles : **ACK** (renouvelé) ou **NACK** (adresse invalide, étendue désactivée, etc.).
- Si le bail expire sans nouvelle IP → la communication IP s'interrompt.
- Si la demande échoue mais que le bail n'est pas encore expiré → le client garde ses paramètres actuels.
- Au redémarrage, le client tente de reprendre **la même adresse** via un REQUEST direct.

---

## 4. Paramètres à définir lors de la configuration d'une étendue

- Adresse de **début** et de **fin** de la plage
- **Plage(s) d'exclusion** (pour cohabiter avec des IP statiques)
- Masque de sous-réseau
- Passerelle par défaut
- Adresse(s) des serveurs DNS
- Durée du bail
- Possibilité de consulter les **baux actifs** (association IP ↔ MAC)

---

## 5. Procédure d'installation & configuration (Windows Server) — à savoir refaire

### a) Installation du rôle
1. Server Manager → **Manage → Add Roles and Features**
2. Next → *Role-based or feature-based installation* → Next
3. Garder le serveur sélectionné → Next
4. Cocher **DHCP Server** → accepter *Add Features* (outils d'admin)
5. Next (x3) → **Install** → Close

> ⚠️ Le serveur DHCP doit avoir une **adresse IP statique** avant configuration.

### b) Configuration post-déploiement (obligatoire après installation)
1. Rafraîchir le Server Manager (icône flèches rondes)
2. Attendre le triangle jaune de notification
3. Cliquer dessus → **Complete DHCP configuration**
4. **Commit** → **Close**

### c) Création d'une étendue (scope)
1. Ouvrir la **console DHCP** (Tools → DHCP, ou clic droit sur le serveur)
2. Clic droit sur **IPv4** → **New Scope**
3. Nom + description (ex: VLAN 10)
4. Définir plage de début/fin + masque (CIDR)
5. Next... → **Finish**

### d) Test
- Vérifier qu'un client du même réseau obtient bien une IP dans la plage configurée.

### e) Réservation d'adresse IP
- Dans **Reservations**, associer une **adresse MAC** à une **IP fixe** au sein (ou hors) de l'étendue.
- Le client réservé garde toujours la même IP, même après renouvellement.

---

## 6. Points clés à retenir pour l'examen ✅

- Le processus **DORA** (Discover / Offer / Request / Acknowledge) et le sens des broadcasts à chaque étape.
- Les seuils de renouvellement de bail : **50%** puis **87,5%**.
- La différence entre **ACK** et **NACK**.
- Les paramètres indispensables d'une étendue DHCP (plage, exclusions, masque, passerelle, DNS, durée du bail).
- L'ordre logique : **IP statique du serveur → installation du rôle → configuration post-déploiement → création de l'étendue → test → réservation**.
- La réservation DHCP = lier une **MAC à une IP fixe** sans sortir du fonctionnement DHCP.
- DHCP peut cohabiter avec des adresses statiques via des **exclusions** ou des **réservations**.

---

## 7. Rappel du challenge pratique (lab à refaire pour s'entraîner)

- Carte réseau en **Réseau Interne**
- Étendue : `172.20.0.100 - 172.20.0.200` sur `172.20.0.0/24`
- IP statique du serveur : `172.20.0.1/24`
- 1 client en DHCP doit obtenir une IP dans l'étendue
- Réservation : une IP fixe `172.20.0.10` pour une MAC donnée
- Livrable : lien GitHub avec 4 captures légendées (config serveur, étendue visible, IP client 1, IP client 2, fenêtre de réservation)
