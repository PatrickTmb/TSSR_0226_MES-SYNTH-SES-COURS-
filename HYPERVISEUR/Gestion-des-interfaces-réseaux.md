# VirtualBox - La gestion des interfaces réseau
## Fiche de révision enrichie - Préparation à l'oral TSSR

---

## 1. Principe général

Chaque machine virtuelle VirtualBox peut posséder **jusqu'à 4 interfaces réseau virtuelles**, configurables indépendamment les unes des autres. Chaque interface peut avoir un **mode d'accès réseau** différent, ce qui permet de simuler des architectures complexes (DMZ, réseau isolé, réseau de management, etc.) sur une seule machine hôte.

📍 Emplacement : **Paramètres de la VM → Réseau → onglet Interface X → Mode d'accès réseau**

---

## 2. Les 5 modes d'accès réseau à connaître par cœur

| Mode | La VM peut joindre... | L'hôte peut joindre la VM ? | Les VM entre elles ? | Adressage typique | Cas d'usage type |
|---|---|---|---|---|---|
| **NAT** | Internet (sortant uniquement) | ❌ Non (sauf redirection de port) | ❌ Non (chaque VM isolée) | 10.0.2.15/24 | Accès internet simple, sans exposer la VM |
| **Accès par pont (Bridged)** | Tout le réseau physique de l'hôte + Internet | ✅ Oui | ✅ Oui | Même plage que l'hôte (ex: 192.168.1.x/24) | VM qui doit apparaître comme une machine physique sur le LAN |
| **Réseau interne (Internal Network)** | Rien en dehors du groupe nommé | ❌ Non | ✅ Oui (si même nom) | Défini manuellement | Labo isolé (ex: DHCP, AD, pfSense LAN) |
| **Réseau privé hôte (Host-Only)** | L'hôte uniquement (+ VMs du même réseau) | ✅ Oui | ✅ Oui | 192.168.56.0/24 par défaut | Administration de la VM depuis l'hôte sans accès Internet |
| **Réseau NAT (NAT Network)** | Internet (sortant) + autres VM du même réseau NAT | ❌ Non (sauf redirection de port) | ✅ Oui (si même réseau NAT) | Défini dans Fichier → Paramètres → Réseau | Groupe de VM isolé du LAN physique mais avec sortie Internet commune |

---

## 3. Points essentiels à retenir pour l'oral

### 🔑 NAT (natif, par VM)
- Chaque VM a **sa propre instance NAT** → pas de communication inter-VM possible même si elles sont toutes en NAT.
- IP fixe généralement en **10.0.2.15/24**, la passerelle est **10.0.2.2**.
- Seules les connexions **initiées depuis la VM** fonctionnent (pare-feu implicite).
- Vérifier que la case **« Câble branché »** est cochée (section Avancé) sinon pas de lien réseau.
- Documentation officielle : `network_nat`.

### 🔑 Bridged (pont)
- La VM devient **visible comme une machine physique** sur le réseau de l'hôte.
- Nécessite de choisir **l'interface physique de l'hôte** (carte Ethernet ou Wi-Fi) à utiliser comme pont.
- Si un serveur DHCP existe sur ce réseau, la VM reçoit une IP dans la même plage que l'hôte.
- Cas d'usage fréquent en labo : simuler un serveur qui doit être joignable depuis tout le réseau local (ex: pfSense en WAN, serveur AD accessible par des postes physiques).

### 🔑 Réseau interne (Internal Network)
- Crée un **switch virtuel totalement isolé**, non relié à l'hôte ni à Internet.
- Les VM communiquent entre elles **uniquement si elles portent le même nom de réseau** (ex: `intnet`).
- Très utilisé pour un labo DHCP/DNS isolé, ou pour la **zone LAN/DMZ d'un pfSense** afin d'éviter tout conflit avec le réseau réel.
- ⚠️ Un routeur (comme pfSense) doit être présent pour donner un accès Internet à ce réseau si besoin — VirtualBox ne route rien lui-même.

