## README.md

## RENDU TP 2

# **I.Mise en place de la VM**

`

 **- I.Routage Statique**

  

 

*- Ajouts des routes statiques :*

    
  -  Sur router1:

    >     > [lilou@router1 network-scripts]$ ip route show
    >     > 10.2.12.0/29 dev enp0s9 proto kernel scope link src 10.2.12.2 metric 102 [lilou@router1 network-scripts]$    `

   -  Sur client1:

  

    >         [lilou@client1 ~]$ cd /etc/sysconfig/network-scripts/
    >         [lilou@client1 network-scripts]$ sudo cat /etc/sysconfig/network-scripts/route-enp0s8
    >         [sudo] Mot de passe de lilou :
    >         10.2.2.0/24 via 10.2.1.254 dev enp0s8
    >         [lilou@client1 network-scripts]$`
    >          `

     


   -  Sur router2:

    > [lilou@router2 network-scripts]$ ip route show
    >  10.2.2.0/24 dev enp0s8 proto kernel scope link src 10.2.2.254 metric 100
    > 10.2.12.0/29 dev enp0s9 proto kernel scope link src 10.2.12.3 metric 101

  
   -  Sur server1:

    > [lilou@server1 ~]$ cd /etc/sysconfig/network-scripts/
    > [lilou@server1 network-scripts]$
    > [lilou@server1 network-scripts]$ sudo cat  /etc/sysconfig/network-scripts/route-enp0s8
    > [sudo] Mot de passe de lilou :
    > 10.2.1.0/24 via 10.2.2.254 dev enp0s8
    > [lilou@server1 network-scripts]$

 
 
  

   - On ping :
   
    >  [ewillian@client1 ~]$ ping 10.2.2.10 PING 10.2.2.10 (10.2.2.10)
    > 56(84) bytes of data. 64 bytes from 10.2.2.10: icmp_seq=1 ttl=62
    > time=1.33 ms 64 bytes from 10.2.2.10: icmp_seq=2 ttl=62 time=2.12 ms
    > ^C --- 10.2.2.10 ping statistics --- 2 packets transmitted, 2
    > received, 0% packet loss, time 1008ms rtt min/avg/max/mdev =
    > 1.337/1.730/2.123/0.393 ms

***Conclusion :*** Router 1 ne peut pas ping server 1 || Router 2 ne peut pas ping client 1 car il ne connaissent pas de route pour leurs envoyer des paquets.


 - **II. Visualitation du routage avec Wireshark**


![enter image description here](https://github.com/Ewillian/CCNA2/blob/master/screens/Screenshot_3.png)

Elles ont des adresses mac différentes





# II. NAT et services d'infra

**- Mise en place du NAT**

    > [ewillian@client1 ~]$ curl https://www.google.com <!doctype html><html
    > itemscope="" itemtype="http://schema.org/WebPage"
    > lang="fr"><head><meta content="text/html; charset=UTF-8"
    > http-equiv="Content-Type"><meta
    > content="/images/branding/googleg/1x/googleg_standard_color_128dp.png"
    > 
    > [ewillian@router2 network-scripts]$ curl https://www.google.com
    > <!doctype html><html itemscope="" itemtype="http://schema.org/WebPage"
    > lang="fr"><head><meta content="text/html; charset=UTF-8"
    > http-equiv="Content-Type"><meta
    > content="/images/branding/googleg/1x/googleg_standard_color_128dp.png"
    > itemprop="image"><title>Google</title>
    > 
    > [ewillian@server1 ~]$ curl https://www.google.fr curl: (6) Could not
    > resolve host: www.google.fr; Erreur inconnue

 **- DHCP server**

    > enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast
    > state UP group default qlen 1000 link/ether 08:00:27:6e:05:94 brd
    > ff:ff:ff:ff:ff:ff inet 10.2.1.50/24 brd 10.2.1.255 scope global
    > noprefixroute dynamic enp0s8 valid_lft 511sec preferred_lft 511sec
    > inet6 fe80::a00:27ff:fe6e:594/64 scope link valid_lft forever
    > preferred_lft forever
    > 
    > [ewillian@client ~]$ ip r s default via 10.0.2.2 dev enp0s3 proto dhcp
    > metric 100 default via 10.2.1.254 dev enp0s8 proto dhcp metric 101
    > 10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 100 10.2.1.0/24 dev enp0s8 proto kernel scope link src 10.2.1.50
    > metric 101

 **-  NTP server**


>     > [ewillian@router1 ~]$ chronyc tracking Reference ID : 5B795BA7
>     > (cluster009.linocomm.net) Stratum : 3 Ref time (UTC) : Mon Mar 04
>     > 16:16:35 2019 System time : 160.189910889 seconds slow of NTP time
>     > Last offset : +0.000494975 seconds RMS offset : 43.400646210 seconds
>     > Frequency : 10.476 ppm fast Residual freq : +4.807 ppm Skew : 22.295
>     > ppm Root delay : 0.036527529 seconds Root dispersion : 0.006058843
>     > seconds Update interval : 59.4 seconds Leap status : Normal
>     > 
>     > [ewillian@server1 ~]$ chronyc tracking Reference ID : 7F7F0101 ()
>     > Stratum : 7 Ref time (UTC) : Mon Mar 04 16:16:35 2019 System time :
>     > 132.757308960 seconds slow of NTP time Last offset : -0.006639207 seconds RMS offset : 37.056121826 seconds Frequency : 2.662 ppm slow
>     > Residual freq : -45.436 ppm Skew : 72.985 ppm Root delay : 0.059753317
>     > seconds Root dispersion : 0.012772200 seconds Update interval : 59.5
>     > seconds Leap status : Normal

 - ##  Web server

![installation du nginx](https://github.com/Ewillian/CCNA2/blob/master/screens/nginx.png)



## BILAN

-     
    on a des  **clients**
    -   dans un réseau dédié
    -   qui récupèrent tout leur réseau automatiquement
        -   une IP
        -   une passerelle par défaut
        -   un serveur DNS
    -   qui peuvent aller sur internet normalement (avec firefox ou quoi) directement
-   on a des  **serveurs**
    -   dans un réseau dédié
    -   qui ont une IP fixe
    -   qui hébergent des services d'infra (un simple  `nginx`  chez nous)
-   on a des  **routeurs**
    -   qui peuvent faire passer le trafic d'un réseau à l'autre
    -   qui possèdent un lien dédié (le  `/30`)
    -   l'un d'eux fait du  **NAT**  pour permettre à tout le monde d'accéder à Internet
