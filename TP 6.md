# Wassim MEYYAH - 3ICS

## 2. Préparation de l'environnement

### 2 - 
Pour lister les interfaces réseau, on utilise ```ip l```. L'interface "lo" correspond à l'interface de loopback. Cette dernière permet de contacter la machine locale sans passer par une interface qui serait accessible de l'extérieur.

### 3 - 
Pour désinstaller ce paquet, on utilise ```sudo apt purge cloud-init```.

### 4 - 
On utilise la commande ```sudo nano /etc/hosts```. On modiife ensuite la ligne correspondant au serveur :
```127.0.0.1 serveur -> 127.0.0.1 serveur serveur.tpadmin.local```

## 3. Installation du serveur DHCP 

### 1 - 
On commence par mettre à jour les paquets installés avec ```sudo apt update ; sudo apt upgrade```. 
On installe ensuite le paquet à l'aide de la commande ```sudo apt install isc-dhcp-server```.

### 2 - 
On vérifie en premier lieu quelle carte réseau est concernée à l'aide de ```ip a```. Une fois identifiée, on modifie l IP avec ```sudo ip addr add 192.168.100.1```. On vérifie ensuite que le changement a bien été appliqué avec ```ip a```.

## 3 - 
