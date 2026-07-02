# Révision - Découpage de réseaux IP (Subnetting)

## 1. Pourquoi découper un réseau en sous-réseaux ?

- **Historique** : à l'origine, les réseaux de classe A permettaient des millions d'hôtes sur un même réseau → ingérable (trafic énorme, saturation).
- **Aujourd'hui** : on utilise la notation **CIDR** (et non plus les classes).
- **Avantages du découpage** :
  - Facilite l'administration (réseaux plus petits, gérables).
  - Permet d'interconnecter des technologies différentes (Ethernet, WiFi, ATM...).
  - Contraintes physiques (longueur de câble, topographie du site).
  - **Sécurité** : isoler des services/départements pour éviter qu'un trafic soit intercepté par un autre groupe (ex : marketing ne doit pas voir le trafic R&D).
- Deux types de découpage : **symétrique** (sous-réseaux de même taille) et **asymétrique** (tailles différentes selon les besoins réels).

---

## 2. Calcul du nombre d'hôtes disponibles

### Principe
Sur une plage d'adresses définie par le masque/CIDR :
- La **première adresse** = adresse réseau (non attribuable).
- La **dernière adresse** = adresse de diffusion / broadcast (non attribuable).
- Toutes les adresses entre les deux sont attribuables aux équipements.

### Formule clé à retenir ⭐
```
Nombre d'adresses disponibles = 2^(nombre de bits hôte) − 2
```

### Exemple : 192.168.10.0/24
- /24 → 3 premiers octets = réseau, dernier octet = hôte (8 bits hôte).
- Plage totale : 192.168.10.0 → 192.168.10.255
- Adresse réseau : .0 | Adresse broadcast : .255
- Adresses utilisables : .1 → .254 → **254 adresses**

### Rappel : CIDR ↔ bits hôte
```
CIDR = 32 − nombre de bits hôte
```

---

## 3. Tableau des puissances de 2 (à connaître par cœur)

| 2^n | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Valeur | 1 | 2 | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1024 | 2048 |

**Méthode** : pour un besoin de N équipements, trouver la plus petite puissance de 2 **strictement supérieure** à N (car il faut garder 2 adresses pour réseau + broadcast), puis appliquer la formule.

---

## 4. Découpage symétrique

Tous les sous-réseaux ont **la même taille** (même CIDR).

### Méthode (exemple : école avec 4 départements, min. 25 équipements chacun)
1. Chercher dans le tableau des puissances de 2 le nombre ≥ besoin+2 → ici 32 (2^5).
2. Nombre de bits hôte = 5 → adresses disponibles = 2^5 − 2 = **30**.
3. CIDR = 32 − 5 = **/27**.
4. Découper le réseau de départ en blocs de 32 adresses consécutifs.

### Exemple résultat (192.168.10.0/24 → 4 x /27)

| Sous-réseau | Adresse réseau | Broadcast | Début plage | Fin plage |
|---|---|---|---|---|
| Développement Web | 192.168.10.0/27 | 192.168.10.31 | .1 | .30 |
| Data et IA | 192.168.10.32/27 | 192.168.10.63 | .33 | .62 |
| Infra et cybersécurité | 192.168.10.64/27 | 192.168.10.95 | .65 | .94 |
| Design produit | 192.168.10.96/27 | 192.168.10.127 | .97 | .126 |

👉 On saute de **32 en 32** (taille du bloc) à chaque nouveau sous-réseau.

---

## 5. Découpage asymétrique

Chaque sous-réseau a une taille **adaptée à son besoin réel** → moins de gaspillage d'adresses.

### Méthode ⭐ (règle d'or)
1. **Toujours commencer par le sous-réseau le plus gros** (le plus d'équipements), puis descendre par ordre décroissant.
2. Pour chaque sous-réseau, calculer individuellement via le tableau des puissances de 2.
3. Attribuer les blocs les uns à la suite des autres, du plus grand au plus petit.

### Exemple résultat (192.168.10.0/24, besoins réels)

| Département | Besoin | Puissance | Hôtes dispo | CIDR |
|---|---|---|---|---|
| Data et IA | 34 | 2^6 | 62 | /26 |
| Développement Web | 20 | 2^5 | 30 | /27 |
| Infra et cybersécurité | 15 | 2^5 | 30 | /27 |
| Design produit | 6 | 2^3 | 6 | /29 |

| Sous-réseau | Adresse réseau | Broadcast | Début plage | Fin plage |
|---|---|---|---|---|
| Data et IA | 192.168.10.0/26 | .63 | .1 | .62 |
| Développement Web | 192.168.10.64/27 | .95 | .65 | .94 |
| Infra et cybersécurité | 192.168.10.96/27 | .127 | .97 | .126 |
| Design produit | 192.168.10.128/29 | .135 | .129 | .134 |

⚠️ **Piège classique d'examen** : si deux sous-réseaux ont le même nombre d'hôtes calculé (ex : 30 et 30), leur ordre entre eux est libre — mais ils doivent tous les deux passer **avant** les plus petits.

---

## 6. Points essentiels à retenir pour l'examen 🎯

1. **Formule à connaître par cœur** : `2^(bits hôte) − 2`
2. **CIDR = 32 − bits hôte**
3. Toujours retirer **2 adresses** (réseau + broadcast) par sous-réseau.
4. **Découpage asymétrique** : toujours trier du **plus grand besoin au plus petit** avant de calculer.
5. Savoir identifier sur une adresse donnée : adresse réseau, adresse de diffusion, plage utilisable, CIDR.
6. Bien distinguer le **découpage symétrique** (même taille pour tous) du **asymétrique** (taille selon besoin réel).
7. Justifier le choix du découpage par des critères de **sécurité** et de **gestion/segmentation**.

---

## 7. Exercice type à refaire (méthode d'entraînement)

Réseau **172.16.1.0/24**, 4 pôles :
- Pôle informatique : ~50 équipements
- Pôle développement : ~12 équipements
- Pôle Administratif : ~20 équipements
- Pôle Technicien : ~15 équipements

**À faire pour t'entraîner** :
- Version symétrique (tous les pôles avec la même taille, basée sur le plus gros besoin).
- Version asymétrique (trier du plus gros au plus petit, calculer individuellement).
- Pour chaque sous-réseau : formule utilisée, CIDR, adresse réseau, adresse broadcast, plage utilisable.

---

## Ressource complémentaire
Réseaux TCP/IP et adressage IPv4 : https://fr.wikibooks.org/wiki/R%C3%A9seaux_TCP/IP/Adressage_IP_v4