### 🔑 Réseau privé hôte (Host-Only)
- VirtualBox crée une **carte réseau virtuelle sur l'hôte** (visible dans les connexions réseau Windows).
- Plage par défaut : **192.168.56.0/24**, hôte = **192.168.56.1**.
- Permet à l'hôte d'administrer directement la VM (ex: SSH, RDP) **sans donner accès à Internet**.
- Différence clé avec le réseau interne : **l'hôte fait partie du réseau**, pas seulement les VM.

### 🔑 Réseau NAT (NAT Network)
- À ne pas confondre avec le NAT simple !
- C'est un **réseau interne + un routeur NAT virtuel** donnant un accès Internet **partagé** à toutes les VM qui y sont connectées.
- Se configure en amont dans **Fichier → Paramètres de l'application → Réseau** (création du réseau, plage CIDR, activation DHCP, redirection de ports).
- Permet la communication **inter-VM** (contrairement au NAT simple) tout en gardant l'accès Internet.

---

## 4. Points capitaux NON abordés dans le cours (à connaître pour l'oral)

### 4.1 Mode Promiscuité (Promiscuous Mode)
Paramètre disponible en mode **Pont** et **Réseau interne**, avec 3 valeurs :
- **Refuser** (par défaut) : la VM ne voit que son propre trafic.
- **Autoriser les VM** : les VM du même réseau voient le trafic des autres VM.
- **Tout autoriser** : la VM peut voir tout le trafic du segment, y compris celui de l'hôte.
👉 Essentiel pour des labos de **capture réseau (Wireshark)** entre plusieurs VM.

### 4.2 Redirection de ports (Port Forwarding)
- Fonctionnalité indispensable en mode **NAT** et **NAT Network** pour rendre un service de la VM accessible depuis l'hôte (ex: rediriger le port hôte 2222 vers le port 22 de la VM pour du SSH).
- Se configure dans **Réseau → Avancé → Redirection de ports**.

### 4.3 Type de carte réseau virtuelle (chipset)
- Choix entre **Intel PRO/1000**, **PCnet**, **Paravirtualized (virtio-net)**.
- Le mode **paravirtualisé** offre de meilleures performances mais nécessite un pilote compatible dans l'OS invité (souvent absent sous Windows sans pilotes additionnels).

### 4.4 Différence VirtualBox vs VMware Workstation (comparaison utile pour le double environnement de labo)
| VirtualBox | VMware Workstation |
|---|---|
| NAT (par VM) | NAT (VMnet8) |
| Pont | Pont (Bridged, VMnet0) |
| Réseau interne | Réseau personnalisé sans accès externe (VMnet isolé) |
| Réseau privé hôte | Hôte uniquement (VMnet1) |
| Réseau NAT | — (équivalent obtenu via VMnet8 partagé) |

### 4.5 Bonnes pratiques de labo (utile pour Pharmgreen / pfSense)
- Toujours **nommer clairement** les réseaux internes (`LAN_Pharmgreen`, `DMZ_Pharmgreen`, etc.) pour éviter les erreurs de câblage virtuel.
- Pour un pare-feu comme pfSense : WAN en **NAT** ou **Pont**, LAN et DMZ en **Réseau interne**.
- Vérifier systématiquement **l'ordre des interfaces** (Interface 1, 2, 3, 4) car il conditionne souvent l'attribution WAN/LAN lors de l'installation de pfSense.

### 4.6 Limitations à connaître
- Maximum **4 interfaces réseau visibles dans l'interface graphique** (8 possibles en ligne de commande via `VBoxManage`).
- Le réseau **Host-Only** ne fournit pas de DHCP par défaut avant VirtualBox 6 ; depuis, un DHCP intégré peut être activé dans les paramètres globaux.

---

## 5. QCM d'entraînement

