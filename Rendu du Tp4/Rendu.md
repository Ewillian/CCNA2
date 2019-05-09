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

| Routeur             | `10.33.10.0`   | `10.33.20.0`   | `10.33.30.0`   |
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
| `router1.menu3.tp4`  | `10.33.20.254` |

Vlan30:

| Hosts               | 10.33.30.0   |
| :------------------ | :----------- |
| `server1.menu3.tp4` | `10.33.30.1` |
| `server2.menu3.tp4` | `10.33.30.2` |
| `server3.menu3.tp4` | `10.33.30.3` |
| `server4.menu3.tp4` | `10.33.30.4` |
| `router1.menu3.tp4` | `10.33.30.5` |



## 1 Configuration des clients et serveurs

On configure les hosts de chaque machine

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



La même chose sur les VPCS

```
rh1> ip 10.33.20.2 /24
Checking for duplicate address...
PC1 : 10.33.20.2 255.255.255.0
```



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



## 2 Configuration des Switchs

- On configure les Switchs ainsi que leurs Vlans

```
IOU6#conf t
//Création du Vlan 30
IOU6(config)#vlan 30
IOU6(config-vlan)#name server-network
IOU6(config-vlan)#exit
//Ajout de chaques interfaces liées à une machine virtuelle dans le vlan
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



On fait la même chose pour les Vlans 20 et 10.

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



# 3 Router-on-a-stick

On commence par configurer le Switch lié au routeur:

``````
IOU2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
// Création Vlan
IOU2(config)#vlan 10
IOU2(config-vlan)#name client-network
IOU2(config-vlan)#exit
// Intégre Interface dans Vlan
IOU2(config)#interface Ethernet 0/0
IOU2(config-if)#switchport mode access
IOU2(config-if)#switchport access vlan 10
IOU2(config-if)#exit
// Mode trunk entre Switch et Routeur + Autorise paquets vlan précisé
(config)# interface Ethernet 0/1
(config-if)# switchport trunk encapsulation dot1q
(config-if)# switchport mode trunk
(config-if)#switchport trunk allowed vlan add 10
``````



Quant au routeur :

``````
//Déninition des ip des sous interfaces
R1(config)#interface fastEthernet 0/0.10
R1(config-subif)#encapsulation dot1q 10
R1(config-subif)#ip address 10.33.10.254 255.255.255.0
R1(config-subif)#no shut
R1(config-subif)#exit
``````



et les clients :

``````
//VPCS ajout Gateway
rh1> ip 10.33.20.2 /24 10.33.20.254
Checking for duplicate address...
PC1 : 10.33.20.2 255.255.255.0 gateway 10.33.20.254

//CENTOS
[ewillian@pro1 ~]$ cat /etc/sysconfig/network-scripts/ifcfg-enp0s3
DEVICE=enp0s3
NAME=enp0s3
BOOTPROTO=static
ONBOOT=yes
IPADDR=10.33.10.1
NETMASK=255.255.255.0
GATEWAY=10.33.10.254
``````



A ce stade tous le monde peut se ping.

``````
rh1> trace 10.33.10.1
trace to 10.33.10.1, 8 hops max, press Ctrl+C to stop
 1   10.33.20.254   7.597 ms  8.787 ms  8.841 ms
 2   *10.33.10.1   18.642 ms 
``````



``````
rh1> trace 10.33.30.3
trace to 10.33.30.3, 8 hops max, press Ctrl+C to stop
 1   10.33.20.254   8.848 ms  9.811 ms  10.392 ms
 2   *10.33.30.3   15.672 ms 
``````



# 4 Limiter l'accès aux serveur

Il est demandé dans ce menu de donner l'accès aux rh et aux pro à 2 serveurs chacun. 

**Première solution** trouvé,  l'option `port-security` du `switchport mode access`.

sont utilisation est la suivante :

```
Switch(config)#interface Ethernet 0/0
Switch(config-if)#switchport mode access
Switch(config-if)#switchport port-security
Switch(config-if)#switchport port-security mac-address [MAC ADDRESS]
```

Autrement dit elle permet d'accepter les paquets venant d'une addresse MAC définie.

Elle permet aussi au Switch de faire une action si il y a tentative d'accès.

```
Switch#Configure terminal
Switch(config)#interface Ethernet 0/3
Switch(config-if)#switchport mode access
Switch(config-if)#switchport port-security violation [METHODE]
```

- **Shudown** : Elle désactive l’interface lorsque qu’il y a violation.
- **Protect** : Toutes les trames ayant des adresses MAC sources inconnues sont bloquées et les autres autorisées.
- **Restrict** : Alerte SNMP envoyée et le compteur de violation est incrémenté.

Il y a un autre détail qu'il faut prendre en compte, par défaut, il est possible d’autoriser une seule adresse MAC sur chacun des ports. Pour y remédier on peut utiliser:

```
switchport port-security maximum x
```

Mais bon ça prend beaucoup de temps à mettre en place.



**Seconde solution** [ACL](https://routeur.clemanet.com/acl-cisco.php) ou Access Control List



Ce que l'on veut c'est interdire la communication entre le Vlan 20 et les serveurs qui ne leurs sont pas attribués.

``````
//On Authorise Admin à accéder au server
R1(config)#access-list 131 permit icmp host 10.33.20.1 host 10.33.30.3
// Interdit l'accès au vlan 20 (10.33.0.0 /24) vers serveur3 (10.33.30.3 /24)
R1(config)#access-list 131 deny icmp 10.33.20.0 0.0.0.255 10.33.30.3 0.0.0.255
// On authorise tout autre trafic
R1(config)#access-list 131 permit icmp any any
// On selectionne l'interface concernée
R1(config)#interface fastEthernet 0/2.30
// On lui indique l'access-list en règle
R1(config-subif)#ip access-group 131 in
R1(config-subif)#exit
``````

Ce qui donne :

``````
R1#show access-lists
Extended IP access list 131
    10 permit icmp host 10.33.20.1 host 10.33.30.3 (6 matches)
    20 deny icmp 10.33.20.0 0.0.0.255 10.33.30.0 0.0.0.255
    30 permit icmp any any (10 matches)
``````

On fait de même avec l'autre serveur.

On teste !

``````
rh2> ping 10.33.30.3
*10.33.20.254 icmp_seq=1 ttl=255 time=9.549 ms (ICMP type:3, code:13, Communication administratively prohibited)
*10.33.20.254 icmp_seq=2 ttl=255 time=2.859 ms (ICMP type:3, code:13, Communication administratively prohibited)
*10.33.20.254 icmp_seq=3 ttl=255 time=5.187 ms (ICMP type:3, code:13, Communication administratively prohibited)
``````

``````
pro7> ping 10.33.30.3
10.33.30.3 icmp_seq=1 timeout
10.33.30.3 icmp_seq=2 timeout
84 bytes from 10.33.30.3 icmp_seq=3 ttl=63 time=21.654 ms
84 bytes from 10.33.30.3 icmp_seq=4 ttl=63 time=18.249 ms
84 bytes from 10.33.30.3 icmp_seq=5 ttl=63 time=20.393 ms
``````

Tout fonctionne !

Après configuration de l'ensemble de l'infrastructure, Admin peut accéder à tout le monde en le précisant que c'est une exception, pro et RH joignent les imprimantes et 2 serveurs !



# 5 Limiter le débit





# 6 Le VPN





# 7 firewall frontal



# 8 serveur web redondé, sécurisé

