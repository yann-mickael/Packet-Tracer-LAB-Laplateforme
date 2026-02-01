PROJET LAPLATEFORME - 13002 - MARSEILLE - TEST DE PRÉ-SELECTION MASTER CYBERSÉCURITÉ
# Configuration Réseau Cisco – SWITCHS, ROUTEUR, VLANs, DHCP, NTP

## Objectif
Configurer un réseau complet incluant :
- Un switch 2960
- Un routeur 1941
- VLANs pour VoIP, WiFi, PC Fixes, Admin
- Téléphones VoIP 7960
- DHCP par VLAN
- Points d'accès PT- Laptop

---

## SWITCH1 – Configuration Initiale

### 1. Renommer le switch
```
conf t
hostname SWITCH1
```

### 2. Désactiver les ports inutilisés
```
conf t
interface range fastEthernet 0/10 -24
shutdown
exit
end
wr
```

### 3. Sécuriser l'accès console
```
enable
conf t
line console 0
password Laplateforme,1,2
login
exit
```

---

## ROUTEUR – Sécurisation
```
enable
conf t
enable secret Laplateforme
exit
wr
```

---

## Configuration des téléphones VoIP
```
interface fa0/2
switchport mode access
switchport access vlan 1
spanning-tree portfast
description VoIP_Telephone_1
exit

interface fa0/3
switchport mode access
switchport access vlan 1
spanning-tree portfast
description VoIP_Telephone_2
exit
```

---

## SWITCH1 – VLANs et Ports

### Création des VLANs
```
vlan 1
name VoIP
exit
vlan 10
name PT_WIFI
exit
vlan 20
name PC_FIXES
exit
vlan 30
name ADMIN
exit
```

### Attribution des ports aux VLANs
```
interface range fa0/2 -3
switchport mode access
switchport access vlan 1
exit

interface range fa0/4 -5
switchport mode access
switchport access vlan 10
exit

interface range fa0/6 -7
switchport mode access
switchport access vlan 20
exit

interface fa0/8
switchport mode access
switchport access vlan 30
exit
```

### Ports Trunk
```
interface range fa0/1, fa0/9
switchport mode trunk
exit
end
wr
```

---

## ROUTEUR – Routage Inter-VLAN

### Activation du routage
```
enable
configure terminal
ip routing
interface gig0/0
no shutdown
exit
```

### Configuration des sous-interfaces pour VLANs
```
interface g0/0.1
encapsulation dot1Q 1 native
ip address 192.168.0.1 255.255.255.0
exit

interface g0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
exit

interface g0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
exit

interface g0/0.30
encapsulation dot1Q 30
ip address 192.168.30.1 255.255.255.0
exit
```

---

## ROUTEUR – DHCP par VLAN
```
enable
conf t

interface gig0/0
no shut
exit

ip dhcp excluded-address 192.168.0.1 192.168.0.9
ip dhcp pool vlan1
network 192.168.0.0 255.255.255.0
default-router 192.168.0.1
dns-server 8.8.8.8

ip dhcp excluded-address 192.168.10.1 192.168.10.9
ip dhcp pool vlan10
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 8.8.8.8

ip dhcp excluded-address 192.168.20.1 192.168.20.9
ip dhcp pool vlan20
network 192.168.20.0 255.255.255.0
default-router 192.168.20.1
dns-server 8.8.8.8

ip dhcp excluded-address 192.168.30.1 192.168.30.9
ip dhcp pool vlan30
network 192.168.30.0 255.255.255.0
default-router 192.168.30.1
dns-server 8.8.8.8
exit
end
wr
```

---

## ROUTEUR – Configuration de l’heure
```
enable
configure terminal
clock set 00:03:00 4 May 2025
exit
show clock
```

---

## Sauvegarde
Ne pas oublier de sauvegarder à chaque étape :
```
wr
```

---

## Remarques
- Chaque VLAN est isolé avec sa propre passerelle.
- Le DHCP est configuré pour éviter les adresses statiques.
- Le port trunk transporte les VLANs jusqu’au routeur.
  

---

## Auteur
Configuration réalisée dans le cadre d’un projet pédagogique chez Laplateforme.