*(Réponses à la fin — cache-les avant de t'exercer !)*

**Q1.** Combien d'interfaces réseau virtuelles maximum peut-on configurer par VM via l'interface graphique de VirtualBox ?
A. 2
B. 4
C. 6
D. 8

**Q2.** Quelle est l'adresse IPv4 typique attribuée à une VM en mode NAT natif VirtualBox ?
A. 192.168.56.1
B. 192.168.1.100
C. 10.0.2.15
D. 10.10.0.1

**Q3.** En mode NAT (natif, par VM), deux VM configurées en NAT peuvent-elles communiquer directement entre elles ?
A. Oui, toujours
B. Non, chaque VM a son propre NAT isolé
C. Oui, mais uniquement en UDP
D. Oui, si elles partagent le même nom de réseau

**Q4.** Quel mode d'accès réseau permet à une VM d'obtenir une IP sur le même segment physique que la machine hôte, via une carte réseau physique choisie (Ethernet ou Wi-Fi) ?
A. Réseau interne
B. Réseau privé hôte
C. Accès par pont
D. Réseau NAT

**Q5.** Quelle est la plage réseau par défaut du mode Réseau privé hôte (Host-Only) ?
A. 10.0.2.0/24
B. 192.168.56.0/24
C. 172.16.0.0/24
D. 192.168.0.0/16

**Q6.** Dans le mode Réseau interne, qu'est-ce qui permet à deux VM de communiquer entre elles ?
A. Elles doivent être sur le même hôte physique uniquement
B. Elles doivent porter le même nom de réseau interne
C. Elles doivent activer le mode Pont en complément
D. Rien, le réseau interne ne permet jamais la communication inter-VM

**Q7.** Quelle est la différence essentielle entre le Réseau privé hôte (Host-Only) et le Réseau interne (Internal Network) ?
A. Le réseau interne permet l'accès à Internet, pas le Host-Only
B. Le Host-Only inclut l'hôte dans le réseau, le réseau interne non
C. Il n'y a aucune différence
D. Le réseau interne est limité à 2 VM maximum

**Q8.** Que permet le "Réseau NAT" (NAT Network) que le NAT simple ne permet pas ?
A. L'accès à Internet
B. La communication entre VM connectées au même réseau NAT
C. La redirection de port
D. L'attribution automatique d'IP

**Q9.** Où configure-t-on la case à cocher "Câble branché" pour une interface réseau en mode NAT ?
A. Dans les paramètres généraux de VirtualBox
B. Dans la section Avancé des paramètres réseau de la VM
C. Dans le gestionnaire de réseau Windows
D. Cette option n'existe pas sous VirtualBox

**Q10.** (Point non abordé dans le cours) Quel paramètre réseau, disponible en mode Pont ou Réseau interne, permet à une VM de capturer le trafic destiné à d'autres machines du même segment (utile pour Wireshark) ?
A. Le mode paravirtualisé
B. La redirection de ports
C. Le mode Promiscuité
D. Le DHCP intégré

**Q11.** (Point non abordé dans le cours) Pour un labo pfSense avec WAN/LAN/DMZ sous VirtualBox, quelle association de modes est la plus cohérente ?
A. WAN en Réseau interne, LAN et DMZ en Pont
B. WAN en NAT ou Pont, LAN et DMZ en Réseau interne
C. Toutes les interfaces en Host-Only
D. Toutes les interfaces en Réseau NAT

**Q12.** Quel type de carte réseau virtuelle offre les meilleures performances mais nécessite un pilote spécifique parfois absent sous Windows sans ajout manuel ?
A. Intel PRO/1000
B. PCnet
C. Paravirtualized (virtio-net)
D. Realtek RTL8139

---

### ✅ Corrigé

1. B — 4 interfaces via l'interface graphique
2. C — 10.0.2.15
3. B — chaque VM a son propre NAT, isolé des autres
4. C — Accès par pont
5. B — 192.168.56.0/24
6. B — même nom de réseau interne
7. B — le Host-Only inclut l'hôte, pas le réseau interne
8. B — communication entre VM du même réseau NAT (le NAT simple isole chaque VM)
9. B — section Avancé des paramètres réseau de la VM
10. C — Mode Promiscuité
11. B — WAN en NAT/Pont, LAN et DMZ en Réseau interne
12. C — Paravirtualized (virtio-net)

---

## 6. Astuce mnémotechnique pour l'oral

> **N**AT = **N**e communique avec personne (isolé, sortant seulement)
> **P**ont = **P**résent sur le réseau physique comme une vraie machine
> **I**nterne = **I**solé du monde, sauf entre VM du même nom
> **H**ost-Only = **H**ôte inclus, pas d'Internet
> **NAT Network** = NAT + réseau partagé entre VM (le meilleur des deux mondes pour un labo isolé avec sortie Internet)
