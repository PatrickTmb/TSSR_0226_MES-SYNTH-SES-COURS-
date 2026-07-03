# Introduction à la sécurité des systèmes d'information

## 1. Définitions et enjeux

**Système d'information (SI)** : ensemble des ressources (matériel, logiciels, données, réseaux, personnes) permettant de collecter, stocker, traiter et diffuser l'information au sein d'une organisation.

**Sécurité des systèmes d'information (SSI)** : ensemble des moyens techniques, organisationnels, juridiques et humains mis en œuvre pour protéger le SI contre les risques (malveillance, erreur, accident).

**Pourquoi la SSI est critique :**
- Perte financière directe (rançon, arrêt de production, amende RGPD)
- Perte de données sensibles / confidentielles
- Atteinte à l'image et à la confiance (clients, partenaires)
- Obligations légales et réglementaires (RGPD, LPM, NIS2...)

---

## 2. Les 3 (ou 4) piliers de la sécurité — DICP

À retenir absolument pour l'oral, c'est LA base théorique de la SSI :

| Critère | Définition | Exemple de menace si non respecté |
|---|---|---|
| **D**isponibilité | Le SI et les données doivent être accessibles quand on en a besoin | Attaque DDoS, panne matérielle |
| **I**ntégrité | Les données ne doivent pas être altérées, modifiées sans autorisation | Corruption de données, modification malveillante |
| **C**onfidentialité | Seules les personnes autorisées accèdent à l'information | Fuite de données, espionnage |
| **P**reuve / Traçabilité (parfois "Auditabilité") | Pouvoir prouver qui a fait quoi, quand (imputabilité) | Absence de logs, compte partagé |

> **Astuce mnémotechnique pour l'oral :** DICP = **D**isponibilité, **I**ntégrité, **C**onfidentialité, **P**reuve.

---

## 3. Les grandes familles de menaces

- **Malwares** : virus, vers, chevaux de Troie, ransomwares, spywares
- **Ingénierie sociale** : phishing, spear-phishing, prétexting, baiting
- **Attaques réseau** : DDoS, sniffing, man-in-the-middle, ARP spoofing
- **Attaques applicatives** : injection SQL, XSS, faille zero-day
- **Menace interne** : erreur humaine, malveillance d'un collaborateur, ex-employé non désactivé
- **Sinistres physiques** : incendie, inondation, vol de matériel, panne

---

## 4. Les bonnes pratiques essentielles (piliers organisationnels)

### 4.1 Gestion des comptes utilisateurs
- **Compte nominatif obligatoire** : un compte = une personne (jamais de compte partagé) → garantit la **traçabilité** et l'**imputabilité**.
- **Principe du moindre privilège** : chaque utilisateur (y compris les administrateurs) ne doit avoir que les droits strictement nécessaires à son activité.
- **Séparation des comptes admin / utilisateur standard** : un administrateur doit avoir un compte dédié à l'administration, distinct de son compte de navigation quotidienne (mails, web), pour limiter le risque de compromission.
- **Cycle de vie des comptes** : désactivation/suppression rapide des comptes des personnes ayant quitté la structure (offboarding).

### 4.2 Gestion du parc informatique
- **Inventaire exhaustif et à jour** de tous les équipements (postes, serveurs, switchs, mobiles...) → base de la cartographie du SI. On ne peut pas protéger ce qu'on ne connaît pas.
- **Mise à jour régulière** de l'ensemble des logiciels, OS et firmwares (**patch management**) : la majorité des attaques exploitent des failles déjà connues et corrigées.
- **Antivirus / EDR** déployés sur l'ensemble des équipements.

### 4.3 Sensibilisation humaine
- Le facteur humain est la **première porte d'entrée** des cyberattaques (phishing en tête).
- Formation et sensibilisation régulières de l'ensemble des collaborateurs = mesure la plus rentable en cybersécurité.

### 4.4 Politique de mots de passe robustes
Un mot de passe robuste combine :
- **Longueur** suffisante (12+ caractères recommandés)
- **Complexité** : majuscules, minuscules, chiffres, symboles
- **Absence de structure logique/personnelle** : pas de dates de naissance, noms, mots du dictionnaire (même combinés, ex. "NomDate" reste faible malgré la longueur)
- Idéalement générés aléatoirement ou via un gestionnaire de mots de passe
- Complément indispensable : **authentification multi-facteurs (MFA/2FA)**

### 4.5 Politique de sauvegarde (backup)
Points de vérification essentiels :
- **Exhaustivité pertinente** : couvrir toutes les données/systèmes critiques (pas "tout sauvegarder sans discernement", mais cibler le périmètre important)
- **Régularité** : fréquence adaptée au **RPO** (Recovery Point Objective = quantité de données qu'on accepte de perdre)
- **Intégrité / test de restauration** : une sauvegarde non testée n'a aucune valeur garantie ; il faut vérifier régulièrement qu'elle est restaurable
- **Règle des 3-2-1** : 3 copies des données, sur 2 supports différents, dont 1 copie hors site (offsite)
- **Sécurisation** : chiffrement, sauvegardes immuables/isolées (protection anti-ransomware)
- **RTO** (Recovery Time Objective) : temps nécessaire pour restaurer le service après incident

---

## 5. Notion de défense en profondeur

Aucune mesure de sécurité seule n'est suffisante. La sécurité repose sur la **superposition de plusieurs couches de protection** (technique + organisationnelle + humaine), de sorte que si une couche est franchie, d'autres subsistent :
- Couche physique (accès aux locaux, salles serveurs)
- Couche réseau (pare-feu, segmentation VLAN, ACL)
- Couche système (antivirus, mises à jour, durcissement/hardening)
- Couche applicative (contrôle des accès, validation des entrées)
- Couche humaine (sensibilisation, politique de sécurité)
- Couche organisationnelle (gouvernance, audits, PRA/PCA)

---

## 6. Points clés à retenir pour l'oral (synthèse rapide)

1. **DICP** = les 4 critères fondamentaux de la sécurité de l'information.
2. **Défense en profondeur** = aucune mesure isolée n'est suffisante, il faut cumuler les protections.
3. **Principe du moindre privilège** = donner uniquement les droits nécessaires, séparer comptes admin/standard.
4. **Traçabilité** = compte nominatif obligatoire, jamais de compte partagé.
5. **Patch management** = mettre à jour régulièrement pour combler les failles connues.
6. **Le facteur humain** est le maillon le plus exploité → sensibilisation indispensable.
7. **Mot de passe robuste** = longueur + complexité + absence de structure prévisible, complété par le MFA.
8. **Règle 3-2-1** pour les sauvegardes + test de restauration régulier obligatoire.
9. **RPO/RTO** = à ne pas confondre : RPO = perte de données acceptable, RTO = temps de restauration acceptable.
10. **Cycle de vie des comptes** = désactiver rapidement les comptes des personnes parties (onboarding/offboarding).

