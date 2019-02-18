# RENDU.md



# RENDU - TP1

 - État du client1.tp1.b2
 
    1. Connexion ssh :
 
PS C:\Users\guigu> ssh ewillian@10.1.1.2 The authenticity of host '10.1.1.2 (10.1.1.2)' can't be established. ECDSA key fingerprint is SHA256:cUaz958UREpPMDrymVKGMMPJ4OXlA+tc9VDjzrrqjzA. Are you sure you want to continue connecting (yes/no)? yes Warning: Permanently added '10.1.1.2' (ECDSA) to the list of known hosts. ewillian@10.1.1.2's password: Last login: Thu Feb 14 15:45:34 2019 from 10.1.1.1
[ewillian@client1 ~]$

   2. Nom de domaine : 
   
   [ewillian@client1 ~]$ cat /etc/hostname client1.tp1.b2

   3.  Fichier hosts de la VM :

[ewillian@client1 ~]$ cat /etc/hosts 127.0.0.1 localhost localhost.localdomain localhost4 localhost4.localdomain4 192.168.33.14 mail.lab.local ::1 localhost localhost.localdomain localhost6 localhost6.localdomain6 10.1.1.1 host 10.1.1.2 client 10.1.2.1 host 10.1.2.2 client

  4. Vérification du fonctionnement des 3 cartes réseaux :

ping 8.8.8.8 PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data. 64 bytes from 8.8.8.8: icmp_seq=1 ttl=120 time=32.4 ms 64 bytes from 8.8.8.8: icmp_seq=2 ttl=120 time=33.6 ms

PING 10.1.1.1 (10.1.1.1) 56(84) bytes of data. 64 bytes from 10.1.1.1: icmp_seq=1 ttl=128 time=0.170 ms 64 bytes from 10.1.1.1: icmp_seq=2 ttl=128 time=0.204 ms

PING 10.1.2.1 (10.1.2.1) 56(84) bytes of data. 64 bytes from 10.1.2.1: icmp_seq=1 ttl=128 time=0.390 ms 64 bytes from 10.1.2.1: icmp_seq=2 ttl=128 time=0.803 ms

 - Routes du client1.tp1.b2

   1. on affiche les routes avec la commande " ip route show" : 

*10.0.2.0/24* dev enp0s3 proto kernel scope link sr **10.0.2.15** metric 100 
*10.1.1.0/24* dev enp0s8 proto kernel scope link src **10.1.1.2** metric 101 
*10.1.2.0/30* dev enp0s9 proto kernel scope link src **10.1.2.2** metric 102

À droite, nous avons des adressent ip en gras qui représente les adresses de nos machines.
À gauche, nous retrouvons les adresses ip par lesquelles nous accédons au réseau " switch".

2.  On supprime une route : 

[ewillian@client1 ~]$ sudo ip route del default [ewillian@client1 ~]$ ping 8.8.8.8 connect: Le réseau n'est pas accessible

3. On remet une route : 

[ewillian@client1 ~]$ sudo ip route add default dev enp0s3 via 10.0.2.2 
[ewillian@client1 ~]$ ping 8.8.8.8 PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data. 64 bytes from 8.8.8.8: icmp_seq=1 ttl=120 time=32.7 ms

 - Table ARP

1. On affiche les voisins : 

[ewillian@client1 ~]$ ip neigh show 
10.1.1.1 dev enp0s8 lladdr 0a:00:27:00:00:36 REACHABLE
 10.1.2.1 dev enp0s9 lladdr 0a:00:27:00:00:3a STALE 10.0.2.2 dev enp0s3 lladdr 52:54:00:12:35:02 STALE

Pour chaque ligne nous avons dans l'ordre :  

10.0.2.2 dev enp0s3 lladdr 52:54:00:12:35:02 STALE
Ip voisin +   carte réseau + adresse mac + statut de l'ip

