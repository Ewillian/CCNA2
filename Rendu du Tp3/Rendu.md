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

