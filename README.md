# tp2.fils

## Routage
### ☀️ Configuration du routeur
```
$ sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s8
NOM=enp0s8
APPAREIL=enp0s8

BOOTPROTO=statique
ONBOOT=oui

IPADDR=10.2.1.254
MASQUE RÉSEAU=255.255.255.0

DNS1=1.1.1.1
```
**Résultat :**
```
$ sudo nmcli avec rechargement
$ sudo nmcli avec enp0s8
$ip un
[...]
3 : enp0s8 : <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP groupe par défaut qlen 1000
    lien/éther 08:00:27:22:de:5b brd ff:ff:ff:ff:ff:ff
    inet 10.2.1.254/24 brd 10.2.1.255 portée globale noprefixroute enp0s8
       valid_lft pour toujours préféré_lft pour toujours
    inet6 fe80 :: a00: 27ff: fe22: de5b/64 lien de portée
       valid_lft pour toujours préféré_lft pour toujours
```
**Sur le fichier actif Forwarding IPv4**
```
$ sudo sysctl -w net.ipv4.ip_forward=1
net.ipv4.ip_forward = 1

$ sudo pare-feu-cmd --add-masquerade
succès

$ sudo pare-feu-cmd --add-masquerade --permanent
succès
```
### ️☀️ Configuration de node1.tp2.efrei
```
$ sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s3
NOM=enp0s3
APPAREIL=enp0s3

BOOTPROTO=statique
ONBOOT=oui

IPADDR=10.2.1.1
MASQUE RÉSEAU=255.255.255.0

DNS=1.1.1.1
```
**Test de Ping**
```
$ping 10.2.1.254
PING 10.2.1.254 (10.2.1.254) 56(84) octets de données.
64 octets de 10.2.1.254 : icmp_seq=1 ttl=64 time=2,21 ms
```
**Configuration de la passerelle par défaut**
```
$ sudo nano /etc/sysconfig/network
PASSERELLE=10.2.1.254
```
**Test de Ping sur le DNS Google**
```
$ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) octets de données.
64 octets de 8.8.8.8 : icmp_seq=1 ttl=113 time=31,3 ms
```
**Les paquets passent bien par le Router :**
```
$ traceroute 8.8.8.8
traceroute vers 8.8.8.8 (8.8.8.8), 30 sauts maximum, paquets de 60 octets
 1 _passerelle (10.2.1.254) 1,537 ms 1,597 ms 1,508 ms
 2 192.168.122.1 (192.168.122.1) 1,372 ms 1,554 ms 1,515 ms
 3 10.100.0.1 (10.100.0.1) 7,081 ms 7,032 ms 6,904 ms
 4 10.100.255.11 (10.100.255.11) 5,668 ms 5,619 ms 5,120 ms
 5 185.176.176.10 (185.176.176.10) 32,149 ms 32,030 ms 31,977 ms
 6 100.126.127.254 (100.126.127.254) 30,292 ms 27,643 ms 27,584 ms
 7 100.126.127.253 (100.126.127.253) 27,547 ms 27,237 ms 28,771 ms
 8 185.181.155.200 (185.181.155.200) 29,922 ms 28,660 ms 29,856 ms
 9 lient-2.par.franceix.net (37.49.238.52) 29,819 ms 29,691 ms 29,702 ms
10 google2.par.franceix.net (37.49.236.2) 29,498 ms 29,453 ms 29,225 ms
11 108.170.244.225 (108.170.244.225) 28,937 ms 108.170.245.1 (108.170.245.1) 28,833 ms 108.170.244.193 (108.170.244.193) 29,58 3 ms
12 142.250.234.41 (142.250.234.41) 29,348 ms 216.239.48.139 (216.239.48.139) 32,816 ms 142.251.253.35 (142.251.253.35) 29,119 m s
13 dns.google (8.8.8.8) 28,927 ms 28,868 ms 30,861 ms
```
## Serveur DHCP
### ☀️ Installation et configuration du serveur DHCP sur dhcp.tp2.efrei
```
$ sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s3
NOM=enp0s3
APPAREIL=enp0s3

BOOTPROTO=statique
ONBOOT=oui

IPADDR=10.2.1.253
MASQUE RÉSEAU=255.255.255.0

DNS1=1.1.1.1
```
**Configuration de la passerelle par défaut**
```
$ sudo nano /etc/sysconfig/network
PASSERELLE=10.2.1.254
```
**Redémarrage du système**
```
$ sudo nmcli avec rechargement
$ sudo nmcli avec enp0s3
```
**Installation DHCP**
```
sudo dnf -y installer le serveur DHCP
```
**Configuration DHCP**
```
sudo nano /etc/dhcp/dhcpd.conf
durée de location par défaut 3 600 ; //durée par défaut du bail DHCP
durée de location maximale 86 400 ; //durée maximale du bail DHCP
faisant autorité;

sous-réseau 10.2.1.0 masque de réseau 255.255.255.0 {
plage 10.2.1.100 10.2.1.200 ;
routeurs d'options 10.2.1.254 ; //adresse IP du routeur par défaut
option masque de sous-réseau 255.255.255.0 ;
}
```
### ☀️ Test du DHCP sur node1.tp2.efrei
```
$ sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s3
NOM=enp0s3
APPAREIL=enp0s3

BOOTPROTO=DHCP
ONBOOT=oui
```
**On libère l'adresse IP**
```
$ sudo dhclient -r
```
**Résulat**
```
$ip un
2 : enp0s3 : <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel état UP groupe par défaut qlen 1000
    lien/éther 08:00:27:35:b3:81 brd ff:ff:ff:ff:ff:ff
    inet6 fe80 :: a00: 27ff: fe35: b381/64 lien de portée
       valid_lft pour toujours préféré_lft pour toujours
```
**Renouvelement de l'adresse IP**
```
$ sudo client client
```
**Résulat**
```
$ip un
2 : enp0s3 : <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel état UP groupe par défaut qlen 1000
    lien/éther 08:00:27:35:b3:81 brd ff:ff:ff:ff:ff:ff
    inet 10.2.1.100/24 ​​brd 10.2.1.255 portée globale dynamique enp0s3
       valid_lft 3493sec préféré_lft 3493sec
    inet6 fe80 :: a00: 27ff: fe35: b381/64 lien de portée
       valid_lft pour toujours préféré_lft pour toujours
```
**Affichage de la table de routage**
```
$iprs
par défaut via 10.2.1.254 dev enp0s3
10.2.1.0/24 dev enp0s3 proto noyau portée lien src 10.2.1.100
192.168.56.0/24 dev enp0s8 proto noyau portée lien src 192.168.56.107 métrique 101
```
**Ping du DNS de Google**
```
$ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) octets de données.
64 octets de 8.8.8.8 : icmp_seq=1 ttl=113 time=22,0 ms
```
**Ping du site web Google.fr**
```
$ ping google.fr
PING google.fr (142.250.75.227) 56(84) octets de données.
64 octets de par10s41-in-f3.1e100.net (142.250.75.227) : icmp_seq=1 ttl=113 time=20,2 ms
```
### ☀️BONUS
```
durée de location par défaut 3 600 ;
durée de location maximale 86 400 ;
faisant autorité;

sous-réseau 10.2.1.0 masque de réseau 255.255.255.0 {
plage 10.2.1.100 10.2.1.200 ;
routeurs d'options 10.2.1.254 ;
option masque de sous-réseau 255.255.255.0 ;
option serveurs de noms de domaine 1.1.1.1 ;
}
```
**Ping du site web Google.fr**
```
$ ping google.fr
PING google.fr (142.250.75.227) 56(84) octets de données.
64 octets de par10s41-in-f3.1e100.net (142.250.75.227) : icmp_seq=1 ttl=113 time=20,2 ms
```
### ☀️ Wireshark
![Échange DORA](echangeDORA.pcapng)

#ARP
## Tableaux ARP
### ☀️ Table ARP du routeur
```
ip neigh show
10.2.1.254 dev enp0s3 lladdr 08:00:27:9a:50:ba ACCESSIBLE
192.168.56.100 dev enp0s8 lladdr 08:00:27:52:c8:4d périmé
10.2.1.253 dev enp0s3 lladdr 08:00:27:a9:4f:18 périmé
192.168.56.1 dev enp0s8 lladdr 0a:00:27:00:00:00 joignable
```
### ☀️ Échanger Wireshark
![Requete ARP](requeteARP.pcapng)

## Empoisonnement ARP
### ☀️ Empoisonnement simple à l'ARP
```
$ sudo ip neigh change 10.2.1.254 lladdr 08:00:27:a9:4f:18 dev enp0s3
```
```
$ ip neigh show
10.2.1.254 dev enp0s3 lladdr 08:00:27:a9:4f:18 PERMANENT
10.2.1.253 dev enp0s3 lladdr 08:00:27:a9:4f:18 périmé
192.168.56.1 dev enp0s8 lladdr 0a:00:27:00:00:00 joignable
```
