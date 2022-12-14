# Wassim MEYYAH - 3ICS

# TP 6 - Services réseau

## Exercice 1. Adressage IP (rappels)

Vous administrez le réseau interne 172.16.0.0/23 d’une entreprise, et devez gérer un parc de 254 machines réparties en 7 sous-réseaux. La répartition des machines est la suivante :
- Sous-réseau 1 : 38 machines
- Sous-réseau 2 : 33 machines
- Sous-réseau 3 : 52 machines
- Sous-réseau 4 : 35 machines
- Sous-réseau 5 : 34 machines
- Sous-réseau 6 : 37 machines
- Sous-réseau 7 : 25 machines

Donnez, pour chaque sous-réseau, l’adresse de sous-réseau, l’adresse de broadcast (multidiffusion) ainsi que les adresses de la première et dernière machine configurées (précisez si vous utilisez du VLSM ou pas).
- Sous-réseau 1 :
    - Adresse de sous-réseau : 172.16.0.0/26
    - Adresse de broadcast :  172.16.0.63
    - Adresse de la première machine : 172.16.0.1
    - Adresse de la dernière machine : 172.16.0.62
    
- Sous-réseau 2 :
    - Adresse de sous-réseau : 172.16.0.64/26
    - Adresse de broadcast :  172.16.0.127
    - Adresse de la première machine : 172.16.0.65
    - Adresse de la dernière machine : 172.16.0.126
    
- Sous-réseau 3 :
    - Adresse de sous-réseau : 172.16.0.128/26
    - Adresse de broadcast :  172.16.0.191
    - Adresse de la première machine : 172.16.0.129
    - Adresse de la dernière machine : 172.16.0.190
    
- Sous-réseau 4 :
    - Adresse de sous-réseau : 172.16.0.192/26
    - Adresse de broadcast : 172.16.0.255
    - Adresse de la première machine : 172.16.0.193
    - Adresse de la dernière machine : 172.16.0.254
    
- Sous-réseau 5 :
    - Adresse de sous-réseau : 172.16.1.0/26
    - Adresse de broadcast :  172.16.1.63
    - Adresse de la première machine : 172.16.1.1
    - Adresse de la dernière machine : 172.16.1.62
    
- Sous-réseau 6 :
    - Adresse de sous-réseau : 172.16.1.64/26
    - Adresse de broadcast : 172.16.1.127
    - Adresse de la première machine : 172.16.1.65
    - Adresse de la dernière machine : 172.16.1.126
    
- Sous-réseau 7 :
    - Adresse de sous-réseau : 172.16.1.128/27
    - Adresse de broadcast : 172.16.1.159
    - Adresse de la première machine : 172.16.1.129
    - Adresse de la dernière machine : 172.16.1.158


## 2. Préparation de l'environnement

2 - 
Pour lister les interfaces réseau, on utilise ```ip l```. L'interface "lo" correspond à l'interface de loopback. Cette dernière permet de contacter la machine locale sans passer par une interface qui serait accessible de l'extérieur.

3 - 
Pour désinstaller ce paquet, on utilise ```sudo apt purge cloud-init```.

4 - 
On utilise la commande ```sudo nano /etc/hosts```. On modiife ensuite la ligne correspondant au serveur :
```127.0.0.1 serveur -> 127.0.0.1 serveur serveur.tpadmin.local```

## 3. Installation du serveur DHCP 

1 - 
On commence par mettre à jour les paquets installés avec ```sudo apt update ; sudo apt upgrade```. 
On installe ensuite le paquet à l'aide de la commande ```sudo apt install isc-dhcp-server```.

2 - 
On vérifie en premier lieu quelle carte réseau est concernée à l'aide de ```ip a```. Une fois identifiée, on modifie l IP avec ```sudo ip addr add 192.168.100.1```. On vérifie ensuite que le changement a bien été appliqué avec ```ip a```.

3 - 

