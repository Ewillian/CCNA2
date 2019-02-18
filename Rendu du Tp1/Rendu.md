# RENDU.md



# RENDU - TP1

 I.Mise en place de la VM
 - État du client1.tp1.b2


       1. Connexion ssh :
       PS C:\Users\guigu> ssh ewillian@10.1.1.2 The authenticity of host '10.1.1.2 (10.1.1.2)' can't be established. 
       ECDSA key fingerprint is SHA256:cUaz958UREpPMDrymVKGMMPJ4OXlA+tc9VDjzrrqjzA. 
       Are you sure you want to continue connecting (yes/no)? yes 
       Warning: 
       Permanently added '10.1.1.2' (ECDSA) to the list of known hosts. ewillian@10.1.1.2's password: 
       Last login: Thu Feb 14 15:45:34 2019 from 10.1.1.1
       [ewillian@client1 ~]$
       
       2.Nom de domaine:
       [ewillian@client1 ~]$ cat /etc/hostname client1.tp1.b2
       
       3.Fichier hosts de la Vm:
       [ewillian@client1 ~]$ cat /etc/hosts 
       127.0.0.1 localhost localhost.localdomain localhost4 localhost4.localdomain4
        192.168.33.14 mail.lab.local
         ::1 localhost localhost.localdomain localhost6 localhost6.localdomain6 
         10.1.1.1 host 
         10.1.1.2 client 
         10.1.2.1 host 
         10.1.2.2 client
         
         4.Vérification du fonctionnement des 3 cartes réseaux :
         PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data. 
         64 bytes from 8.8.8.8: icmp_seq=1 ttl=120 time=32.4 ms
         64 bytes from 8.8.8.8: icmp_seq=2 ttl=120 time=33.6 ms
         
         PING 10.1.1.1 (10.1.1.1) 56(84) bytes of data. 
         64 bytes from 10.1.1.1: icmp_seq=1 ttl=128 time=0.170 ms 
         64 bytes from 10.1.1.1: icmp_seq=2 ttl=128 time=0.204 ms
         
         PING 10.1.2.1 (10.1.2.1) 56(84) bytes of data. 
         64 bytes from 10.1.2.1: icmp_seq=1 ttl=128 time=0.390 ms 
         64 bytes from 10.1.2.1: icmp_seq=2 ttl=128 time=0.803 ms


       

 - Basics
    
   - Routes 
   
         1.On supprime la route de la cart NAT par default:
         [ewillian@client1 ~]$ sudo ip route del default
         [ewillian@client1 ~]$ ping 8.8.8.8 connect: Le réseau n'est pas accessible
        
         2.Affichage des routes : 
         [ewillian@client1 ~]$ ip neigh show 
         10.0.2.2 	dev enp0s3 lladdr 	52:54:00:12:35:02        	STALE 
         10.1.1.3 	dev enp0s8 lladdr 	08:00:27:fa:54:bb        	STALE 
         10.1.1.1 	dev enp0s8 lladdr 	0a:00:27:00:00:36        	DELAY
         ip voisin +  carte réseau  +   		adresse mac  		+  statut de l'ip voisin
         
         3.Ajout d'une route : 
         [ewillian@client1 ~]$ sudo ip route add default dev enp0s3 via 10.0.2.2 
         [ewillian@client1 ~]$ ping 8.8.8.8 PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data. 
         64 bytes from 8.8.8.8: icmp_seq=1 ttl=120 time=32.7 ms 
        
         

         
- Table ARP 
   
         1.On affiche les voisins : 
         [ewillian@client1 ~]$ ip neigh show 
         10.0.2.2 dev enp0s3 lladdr 52:54:00:12:35:02 STALE 
         10.1.1.3 dev enp0s8 lladdr 08:00:27:fa:54:bb STALE 
         10.1.1.1 dev enp0s8 lladdr 0a:00:27:00:00:36 DELAY
         
         2. On vide la table ARP :
         [ewillian@client2 ~]$ ip neigh show 
         10.1.1.2 dev enp0s8 lladdr 08:00:27:00:25:39 REACHABLE 
         10.1.1.1 dev enp0s8 lladdr 0a:00:27:00:00:36 REACHABLE
        

     
II. Communication simple entre deux machines 

   

 A. Ping et ARP

 
         1.Depuis le client 1 : 
         [ewillian@client1 ~]$ ping -c 4 client2 
         PING client2 (10.1.1.3) 56(84) bytes of data. 
         64 bytes from client2 (10.1.1.3): icmp_seq=1 ttl=64 time=0.498 ms 
         64 bytes from client2 (10.1.1.3): icmp_seq=2 ttl=64 time=0.324 ms 
         64 bytes from client2 (10.1.1.3): icmp_seq=3 ttl=64 time=0.243 ms 
         64 bytes from client2 (10.1.1.3): icmp_seq=4 ttl=64 time=0.717 ms
         

       
   - Explication du ping avec Wireshark




  
 B. Netcat

 

 - UPD

         1.Sur le client1:
         [ewillian@client1 ~]$ nc -u -l 8888 ssss
         
         2.Sur le client2:
         [ewillian@client2 ~]$ nc -u 10.1.1.2 8888 ssss
         

   - Analyse  Wireshark



 - TCP

         1.Sur le client1:
         [ewillian@client1 ~]$ ss -tnp 
         State     Recv-Q Send-Q Local Address:Port 
         Peer Address:Port 
         ESTAB 	0 		0 		10.1.1.2:22 
         10.1.1.1:65087 
         ESTAB 	0 		0 		10.1.1.2:8888 
         10.1.1.3:60932  	users:(("nc",pid=4137,fd=5)) 
         ESTAB 	0 		0 		10.1.1.2:22 
         10.1.1.1:53963
         
         2.Sur le client2:
         [ewillian@client2 ~]$ ss -tnp 
         State 		Recv-Q Send-Q Local Address:Port 
         Peer Address:Port 
         ESTAB 	0 		0 		10.1.1.3:22 
         10.1.1.1:54622 
         ESTAB 	0 		0 		10.1.1.3:22 
         10.1.1.1:65089 
         ESTAB 	0 		0 		10.1.1.3:60932 
         10.1.1.2:8888 		users:(("nc",pid=4263,fd=3))
         
    - Analyse  Wireshark



 - FIREWALL

      
       [ewillian@client1 ~]$ sudo firewall-cmd --remove-port=8888/tcp --permanent 
        [sudo] Mot de passe de ewillian : 
        Warning: NOT_ENABLED: 8888:tcp 
        success
         
    - Analyse  Wireshark




