# Introduction à la sécurité des systèmes d'information

*10 questions, plusieurs réponses possibles par question. Corrigé en bas de page.*

---

**Question 1.**
Que signifie le sigle DICP en sécurité des systèmes d'information ?

A. Disponibilité, Intégrité, Confidentialité, Preuve
B. Détection, Identification, Correction, Protection
C. Données, Infrastructure, Confidentialité, Personnel
D. Disponibilité, Investigation, Cybersécurité, Preuve

---

**Question 2.**
Quels sont les points importants à vérifier pour la mise en place de sauvegardes ?

A. L'intégrité : les sauvegardes doivent être régulièrement vérifiées
B. L'exhaustivité : il faut sauvegarder la totalité de l'ensemble des supports de stockage (SSD, disques durs, etc.)
C. La régularité : les sauvegardes doivent être refaites régulièrement pour rester à jour
D. Le stockage : toutes les sauvegardes doivent être conservées sur le même serveur que les données d'origine

---

**Question 3.**
Parmi ces mots de passe, lesquels peuvent être considérés comme robustes ?

A. =p0WAx4*ED3fg9
B. 1234567890
C. WildoWilder24-09-1997
D. Fg9+Ht7

---

**Question 4.**
Parmi ces propositions, lesquelles sont des bonnes pratiques de cybersécurité ?

A. Installer des antivirus sur tous les équipements
B. Avoir une liste à jour et exhaustive de l'ensemble des équipements informatiques de la structure
C. Mettre à jour l'ensemble des logiciels le plus régulièrement possible
D. Informer et sensibiliser l'ensemble des collaborateurs

---

**Question 5.**
Concernant les comptes utilisateurs, quelles recommandations sont correctes ?

A. Un administrateur n'a pas besoin d'un autre compte puisque son compte administrateur permet de tout faire
B. Il est envisageable de partager un compte entre plusieururs utilisateurs
C. Les comptes des utilisateurs ayant quitté la structure doivent être désactivés ou supprimés rapidement
D. Chaque utilisateur doit disposer d'un compte nominatif

---

**Question 6.**
Qu'est-ce que le principe du "moindre privilège" ?

A. Donner à chaque utilisateur uniquement les droits strictement nécessaires à son activité
B. Donner à tous les utilisateurs les droits d'administrateur pour simplifier la gestion
C. Limiter le nombre de comptes créés dans l'organisation
D. Réserver les droits d'administration au service informatique uniquement, sans exception

---

**Question 7.**
Que signifie le sigle RPO (Recovery Point Objective) ?

A. La quantité de données qu'une organisation accepte de perdre en cas d'incident
B. Le temps nécessaire pour restaurer un système après un incident
C. Le nombre de copies de sauvegarde à conserver
D. Le responsable désigné pour piloter les sauvegardes

---

**Question 8.**
Quelle est la règle de sauvegarde connue sous le nom de "règle 3-2-1" ?

A. 3 sauvegardes par jour, 2 responsables, 1 rapport
B. 3 copies des données, sur 2 supports différents, dont 1 copie hors site
C. 3 supports de stockage, 2 sauvegardes par semaine, 1 seul site
D. 3 utilisateurs autorisés, 2 mots de passe, 1 sauvegarde mensuelle

---

**Question 9.**
Quelles sont les principales familles de menaces pour un système d'information ?

A. Les malwares (virus, ransomwares, chevaux de Troie)
B. L'ingénierie sociale (phishing, prétexting)
C. Les sinistres physiques (incendie, vol de matériel)
D. Uniquement les attaques informatiques venant de l'extérieur de l'organisation

---

**Question 10.**
Qu'est-ce que la "défense en profondeur" ?

A. Une stratégie consistant à superposer plusieurs couches de protection complémentaires (technique, organisationnelle, humaine)
B. Une méthode consistant à investir uniquement dans le pare-feu le plus performant du marché
C. Le fait de confier toute la sécurité à un seul prestataire externe
D. Un principe selon lequel une seule mesure de sécurité bien choisie suffit à protéger le SI

---

## Corrigé

**1. A** — DICP = Disponibilité, Intégrité, Confidentialité, Preuve (traçabilité). C'est le socle théorique fondamental de la SSI.

**2. A, C** — L'intégrité (vérification régulière) et la régularité sont essentielles. B est un piège : l'exhaustivité concerne la couverture des *données critiques pertinentes*, pas la sauvegarde brute de tous les supports physiques sans discernement. D est faux et dangereux : une sauvegarde stockée uniquement sur le même serveur que les données d'origine ne protège contre rien (perte totale en cas de sinistre ou de ransomware) — c'est l'inverse de la règle 3-2-1.

**3. A** — Seul =p0WAx4*ED3fg9 combine longueur, complexité et absence de structure logique. B est trivial. C contient une date de naissance devinable malgré sa longueur. D est trop court malgré sa complexité apparente.

**4. A, B, C, D** — Les quatre propositions sont de bonnes pratiques complémentaires : protection technique (antivirus), gouvernance (inventaire), maintenance (mises à jour), et facteur humain (sensibilisation).

**5. C, D** — Chaque utilisateur doit avoir un compte nominatif, et les comptes des personnes parties doivent être désactivés rapidement. A et B sont des erreurs : un admin doit avoir un compte séparé pour l'administration (moindre privilège), et le partage de compte détruit la traçabilité.

**6. A** — Le moindre privilège consiste à limiter les droits de chaque utilisateur au strict nécessaire, y compris pour les administrateurs (séparation compte admin/standard).

**7. A** — Le RPO définit la perte de données maximale acceptable (ex. RPO de 1h = on accepte de perdre au maximum 1h de données). À ne pas confondre avec le RTO (temps de restauration).

**8. B** — 3 copies des données, sur 2 supports différents, dont 1 copie hors site (offsite), pour se prémunir contre un sinistre physique ou une attaque touchant le site principal.

**9. A, B, C** — Les menaces sont multiples : logicielles (malwares), humaines (ingénierie sociale), et physiques (sinistres). D est faux : les menaces internes (erreur humaine, malveillance d'un collaborateur, ex-employé) sont également une source majeure de risque, pas uniquement les attaques externes.

**10. A** — La défense en profondeur repose sur la superposition de plusieurs couches de sécurité complémentaires, car aucune mesure seule n'est infaillible.