```bash
default-lease-time 120;
max-lease-time 600;
authoritative; #DHCP officiel pour notre réseau
option broadcast-address 192.168.100.255; #informe les clients de l'adresse de broadcast
option domain-name "tpadmin.local"; #tous les hôtes qui se connectent au réseau auront ce nom de domaine

subnet 192.168.100.0 netmask 255.255.255.0 { #configuration du sous-réseau 192.168.100.0
    range 192.168.100.100 192.168.100.240; #pool d'adresses IP attribuables
    option routers 192.168.100.1; #le serveur sert de passerelle par défaut
    option domain-name-servers 192.168.100.1; #le serveur sert aussi de serveur DNS
}
``

Les deux premières lignes correspondent à la durée d'affectation d'une adresse IP

4. 

```bash
INTERFACESv4="eth0"
```

5.

```bash
$ sudo dhcpd -t
$ sudo systemctl restart isc-dhcp-server
$ sudo systemctl status isc-dhcp-server
```

6.

```bash
$ sudo apt-get purge cloud-init
$ sudo hostnamectl set-hostname client.tpadmin.local
```

7. 

```bash
$ sudo tail -f /var/log/syslog
```

Le client se voit bien affecter une adresse IP de la plage spécifiée précédemment.

8. 

```bash
$ sudo cat /var/lib/dhcp/dhcpd.leases
$ sudo dhcp-lease-list
```

Le fichier /var/lib/dhcp/dhcpd.leases contient les informations sur les adresses IP attribuées. 
La commande dhcp-lease-list affiche les informations sur les adresses IP attribuées.

9. 

```bash
$ ping 192.168.100.1
```

Les deux machines peuvent communiquer.

10. 

```bash
deny unknown-clients; #empêche l'attribution d'une adresse IP à une
#station dont l'adresse MAC est inconnue du serveur

host client {
    hardware ethernet a2:83:f7:50:54:1d; #remplacer par l'adresse MAC
    fixed-address 192.168.100.20;
}
```

```bash
$ sudo dhclient -v
```

La nouvelle configuration a bien été appliquée sur le client.


## Exercice 4. Donner un accès à Internet au client


1. 

```bash
$ sudo nano /etc/sysctl.conf
$ sudo sysctl -p /etc/sysctl.conf
$ sudo sysctl net.ipv4.ip_forward
```

2.

```bash
sudo iptables --table nat --append POSTROUTING --out-interface enp0s3 -j MASQUERADE
```


```bash
$ ping 1.1.1.1
```

On arrive à émettre un ping depuis le client.

## Exercice 5. Installation du serveur DNS

1. 

```bash
$ sudo apt-get install bind9
$ sudo systemctl status bind9
```

2. 

```bash
$ sudo nano /etc/bind/named.conf.options
$ sudo systemctl restart bind9
```

3. 

```bash
$ ping www.google.fr
```

On arrive à émettre un ping vers www.google.fr depuis le client.

4. 

```bash
$ sudo apt-get install lynx
$ lynx fr.wikipedia.org
```

On arrive à surfer sur fr.wikipedia.org depuis le client.

## Exercice 6. Configuration du serveur DNS pour la zone tpadmin.local

1. 

```
zone "tpadmin.local" IN {
    type master; // c'est un serveur maître
    file "/etc/bind/db.tpadmin.local"; // lien vers le fichier de définition de zone
};
```

2.

```bash
$ sudo cp /etc/bind/db.local /etc/bind/db.tpadmin.local
$ sudo nano /etc/bind/db.tpadmin.local
```

3. 

```
zone "100.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/db.192.168.100";
};
```

```bash
$ sudo cp /etc/bind/db.127 /etc/bind/db.192.168.100
$ sudo nano /etc/bind/db.192.168.100
```

4.

```bash
$ sudo named-checkconf named.conf.local
$ sudo named-checkzone tpadmin.local /etc/bind/db.tpadmin.local
$ sudo named-checkzone 100.168.192.in-addr.arpa /etc/bind/db.192.168.100
```

```bash
$ sudo nano /etc/systemd/resolved.conf
```

5. 
```bash
$ sudo systemctl restart bind9
```
Toutes les machines sont "pingables"
