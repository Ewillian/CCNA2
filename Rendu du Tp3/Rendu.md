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

Même chose sur l'interface 0/2 en remplaçant **vlan 10** par **vlan 20**.

- Définir Trunk

``````
(config)# interface Ethernet 0/0
(config-if)# switchport trunk encapsulation dot1q
(config-if)# switchport mode trunk
``````

On effectue ces commandes sur les deux switch (vu qu'ils utilisent interface 0/0 tous les deux rien à modifier :smile: !!)