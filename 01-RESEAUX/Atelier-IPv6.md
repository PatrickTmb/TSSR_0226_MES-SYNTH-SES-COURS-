# Résumé - Atelier IPv6 (révision examen)

## Objectif de l'atelier
Configurer et tester la connectivité IPv6 entre 3 VM (Ubuntu, Debian, Windows) via VirtualBox en mode **Réseau interne**, en passant par deux approches : adresses **globales manuelles** puis adresses **link-local automatiques**.

---

## 1. Partie 1 - Adressage IPv6 manuel (global unicast documentaire)

**Ubuntu** - ajout d'une adresse IPv6 sur une interface :
```bash
sudo ip -6 addr add 2001:db8::1/64 dev enp0s3
ip -6 addr show enp0s3 | grep inet6
```

**Windows** - équivalent PowerShell (admin) :
```powershell
Get-NetAdapter
New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress "3001:db8::2" -PrefixLength 64
Get-NetIPAddress | Where-Object {($_.InterfaceAlias -eq "Ethernet") -and ($_.AddressFamily -eq "IPv6")}
```

⚠️ **Point piège à retenir** : les deux machines sont volontairement sur des préfixes **différents** (`2001:db8::/64` vs `3001:db8::/64`) → le ping échoue tant qu'elles ne sont pas sur le **même sous-réseau**. C'est l'objet de l'étape suivante.

**Correction** - remise sur le même préfixe :
- Ubuntu : `2001:db8::3`
- Windows : `2001:db8::4`

👉 **Point essentiel** : comme en IPv4, deux hôtes doivent partager le même préfixe réseau pour communiquer directement sans routeur.

---

## 2. Commandes de test à connaître

| OS | Commande | Remarque |
|---|---|---|
| Linux | `ping -6 <adresse> -I <interface>` | `-I` précise l'interface source |
| Windows | `Test-Connection -ComputerName <adresse>%<n°interface>` | le `%` + index d'interface sert à lever l'ambiguïté sur les adresses **link-local** (obligatoire si adresse fe80::) |
| Windows | `Test-NetConnection` | cmdlet plus moderne, remplace `Test-Connection`, donne plus de détails (route, interface, etc.) |

---

## 3. Partie 2 — Adresses link-local (fe80::/64)

**Principe clé à retenir pour l'examen** :
> Une adresse **link-local** (`fe80::/64`) est **auto-générée automatiquement** par chaque interface IPv6, **sans DHCP ni configuration manuelle**, et permet la communication entre machines du **même lien physique/logique** uniquement (non routable).

### Configuration selon l'OS

**Ubuntu (NetworkManager / GUI)** — options IPv6 possibles :
- **Automatique** : SLAAC ou DHCPv6 selon disponibilité
- **Réseau local seulement** : uniquement link-local (fe80::/64) ← **option à choisir ici**
- **Automatique, DHCP seulement** : DHCPv6 pur
- **Manuel** : adresse/masque/passerelle saisis à la main
- **Partagé avec d'autres ordinateurs** : la machine devient routeur/passerelle NAT pour les autres

→ Redémarrage réseau : `sudo systemctl restart networking`

**Debian (fichier `/etc/network/interfaces`)** :
```
auto enp0s3
iface enp0s3 inet6 manual
iface enp0s3 inet manual
```
`inet6 manual` = pas de config auto ni statique → seule l'adresse link-local générée par le noyau reste active.

**Windows (PowerShell)** :
```powershell
Remove-NetIPAddress ...          # supprime l'adresse globale précédente
Set-NetIPInterface -InterfaceAlias "Ethernet" -AddressFamily IPv6 -Dhcp Disabled
# puis redémarrage de la machine
```

**Résultat attendu** : chaque interface affiche une adresse commençant par **fe80::** et les pings fonctionnent entre les 3 VM **sans aucune configuration d'adresse manuelle**.

---

## 4. Lexique technique à maîtriser

| Terme | Définition |
|---|---|
| **SLAAC** | Stateless Address Autoconfiguration — auto-configuration d'adresse IPv6 sans serveur DHCP, basée sur les annonces du routeur (RA) |
| **DHCPv6** | Attribution d'adresse IPv6 par un serveur DHCP dédié IPv6 (stateful) |
| **Link-local (fe80::/64)** | Adresse non routable, valable uniquement sur le lien local, auto-générée par chaque interface |
| **Adresse globale unicast** | Équivalent IPv6 d'une IP publique/privée routable (ex: `2001:db8::/32`, plage documentaire) |
| **Préfixe /64** | Taille standard d'un sous-réseau IPv6 (masque quasi systématique) |
| **Interface Alias / n° interface** | Identifiant utilisé par Windows/Linux pour lever l'ambiguïté quand une adresse link-local (fe80::) est présente sur plusieurs interfaces |
| **Réseau interne (VirtualBox)** | Mode réseau isolé reliant uniquement les VM entre elles, sans accès à l'hôte ni à Internet |

---

## 5. Points essentiels à retenir pour l'examen

1. **Même préfixe = communication directe possible** ; préfixes différents → échec sans routeur.
2. **Link-local est automatique et toujours présente**, même sans configuration — c'est le socle minimal d'IPv6 sur un lien.
3. **`%<interface>` ou `-I <interface>`** est indispensable pour cibler une adresse link-local en ping (ambiguïté car fe80:: peut exister sur plusieurs interfaces).
4. Sur Debian, `inet6 manual` = laisser IPv6 en mode link-local seul (pas de SLAAC/DHCPv6/statique).
5. Sur Windows, désactiver le DHCPv6 (`Set-NetIPInterface -Dhcp Disabled`) ne désactive pas IPv6, seulement l'auto-configuration stateful — la link-local reste active.
6. **Conclusion pédagogique du TP** : on peut établir une connectivité IPv6 fonctionnelle **sans configurer aucune adresse manuellement**, contrairement à IPv4.
