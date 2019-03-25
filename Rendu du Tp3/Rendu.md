# # B2 Réseau 2018 - TP3
## ## I. Manipulation de switches et de VLAN

Oublié de prendre des captures quelques étapes, je les remplace par ce qu'il faut faire :sweat_smile:.

![Capture1](https://github.com/Ewillian/CCNA2/blob/master/Rendu%20du%20Tp3/captures/Capture1.png?raw=true)

Après avoir configuré les client (changer l'ip statique, etc...), toutes les machines se **ping** et on peut commencer à faire la configuration voulu.

Tout d'abord, on configure **IOU1** (ou **switch N°1**) pour définir une **VLAN 10** sur **client1** (ou interface 0/1), un **VLAN 20** sur **client2** (ou interface 0/2) et le **mode Trunk** sur son **interface 0/0.**

- Définir VLAN10

``````
# conf t
(config)# vlan 10
(config-vlan)# name client-network
(config-vlan)# exit
(config)# interface Ethernet 0/0
(config-if)# switchport mode access
(config-if)# switchport access vlan 10
``````

Même chose sur l'interface 0/2 en remplaçant **vlan 10** par **vlan 20** (**Switch n°1**).

On réutilise ses commandes pour mettre **client3** dans le **Vlan 10** (**Switch n°2**).

- Définir Trunk

``````
(config)# interface Ethernet 0/0
(config-if)# switchport trunk encapsulation dot1q
(config-if)# switchport mode trunk
``````

On effectue ces commandes sur les **deux switch** (vu qu'ils utilisent interface 0/0 tous les deux rien à modifier :smile: !!)

Et voila !! Plus qu'a ping !!

![Ping client 1 <-> 3 !! 2](https://github.com/Ewillian/CCNA2/blob/master/Rendu%20du%20Tp3/captures/Capture2.png?raw=true)

Avec ces **Ping **, on peut voir que client1 et client3 communiquent sans problème mais vu que client2 est dans un **Vlan différent** (Vlan 10 != Vlan20) des deux autres, **il ne peut échanger avec eux**.



## II. Manipulation simple de routeurs

![Routeur Config](https://github.com/Ewillian/CCNA2/blob/master/Rendu%20du%20Tp3/captures/Capture3.png?raw=true)

Après configuration de bases (Changer nom de domaine, ip static, etc....) pour **les clients** et **server1**, il faut **configurer les routeurs R1 et R2**.

- Configuration Routeur Cisco C3650

  ``````
  # conf t
  (config)# interface ethernet <NUMERO>
  (config-if)# ip address <IP> <MASK>
  (config-if)# no shut
  (config-if)# exit
  (config)# exit
  # show ip int br
  ``````

  Ce sont basiquement les commandes qu'il faut effectuer sur chaque interface pour définir les différentes ip des interface.

  Par exemple pour **R1** dans le réseau **10.2.1.0/24** vers **client2**:

  ``````
  //Mode configuration
  # conf t
  //Selection de la carte
  (config)# interface ethernet <2/0>
  //Définition de l'ip et mask
  (config-if)# ip address <10.2.1.254> <255.255.255.0>
  //Allume la carte
  (config-if)# no shut
  //On quitte config interface
  (config-if)# exit
  //On quitte mode config
  (config)# exit
  //On  affiche les ip
  # show ip int br
  ``````

  On fait la même chose pour toutes les **interfaces connectés** :heavy_exclamation_mark::heavy_exclamation_mark: **​sauf celle vers client1** :heavy_exclamation_mark::heavy_exclamation_mark:.

  On fait les pings !!

  ![Ping2](https://github.com/Ewillian/CCNA2/blob/master/Rendu%20du%20Tp3/captures/Captures4.png?raw=true)

**Tous le monde peut communiquer sauf client1 car il n'a pas de passerelle !!!**

Pour que tout le monde puisse communiquer, il faudrait ajouter un Switch entre R1 et client1 / client2.

![Config imma](https://github.com/Ewillian/CCNA2/blob/master/Rendu%20du%20Tp3/captures/Cpature5.png?raw=true)

## III. Mise en place d'OSPF

![Config3](https://github.com/Ewillian/CCNA2/blob/master/Rendu%20du%20Tp3/captures/Capture8.png?raw=true)

Après configuration de bases (Changer nom de domaine, ip static, etc....), il faut définir toutes les routes sur tout les routers:

- Ajouter une route statique

``````
# conf t
(config)# ip route <REMOTE_NETWORK_ADDRESS> <MASK> <GATEWAY_IP> 
(config)# exit
# show ip route
``````

Après installation voici le résultat :

```
R1#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            10.3.100.1      YES manual up                    up
FastEthernet1/0            10.3.100.22     YES manual up                    up
FastEthernet2/0            10.3.102.254    YES manual up                    up
```

```
R2#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            10.3.100.2      YES manual up                    up
FastEthernet1/0            10.3.100.5      YES manual up                    up
```

```
R3#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            10.3.100.6      YES manual up                    up
FastEthernet1/0            10.3.100.9      YES manual up                    up
```

```
R4#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            10.3.100.10     YES manual up                    up
FastEthernet1/0            10.3.100.13     YES manual up                    up
FastEthernet2/0            10.3.101.254    YES manual up                    up
```

```
R5#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            10.3.100.14     YES manual up                    up
FastEthernet1/0            10.3.100.17     YES manual up                    up
```

```
R6#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            10.3.100.18     YES manual up                    up
FastEthernet1/0            10.3.100.21     YES manual up                    up
```

Donc après configuration, chaque routeur peut ping son voisin:

``````
R1#ping 10.3.102.10

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.102.10, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 36/43/52 ms
R1#ping 10.3.100.21

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.100.21, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 32/57/64 ms
R1#ping 10.3.100.2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.100.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 32/47/68 ms

------

R2#ping 10.3.100.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.100.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 64/66/68 ms
R2#ping 10.3.100.6

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.100.6, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 64/72/100 ms

------


R3#ping 10.3.100.5

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.100.5, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 64/67/72 ms
R3#ping 10.3.100.10

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.100.10, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 64/72/100 ms
R4#ping 10.3.100.9

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.100.9, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 64/66/68 ms

------

R4#ping 10.3.100.14

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.100.14, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 64/71/92 ms
R4#ping 10.3.101.10

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.101.10, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 36/44/60 ms


------

R5#ping 10.3.100.13

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.100.13, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 32/47/64 ms
R5#ping 10.3.100.18

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.100.18, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 64/66/68 ms

------

R6#ping 10.3.100.17

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.100.17, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 36/59/96 ms
R6#ping 10.3.100.22

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.100.22, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 32/40/64 ms
``````

Maintenant il fauuuuuut:

- Configurer le OSPF

Autrement dit:

``````
//Passer en mode configuration
# conf t
Activer OSPF
(config)# router ospf <ID OSPF>
(config-router)# router-id 1.1.1.1
(config-router)# network <NETWORK ADDRESS> <NETWORK MASK> area <ID AREA>
<=> (config-router)# network 10.6.100.0 0.0.0.3 area 0
//0.0.0.3 == 255.255.255.252
//0.0.0.255 == 255.255.255.0
//En gros l'inverse du mask habituel

//pour vérifier l'état OSPF
# show ip protocols
# show ip ospf interface
# show ip ospf neigh
# show ip ospf border-routers
``````

Après avoir partagé toutes les routes avec tous le monde et avoir mis la route par défaut du client et du serveur, testons au hasard 3 pings.

```
R6#ping 10.3.100.6

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.100.6, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 32/46/64 ms
```

```
R2#ping 10.3.101.10

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.101.10, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 16/38/64 ms
```

![Ping3](https://github.com/Ewillian/CCNA2/blob/master/Rendu%20du%20Tp3/captures/Capture6.png?raw=true)

Autrement dit l'arborescence fonctionne !!!

Bien testons ping de client1 vers server1

![Schéma](https://github.com/Ewillian/CCNA2/blob/master/Rendu%20du%20Tp3/captures/Capture7.png?raw=true)

Tout fonctionne :ok_hand: !!

## IV. Lab Final

**Topologie**

![Schéma](https://github.com/Ewillian/CCNA2/blob/master/Rendu%20du%20Tp3/captures/TopologieMK1.png?raw=true)

- Routeurs:

| Hosts              | `10.3.100.0/30` | `10.3.100.4/30` | `10.3.100.8/30`  | `10.3.100.12/30` | `10.33.10.0/24` |
| ------------------ | --------------- | --------------- | ---------------- | ---------------- | --------------- |
| `router1.lab3.tp4` | `10.3.100.1/30` | x               | x                | `10.3.100.14/30` | `10.33.10.254`  |
| `router2.lab3.tp4` | `10.3.100.2/30` | `10.3.100.5/30` | x                | x                | x               |
| `router3.lab3.tp4` | x               | `10.3.100.6/30` | `10.3.100.9/30`  | x                | x               |
| `router4.lab3.tp4` | x               | x               | `10.3.100.10/30` | `10.3.100.13/30` | x               |

- AREA 1:

| Hosts              | `10.33.10.0/24` |
| ------------------ | --------------- |
| `client1.lab3.tp4` | `10.33.10.1`    |
| `client2.lab3.tp4` | `10.33.10.2`    |
| `server1.lab3.tp4` | `10.33.10.3`    |
| `client3.lab3.tp4` | `10.33.20.4`    |
| `client4.lab3.tp4` | `10.33.20.5`    |
| `server2.lab3.tp4` | `10.33.20.6`    |
