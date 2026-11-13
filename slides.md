---
marp: true
theme: default
style: |
  section {background-color: #121114}
  h1,h2,h3 {color: #8393f0}
  p,ul,li,td,th {color: #ccc}
  section table {font-size: 0.85em}
  section table td {background-color: #121114}
  section table th {background-color: #272133; font-weight: bolder}
  pre {background-color: #17151a; color: #ccc}
  blockquote {border: 2px green solid; border-left-width: 15px; padding: 0.5em 15px;font-style: italic }
paginate: true
header: "Pare-feu, proxy et VPN"
footer: "![height:20px](https://raw.githubusercontent.com/DiginamicInternal/PublicAssets/refs/heads/main/Logo-diginamic-color-blk.png)"
---

# Pare-feu, proxy et VPN

### Garder les frontières du réseau

Nicolas SAHUC

---

## 🎯 Objectifs pédagogiques

1. Définir un pare-feu et ses types
2. Découper un réseau en zones, placer une DMZ
3. Construire une matrice de flux et des règles
4. Expliquer le NAT, le PAT, le DNAT
5. Distinguer stateless, stateful, applicatif, IDS, IPS
6. Présenter les types de proxy et l'inspection SSL
7. Comparer les protocoles VPN

---

## Le parcours

| #  | Étape                                  | Durée  |
|----|----------------------------------------|--------|
| 01 | Le pare-feu                            | 1 h    |
| 02 | Matrices de flux et règles de filtrage | 1 h 30 |
| 03 | Le NAT                                 | 1 h    |
| 04 | Inspection, IDS, IPS                   | 1 h    |
| 05 | Le proxy                               | 1 h    |
| 06 | Le VPN                                 | 1 h 30 |

---

# 1. Le pare-feu

---

## Définition

Contrôler et filtrer les connexions **entrantes et sortantes** : surveiller, puis **autoriser ou bloquer**.

| Type | En bref |
|---|---|
| Matériel | boîtier dédié entre deux réseaux |
| Logiciel | protège une seule machine |
| Virtuel | VM placée entre deux réseaux |
| NGFW | + inspection approfondie, filtrage de contenu |
| ALG | modules par protocole (FTP, SIP…) |
| Cloud | filtre avant l'infrastructure |

---

## Zones et DMZ

- Le réseau est découpé en **zones** : WAN, LAN, serveurs, DMZ
- La **DMZ** héberge ce qui est exposé à Internet : web, mail, DNS public
- La DMZ n'accède **pas librement** au LAN

> Si un serveur exposé tombe, l'attaquant reste enfermé dans la DMZ.

---

# 2. Matrices de flux et règles

---

## La matrice de flux

| FROM ＼ TO | WAN | SERVEURS | LAN | DMZ |
|---|---|---|---|---|
| WAN | — | aucun | OpenVPN | HTTP(S), SMTP |
| SERVEURS | DNS, HTTP(S) | tous | aucun | aucun |
| LAN | tous | DNS, LDAP, SSH, RDP… | tous | HTTP(S), SSH, RDP |
| DMZ | DNS, HTTP(S), SMTP | aucun | aucun | tous |

> Tout flux absent de la matrice est interdit.

---

## Une règle de pare-feu

- IP source · IP destination
- Protocole · port source · port destination
- Action : autoriser, refuser, ignorer
- **Priorité** : évaluation de haut en bas, la première qui correspond gagne

Dernière règle : **tout refuser**.

---

## Types de règles

- Blocage · Autorisation
- Redirection
- NAT
- Détection d'intrusion
- Filtrage d'URL

---

# 3. Le NAT

---

## Pourquoi le NAT ?

- Peu d'adresses IPv4 publiques, beaucoup d'équipements
- Plusieurs machines partagent **une** adresse publique
- Effet de bord : les adresses privées sont **masquées**

Une **table de traduction** mémorise chaque connexion.

---

## Les types de NAT

| Type | Correspondance |
|---|---|
| Statique | 1 privée ⇄ 1 publique, permanent |
| Dynamique | n privées ⇄ pool de publiques |
| PAT | n privées ⇄ 1 publique, distinguées par le **port** |
| DNAT | port public ⇄ serveur interne |

---

# 4. Inspection, IDS, IPS

---

## Trois niveaux d'inspection

| | Stateless | Stateful | Applicatif |
|---|---|---|---|
| Couches | 3-4 | 3-4 | 3 à 7 |
| Suit les connexions | non | oui | oui |
| Voit le contenu | non | non | oui |

---

## IDS et IPS

- **IDS** : détecte et **alerte** — passif, un humain agit
- **IPS** : détecte et **bloque** — actif, automatique
- Variantes **réseau** (NIDS, NIPS) et **hôte** (HIDS, HIPS)
- Intégrés au pare-feu : **UTM**

> Les signatures doivent être mises à jour en permanence.

---

# 5. Le proxy

---

## Rôles et types

Intermédiaire, filtre, cache, masque l'IP des clients.

| Type | Principe |
|---|---|
| Explicite | configuré dans le navigateur |
| Transparent | sur le chemin, invisible, incontournable |
| Anonyme | masque l'IP (≠ VPN : pas de chiffrement) |
| Cache | sert le contenu déjà consulté |
| Reverse | devant les serveurs web de l'entreprise |

---

## HTTPS et inspection SSL

- En HTTPS, le proxy ne voit pas le contenu
- **Inspection SSL** : un « homme du milieu » assumé
- Le certificat du proxy est déployé sur tous les postes
- Deux tunnels TLS : poste ⇄ proxy ⇄ serveur

Filtrage : **liste blanche**, **liste noire**, **catégories**

---

# 6. Le VPN

---

## Le tunnel

Encapsulation → transmission → désencapsulation, **chiffrée** de bout en bout

- **VPN d'accès** : nomades
- **Intranet VPN** : site à site
- **Extranet VPN** : partenaires

---

## Les protocoles

| Protocole | Niveau | À retenir |
|---|---|---|
| PPTP | 2 | chiffrement cassé : à proscrire |
| L2TP | 2 | tunnel sans chiffrement → + IPsec |
| IPsec | 3 | AH (intégrité) + ESP (chiffrement) |
| MPLS | 2,5 | cloisonnement opérateur, sans chiffrement |
| OpenVPN | 4+ | SSL/TLS, open source |
| WireGuard | 3 | moderne, rapide |

---

# Des questions ?

Pour toute question [adressez-vous au service pédagogie](mailto:pedagogie@diginamic.fr)
