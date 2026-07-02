# Fiche de révision - VirtualBox : Installation


> Fiche construite pour préparation à l'oral TSSR (CCP Virtualisation)

---

## 1. Rappel du contexte de l'exercice

L'exercice demande de :
1. Activer la virtualisation matérielle si nécessaire (au niveau du BIOS/UEFI)
2. Installer VirtualBox
3. Installer l'Extension Pack correspondant

La vérification se fait de manière visuelle (Explorateur de fichiers), sans ligne de commande :
- `C:\Program Files\Oracle\VirtualBox` → confirme l'installation du logiciel
- `C:\Program Files\Oracle\VirtualBox\ExtensionPacks\Oracle_VirtualBox_Extension_Pack` → confirme l'installation de l'Extension Pack

---

## 2. Points essentiels à retenir (cœur du cours)

### 2.1 Qu'est-ce que la virtualisation matérielle ?
- La virtualisation permet de faire tourner un ou plusieurs systèmes d'exploitation invités (**guest OS**) sur une machine physique (**host**), de manière isolée.
- Elle repose sur des extensions du processeur :
  - **Intel VT-x** (Virtualization Technology)
  - **AMD-V** (AMD Virtualization)
- Ces extensions doivent être **activées dans le BIOS/UEFI** de la machine hôte, sinon VirtualBox refuse de démarrer une VM 64 bits ou affiche un message d'erreur (ex : `VT-x is not available`).
- À l'oral, sache expliquer **comment vérifier** que la virtualisation est activée sans aller dans le BIOS : Gestionnaire des tâches → onglet Performance → CPU → ligne « Virtualisation : Activé/Désactivé ».

### 2.2 Qu'est-ce que VirtualBox ?
- VirtualBox est un **hyperviseur de Type 2** (hébergé), développé par Oracle.
- Un hyperviseur de Type 2 s'installe **par-dessus un système d'exploitation existant** (Windows, Linux, macOS), contrairement à un hyperviseur de Type 1 (bare-metal, ex : VMware ESXi, Microsoft Hyper-V en mode serveur, Proxmox VE) qui s'installe directement sur le matériel.
- VirtualBox est **open source (GPL v2)** pour son cœur logiciel.

### 2.3 L'Extension Pack : pourquoi c'est important
- L'Extension Pack **n'est pas open source** : il est distribué sous licence **PUEL** (Personal Use and Evaluation License) — gratuite pour un usage personnel/éducatif/évaluation, payante pour un usage commercial en entreprise.
- Il ajoute des fonctionnalités **absentes du cœur VirtualBox** :
  - Support des périphériques **USB 2.0 (EHCI) et USB 3.0 (xHCI)**
  - **VirtualBox RDP** (Remote Desktop Protocol) pour se connecter à distance à une VM
  - **PXE boot pour cartes réseau Intel** (démarrage réseau)
  - Chiffrement de disque des machines virtuelles (VM disk encryption)
  - Lecture de webcam hôte via passthrough
- **Point d'attention examen** : la version de l'Extension Pack doit **impérativement correspondre à la version de VirtualBox installée** (ex : VirtualBox 7.0.x → Extension Pack 7.0.x). Une incompatibilité de version empêche son bon fonctionnement.

### 2.4 Méthode de vérification (sans terminal)
- Le cours privilégie une vérification **par l'interface graphique / explorateur de fichiers**, cohérent avec une approche GUI.
- Alternative native dans VirtualBox : menu **Fichier → Outils → Gestionnaire de VirtualBox**, puis **Aide → À propos de VirtualBox** affiche la version installée, et **Fichier → Préférences → Extensions** liste les extension packs installés avec leur version — méthode plus fiable que la simple présence du dossier, car elle confirme aussi que VirtualBox reconnaît l'extension comme active.

---

## 3. Points capitaux non abordés dans ce cours (à connaître pour l'oral)

Ces éléments sont hors du périmètre strict de l'exercice mais font partie du socle de connaissance attendu sur VirtualBox pour un TSSR :

| Thème | Ce qu'il faut savoir |
|---|---|
| **Types de stockage virtuel** | VDI (format natif VirtualBox), VMDK (compatible VMware), VHD (compatible Hyper-V) ; disques à taille fixe vs dynamiquement alloués |
| **Modes réseau** | NAT, NAT Network, Réseau ponté (Bridged), Réseau interne (Internal Network), Réseau privé hôte (Host-only) — savoir expliquer la différence et un cas d'usage pour chacun |
| **Snapshots (instantanés)** | Mécanisme de différencing disk ; risque de suppression sans vérifier l'intégrité des fichiers différentiels avant de supprimer un snapshot parent |
| **Guest Additions** | Pilotes et outils installés **dans** l'OS invité (différent de l'Extension Pack, installé côté hôte) : résolution d'écran dynamique, dossiers partagés, presse-papiers partagé, glisser-déposer |
| **Import/Export de VM** | Format **OVA/OVF** (Open Virtualization Format), standard pour la portabilité entre hyperviseurs |
| **Clonage** | Clone lié (linked clone, dépend du disque parent) vs clone complet (full clone, autonome) |
| **Allocation des ressources** | Bonnes pratiques : ne pas allouer plus de 50 % de la RAM physique de l'hôte à une VM ; nombre de cœurs virtuels (vCPU) ≤ cœurs physiques disponibles |
| **VBoxManage** | Interface en ligne de commande équivalente au GUI, utile pour l'automatisation et les scripts (utile de savoir qu'elle existe même en préférant le GUI) |
| **Sécurité / isolation** | Notion de VM comme environnement isolé pour tester des configurations à risque (malware, correctifs) sans impacter l'hôte |
| **Alternatives concurrentes** | VMware Workstation Pro/Player, Hyper-V (natif Windows Pro/Entreprise), QEMU/KVM (Linux) — savoir positionner VirtualBox par rapport à elles |

