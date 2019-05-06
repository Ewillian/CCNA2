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

![Topologie Rendu 4](https://github.com/Ewillian/CCNA2/blob/master/Rendu%20du%20Tp4/captures/topologie1.png?raw=true)



Routeurs :

| Routeurs            | `10.33.10.0`   | `10.33.20.0`   | `10.33.30.0`   |
| ------------------- | -------------- | -------------- | -------------- |
| `router1.menu3.tp4` | `10.33.10.254` | `10.33.20.254` | `10.33.30.254` |



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
| `3router1.menu3.tp4` | `10.33.10.254` |

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



## 1 Configuration des clients et serveurs

Exemple avec pro1: 

On configure les hots de chaque machine

```
[ewillian@localhost ~]$ sudo echo 'pro1.menu3.tp4' | sudo tee /etc/hostname
```

Ensuite on configure l'IP statique 

```
[ewillian@pro1 ~]$ cat /etc/sysconfig/network-scripts/ifcfg-enp0s3
DEVICE=enp0s3
NAME=enp0s3
BOOTPROTO=static
ONBOOT=yes
IPADDR=10.33.10.1
NETMASK=255.255.255.0
```

On fait la même pour chaque machines virtuelles
Puis on vérifie si ça marche avec des pings

```
[ewillian@pro1 ~]$ ping 10.33.10.5
PING 10.33.10.5 (10.33.10.5) 56(84) bytes of data .
64 bytes from 10.33.10.5: icmp_seq=1 ttl=64 time=8.25 ms
64 bytes from 10.33.10.5: icmp_seq=2 ttl=64 time=0.664 ms
^C
--- 10.33.10.5 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 0.664/4.458/8.252/3.794 ms
```



## 2 Installation et configuration du routeur R1

On configure le routeur 1:

```
R1#conf t
R1(config)#interface fastEthernet 0/0.10
R1(config-subif)#encapsulation dot1q 10
R1(config-subif)#ip address 10.33.10.254 255.255.255.0
R1(config-subif)#exit
R1(config)#interface fastEthernet 0/0.20
R1(config-subif)#encapsulation dot1Q 20
R1(config-subif)#ip address 10.33.20.254 255.255.255.0
R1(config-subif)#exit
R1(config)#interface fastEthernet 0/0.30
R1(config-subif)#encapsulation dot1Q 30
R1(config-subif)#ip address 10.33.30.254 255.255.255.0
R1(config-subif)#exit
R1(config)#exit

R1#show ip int br
*Mar  1 00:28:50.247: %SYS-5-CONFIG_I: Configured from console by console
R1#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            unassigned      YES unset  up                    up
FastEthernet0/0.10         10.33.10.254    YES manual up                    up
FastEthernet0/0.20         10.33.20.254    YES manual up                    up
FastEthernet0/0.30         10.33.30.254    YES manual up                    up
FastEthernet1/0            unassigned      YES unset  administratively down down
FastEthernet2/0            unassigned      YES unset  administratively down down
FastEthernet3/0            unassigned      YES unset  administratively down down
```



## 3 Configuration des Switchs côté serveur 

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



On fait la même chose pour les Vlans 20 et 10



```
pro12> ping 10.33.10.11
84 bytes from 10.33.10.11 icmp_seq=1 ttl=64 time=0.470 ms
84 bytes from 10.33.10.11 icmp_seq=2 ttl=64 time=0.652 ms
^C
pro12> ping 10.33.10.1
84 bytes from 10.33.10.1 icmp_seq=1 ttl=64 time=0.847 ms
84 bytes from 10.33.10.1 icmp_seq=2 ttl=64 time=0.714 ms
^C
pro12> ping 10.33.10.6
84 bytes from 10.33.10.6 icmp_seq=1 ttl=64 time=1.265 ms
84 bytes from 10.33.10.6 icmp_seq=2 ttl=64 time=0.689 ms
^C
```

