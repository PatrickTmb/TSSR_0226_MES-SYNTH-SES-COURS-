 Résumé — Entêtes Ethernet et IP (révision examen)

## 1. Principe d'encapsulation

Sur un réseau Ethernet, un paquet IP est **encapsulé** dans une trame Ethernet.
Ordre de circulation des informations sur le câble :

1. Entête Ethernet
2. Entête IP
3. Payload (données applicatives)

Les champs sont reconnus par **leur position** et **leur taille fixe** (sauf options).

---

## 2. La trame Ethernet II

| Champ | Taille |
|---|---|
| Adresse MAC destination | 6 octets |
| Adresse MAC source | 6 octets |
| EtherType | 2 octets |
| Payload (datagramme IP) | variable |
| CRC / FCS (vérifie l'intégrité de la trame) | 4 octets |

**Total entête = 14 octets** (6+6+2)

> On parle de trame **Ethernet II** quand le champ EtherType contient une valeur **> 1536** (0x0600). En dessous, c'est un champ de longueur (norme 802.3).

EtherType courants à connaître :
- `08 00` → IPv4
- `08 06` → ARP
- `86 DD` → IPv6

---

## 3. L'entête IP (IPv4) — RFC 791

Taille par défaut : **20 octets** (sans options).

| Champ | Taille | Notes |
|---|---|---|
| Version | 4 bits | `4` pour IPv4, `6` pour IPv6 |
| IHL (Internet Header Length) | 4 bits | Nombre de mots de 32 bits de l'entête. Par défaut `5` → 5×4 = 20 octets. Max `15` → 60 octets |
| Service (ToS) | 8 bits | Priorité (3b) + Délai/Débit/Fiabilité/Coût (4×1b) + MBZ (1b) |
| Longueur totale | 16 bits (2 octets) | Taille totale du datagramme IP (entête + données), **en octets** |
| Identification | 16 bits (2 octets) | Identifie le datagramme (utile pour la fragmentation) |
| Flags | 3 bits | Bit de fragmentation : `0`=réservé, `1`=DF (Don't Fragment), `2`=MF (More Fragments) |
| Fragment Offset | 13 bits | Position du fragment |
| TTL (Time To Live) | 8 bits (1 octet) | Décrémenté à chaque routeur traversé |
| Protocole | 8 bits (1 octet) | Protocole encapsulé : `01`=ICMP, `06`=TCP, `11` (17)=UDP → voir RFC 1700 |
| Checksum | 16 bits (2 octets) | Contrôle d'intégrité de l'entête IP uniquement |
| Adresse IP source | 32 bits (4 octets) | |
| Adresse IP destination | 32 bits (4 octets) | |
| Options | 0 à 40 octets | Variable |
| Padding (bourrage) | 0 à 7 bits | Complète l'entête à un multiple de 4 octets |

**⚠️ Piège fréquent :** Flags (3 bits) et Fragment Offset (13 bits) sont **contenus ensemble dans les 2 octets** qui suivent le champ Identification. Il faut convertir en binaire pour bien séparer les 3 premiers bits (flags) des 13 bits suivants (offset).

Exemple : `40 00` en hexadécimal = `0100 0000 0000 0000` en binaire
→ 3 premiers bits = `010` = **flags = 2 (bit DF activé)**
→ 13 bits restants = `0000000000000` = **offset = 0**

---

## 4. Lecture d'une trame en hexadécimal

Méthode :
1. Repérer les **14 premiers octets** = entête Ethernet (6 MAC dest + 6 MAC src + 2 EtherType)
2. À partir de l'octet 15 (offset 0x0E), commence l'entête IP
3. Le **1er octet** de l'entête IP donne Version (4 bits de poids fort) + IHL (4 bits de poids faible). Ex : `45` → Version = `4`, IHL = `5`
4. Découper ensuite champ par champ selon les tailles du tableau ci-dessus
5. Les adresses IP se lisent en **décimal pointé** : chaque octet hexa → décimal, séparés par des points

---

## 5. Exemple corrigé (trame du challenge)

```
0000: 00 12 17 41 c2 c7 00 1a 73 24 44 89 08 00 45 00
0010: 01 bb da c2 40 00 3c 06 fc 9d d5 e4 00 2a 3e 93
0020: 51 3b 00 50 04 85 87 c7 14 d5 00 12 b0 cb 50 19
```

| Élément demandé | Valeur trouvée |
|---|---|
| Adresse MAC destination | `00:12:17:41:c2:c7` |
| Adresse MAC source | `00:1a:73:24:44:89` |
| EtherType | `08 00` → IPv4 |
| Version | `4` |
| IHL | `5` (→ entête de 20 octets, pas d'options) |
| Service (ToS) | `00` |
| Longueur totale | `01 bb` = **443 octets** (décimal) |
| Identification | `da c2` |
| Flags | `010` = **2 (DF, ne pas fragmenter)** |
| Fragment Offset | `0` |
| TTL | `3c` = **60** (décimal) |
| Protocole | `06` → **TCP** |
| Checksum | `fc 9d` |
| Adresse IP source | `d5.e4.00.2a` = **213.228.0.42** |
| Adresse IP destination | `3e.93.51.3b` = **62.147.81.59** |

> **Note de correction :** dans la version postée en solution, les champs *Flags* (indiqué `4`) et *TTL* (indiqué en hexa brut `3c` au lieu de sa valeur décimale `60`) contenaient des imprécisions. Le calcul détaillé ci-dessus (conversion binaire de `40 00`) donne Flags = 2 et Offset = 0, ce qui est cohérent avec un paquet TCP non fragmenté typique.

---

## 6. Points essentiels à retenir pour l'examen

✅ **Encapsulation** : Ethernet (couche 2) transporte IP (couche 3) qui transporte TCP/UDP (couche 4)

✅ **Taille fixe de l'entête Ethernet II** : toujours 14 octets (avant le payload)

✅ **Taille de l'entête IP** : 20 octets par défaut (IHL=5), jusqu'à 60 octets avec options

✅ **IHL se lit en mots de 32 bits**, pas en octets directement (IHL × 4 = taille en octets)

✅ **Savoir distinguer** : la longueur totale IP inclut l'entête ET les données ; ce n'est pas la taille de la trame Ethernet complète (qui ajoute les 14 octets d'entête Ethernet + 4 octets de CRC)

✅ **Le champ Protocole** de l'entête IP permet d'identifier ce qui est encapsulé au-dessus (1=ICMP, 6=TCP, 17=UDP)

✅ **Savoir convertir hexa → binaire → décimal**, en particulier pour les champs Flags/Offset qui partagent les mêmes 2 octets

✅ **Le Checksum IP** ne protège que l'entête IP, pas les données (contrairement au CRC Ethernet qui protège toute la trame)

✅ **EtherType vs longueur** : la norme 802.3 utilise ce champ comme longueur si valeur ≤ 1536 ; Ethernet II l'utilise comme type de protocole si > 1536

---

## 7. Questions probables à l'oral

- Que se passe-t-il si l'IHL vaut 6 au lieu de 5 ? (→ présence d'options, entête de 24 octets)
- Pourquoi le TTL diminue-t-il à chaque saut ? (→ éviter les boucles de routage infinies)
- Que signifie le bit DF dans les Flags ? (→ interdit la fragmentation du paquet)
- Différence entre le checksum IP et le CRC Ethernet ? (→ portée différente : entête IP seule vs trame entière)
- Comment reconnaît-on une trame Ethernet II d'une trame 802.3 ? (→ valeur du champ EtherType/Longueur, seuil à 1536)