---

## 4. QCM d'entraînement

*Une seule bonne réponse par question, sauf mention contraire.*

**1. VirtualBox est un hyperviseur de quel type ?**
A) Type 1 (bare-metal)
B) Type 2 (hébergé)
C) Un conteneur, pas un hyperviseur
D) Type 3 (cloud natif)

**2. Que doit-on activer dans le BIOS/UEFI avant d'installer des VM 64 bits performantes ?**
A) Secure Boot
B) Fast Boot
C) La virtualisation matérielle (VT-x / AMD-V)
D) Le TPM 2.0

**3. Quelle licence encadre l'Extension Pack VirtualBox ?**
A) GPL v2
B) MIT
C) PUEL (Personal Use and Evaluation License)
D) Apache 2.0

**4. Laquelle de ces fonctionnalités N'EST PAS apportée par l'Extension Pack ?**
A) Support USB 2.0/3.0
B) VirtualBox RDP
C) Dossiers partagés entre hôte et invité
D) PXE boot pour cartes Intel

**5. Où vérifier, dans l'Explorateur de fichiers Windows, que VirtualBox est installé ?**
A) `C:\Windows\System32\VirtualBox`
B) `C:\Program Files\Oracle\VirtualBox`
C) `C:\Users\Public\VirtualBox`
D) `C:\ProgramData\VirtualBox`

**6. Que faut-il vérifier concernant la version de l'Extension Pack lors de l'installation ?**
A) Rien, toutes les versions sont compatibles entre elles
B) Qu'elle est plus récente que VirtualBox de deux versions majeures
C) Qu'elle correspond à la version de VirtualBox installée
D) Qu'elle date de moins d'un mois

**7. Quelle est la différence entre les Guest Additions et l'Extension Pack ? (question ouverte)**
*(Réponse attendue : les Guest Additions s'installent dans le système invité et apportent pilotes/confort d'usage ; l'Extension Pack s'installe côté hôte et étend les capacités de VirtualBox lui-même, ex. USB, RDP.)*

**8. Parmi ces modes réseau VirtualBox, lequel isole complètement la VM d'Internet tout en permettant la communication entre plusieurs VM ?**
A) NAT
B) Bridged (Réseau ponté)
C) Internal Network (Réseau interne)
D) Host-only

**9. Quel format est le standard ouvert pour exporter/importer une VM entre différents hyperviseurs ?**
A) VDI
B) VMDK
C) OVA/OVF
D) ISO

**10. Vrai ou Faux : Il est recommandé d'allouer plus de 50 % de la RAM physique de l'hôte à une seule VM.**
A) Vrai
B) Faux

---

## 5. Corrigé du QCM

| Question | Réponse | Justification courte |
|---|---|---|
| 1 | B | Hébergé, tourne au-dessus d'un OS hôte existant |
| 2 | C | VT-x (Intel) / AMD-V (AMD), à activer au niveau BIOS/UEFI |
| 3 | C | Gratuite en usage personnel/éval, payante en entreprise |
| 4 | C | Les dossiers partagés sont fournis par les Guest Additions, pas l'Extension Pack |
| 5 | B | Dossier `Oracle\VirtualBox` sous Program Files |
| 6 | C | Une version incompatible peut empêcher le bon fonctionnement |
| 7 | — | Voir explication dans la question |
| 8 | C | Le réseau interne relie les VM entre elles sans accès externe |
| 9 | C | OVA (archive) / OVF (format XML descriptif), portable entre VirtualBox, VMware, etc. |
| 10 | B | Faux — il faut préserver des ressources pour l'hôte et les autres VM |

---

### 💡 Conseil pour l'oral
Sois capable d'expliquer **avec tes propres mots** :
- La différence Type 1 / Type 2 d'hyperviseur, avec un exemple de chaque
- Pourquoi l'Extension Pack est séparé du cœur VirtualBox (raison : licence différente)
- La différence entre Extension Pack (côté hôte) et Guest Additions (côté invité) — c'est une confusion fréquente et un classique de question piège à l'oral
