# B2 Réseau 2018 - TP4
## Menu 3 : Infra small/medium office

[Consignes](https://github.com/It4lik/B2-Reseau-2018/tree/master/tp/4#menu-3--infra-smallmedium-office)

- **Installations de base**
  - Centos
  - Router
  - Switch
- **Configurations**

  - ~20 personnes pro
  - 3 RH
  - 1 admin
  - 5 serveurs
  - 5 imprimantes
- Récap Besoin

  - Chaque Pro et RH joignent les imprimantes et 2 serveurs.
  - L'administrateur joint tout le monde.
  - Débit descendant WAN exigé
    - Administrateur, Pro, RH : 5Mo/sec
    - Serveur : 50Mo/sec
  - VPN pour se connecter à distance (Openvpn).
  - Firewall frontal (pfsense/opensense).
  - Serveur Web redondé, sécurisé (nginx, haproxy, keepalived, etc...).




## Topologie

Image Topo

| Routeurs           | `10.3.100.0/30` | `10.3.100.4/30` | `10.3.100.8/30`  | `10.3.100.12/30` |
| ------------------ | :-------------: | :-------------: | :--------------: | :--------------: |
| `router1.lab3.tp4` | `10.3.100.1/30` |       `X`       |       `X`        | `10.3.100.14/30` |
| `router2.lab3.tp4` | `10.3.100.2/30` | `10.3.100.5/30` |       `X`        |       `X`        |
| `router4.lab3.tp4` |       `X`       | `10.3.100.6/30` | `10.3.100.9/30`  |       `X`        |
| `router3.lab3.tp4` |       `X`       |       `X`       | `10.3.100.10/30` | `10.3.100.13/30` |

