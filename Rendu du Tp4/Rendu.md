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

![Topologie Rendu 4](https://github.com/Ewillian/CCNA2/blob/master/Rendu%20du%20Tp4/captures/Topologie.png?raw=true)



Routeurs :

| Routeurs            | `10.3.100.0/30` | `10.3.100.4/30` | `10.3.100.8/30`  | `10.3.100.12/30` | `10.33.10.0`   | `10.33.20.0` | `10.33.30.0` |
| ------------------- | :-------------: | :-------------: | :--------------: | :--------------: | -------------- | ------------ | ------------ |
| `router1.menu3.tp4` | `10.3.100.1/30` |       `X`       |       `X`        | `10.3.100.14/30` | `10.33.10.254` |              |              |
| `router2.menu3.tp4` | `10.3.100.2/30` | `10.3.100.5/30` |       `X`        |       `X`        |                |              |              |
| `router3.menu3.tp4` |       `X`       | `10.3.100.6/30` | `10.3.100.9/30`  |       `X`        |                |              |              |
| `router4.menu3.tp4` |       `X`       |       `X`       | `10.3.100.10/30` | `10.3.100.13/30` |                |              |              |



Vlan10 :

| Hosts                | 10.33.10.0     |
| -------------------- | -------------- |
| `pro1.menu3.tp4`     | `10.33.10.1`   |
| `pro2.menu3.tp4`     | `10.33.10.2`   |
| `pro3.menu3.tp4`     | `10.33.10.3`   |
| `pro4.menu3.tp4`     | `10.33.10.4`   |
| `pro5.menu3.tp4`     | `10.33.10.5`   |
| `pro6.menu3.tp4`     | `10.33.10.6`   |
| `pro7.menu3.tp4`     | `10.33.10.7`   |
| `pro8.menu3.tp4`     | `10.33.10.8`   |
| `pro9.menu3.tp4`     | `10.33.10.9`   |
| `pro10.menu3.tp4`    | `10.33.10.10`  |
| `pro11.menu3.tp4`    | `10.33.10.11`  |
| `pro12.menu3.tp4`    | `10.33.10.12`  |
| `pro13.menu3.tp4`    | `10.33.10.13`  |
| `pro14.menu3.tp4`    | `10.33.10.14`  |
| `pro15.menu3.tp4`    | `10.33.10.15`  |
| `printer1.menu3.tp4` | `10.33.10.16`  |
| `printer2.menu3.tp4` | `10.33.10.17`  |
| `printer3.menu3.tp4` | `10.33.10.18`  |
| `router1.menu3.tp4`  | `10.33.10.254` |

Vlan20:

| Hosts                | 10.33.20.0     |
| -------------------- | -------------- |
| `admin.menu3.tp4`    | `10.33.20.1`   |
| `rh1.menu3.tp4`      | `10.33.20.2`   |
| `rh2.menu3.tp4`      | `10.33.20.3`   |
| `rh3.menu3.tp4`      | `10.33.20.4`   |
| `printer4.menu3.tp4` | `10.33.20.5`   |
| `printer5.menu3.tp4` | `10.33.20.6`   |
| `router2.menu3.tp4`  | `10.33.20.254` |

Vlan30:

| Hosts               | 10.33.30.0   |
| :------------------ | :----------- |
| `server1.menu3.tp4` | `10.33.30.1` |
| `server2.menu3.tp4` | `10.33.30.2` |
| `server3.menu3.tp4` | `10.33.30.3` |
| `server4.menu3.tp4` | `10.33.30.4` |
| `server5.menu3.tp4` | `10.33.30.5` |



## Installation et configuration des routeurs

- On commence par définir pour chaque interfaces une Ip Statique:

````
// Entre en mode configuration
# conf t
//Selection interface
(config)# interface ethernet <NUMERO>
//Definition de l'adresse ip
(config-if)# ip address 10.5.1.254 255.255.255.0
//Lancement de l'interface
(config-if)# no shut
//Exit
(config-if)# exit
(config)# exit
````

`Exemple:`

```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface FastEthernet 0/0
R1(config-if)#ip address 10.3.100.1 255.255.255.252
R1(config-if)#exit
R1(config)#exit
R1#
*Mar  1 00:13:22.895: %LINK-3-UPDOWN: Interface FastEthernet0/0, changed state to up
*Mar  1 00:13:23.387: %SYS-5-CONFIG_I: Configured from console by console
*Mar  1 00:13:23.895: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/0, changed state to up
R1#
```

Ensuite, on teste les pings:

On s'attend à ceci :

`R1<->R2<->R3<->R4<->R1`



`R1 <--> R2`

```
R1#ping 10.3.100.2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.100.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 32/33/40 ms

------

R2#ping 10.3.100.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.100.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 32/54/76 ms
```



`R2 <--> R3`

```
R2#ping 10.3.100.6

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.100.6, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 32/39/64 ms

------

R3#ping 10.3.100.5

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.100.5, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 16/24/40 ms
```



`R3 <--> R4`

``````
R3#ping 10.3.100.10

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.100.10, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 44/61/68 ms
R3#

------

R4#ping 10.3.100.9

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.100.9, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 32/34/36 ms
``````



`R4 <--> R1`

``````
R4#ping 10.3.100.14

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.100.14, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 32/48/76 ms

------

R1#ping 10.3.100.13

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.3.100.13, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 32/59/68 ms
``````



[x] Voilà ! Tout fonctionne ! :heart_eyes: :blush: :ok_hand:



## 2 Configuration des Switchs côté serveur 

- On configure le Vlan 30 présent dans la salle des serveurs

D'abord on configure le switch,

```
IOU6#conf t
IOU6(config)#vlan 30
IOU6(config-vlan)#name server-network
IOU6(config-vlan)#exit
IOU6(config)#interface Ethernet 0/1
IOU6(config-if)#switchport mode access
IOU6(config-if)#switchport access vlan 30
IOU6(config-if)#interface Ethernet 0/2
IOU6(config-if)#switchport mode access
IOU6(config-if)#switchport access vlan 30
IOU6(config-if)#interface Ethernet 0/3
IOU6(config-if)#switchport mode access
IOU6(config-if)#switchport access vlan 30
IOU6(config-if)#interface Ethernet 1/0
IOU6(config-if)#switchport mode access
IOU6(config-if)#switchport access vlan 30
IOU6(config-if)#interface Ethernet 1/1
IOU6(config-if)#switchport mode access
IOU6(config-if)#switchport access vlan 30
IOU6(config-if)#exit
IOU6(config)#exit
```

Puis on vérifie si ça fonctionne

`Server4 <--> Server 5`

```
VPCS> ping 10.33.30.4
84 bytes from 10.33.30.4 icmp_seq=1 ttl=64 time=0.202 ms
84 bytes from 10.33.30.4 icmp_seq=2 ttl=64 time=0.331 ms
84 bytes from 10.33.30.4 icmp_seq=3 ttl=64 time=0.310 ms
84 bytes from 10.33.30.4 icmp_seq=4 ttl=64 time=0.358 ms
^C
VPCS> ping 10.33.30.5
84 bytes from 10.33.30.5 icmp_seq=1 ttl=64 time=0.198 ms
84 bytes from 10.33.30.5 icmp_seq=2 ttl=64 time=0.289 ms
84 bytes from 10.33.30.5 icmp_seq=3 ttl=64 time=0.384 ms
84 bytes from 10.33.30.5 icmp_seq=4 ttl=64 time=0.302 ms
^C
```

```
VPCS> show ip

NAME        : VPCS[1]
IP/MASK     : 10.33.30.3/24
```



Ensuite on configure les 2 serveurs CentOS

exemple :

```
[ewillian@server2 network-scripts]$ ping 10.33.30.5
PING 10.33.3..5 (10.33.30.5) 56(84) bytes of data.
64 bytes from 10.33.30.5: icmp-seq_1 ttl=64 time=1.12 ms
64 bytes from 10.33.30.5: icmp-seq_2 ttl=64 time=1.56 ms
64 bytes from 10.33.30.5: icmp-seq_3 ttl=64 time=1.00 ms
64 bytes from 10.33.30.5: icmp-seq_4 ttl=64 time=0.937 ms
^C
--- 10.33.30.5 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3004ms
rtt min/avg/max/mdev = 0.937/1.158/1.565/0.245 ms
```



## Installation et configuration Vlans 

