# Packet Tracer

## Console

Accéder à la liste d'aide, saisir la commande **help** . Celle-ci est accessible dans tous les modes d'exécution.

Elle permet d'affiche la liste des commandes accessibles dans le mode actuel avec une petite description de ce qu'elle fait.

### Les modes d'exécution

- **Exécution utilisateur** : Ce mode offre des fonctionnalités limitées mais utile pour les opérations de base. Il autorise seulement un nombre de commandes limitées pour la surveillance de base mais aucune susceptible de modifier la configuration du périphérique. Ce mode est reconnu par le symbole **>** qui termine le prompt.
- **Exécution privilégié / actif** : Ce mode est utile pour les administrateurs réseau. Il permet d'accéder à des configurations avancées pour modifier certains paramètres. Il est reconnu par le symbole **#** qui termine le prompt. Pour accéder à ce mode, saisir la commande suivante.

```php
switch> enable
switch#
```

- **Configuration de ligne** : Utilisé pour l'accès par la console, ssh, telnet ou AUX. L'invite par défaut pour le mode de configuration de ligne est **Switch(config-line)#**.

```php
switch(config)# line console <numero>
```

- **Configuration d'interface** : Utilisé pour configurer l'interface réseau d'un port (ou plusieurs ports) d'un switch ou d'un routeur. Le prompt par défaut est **Switch(config-if)#**. 

```php
switch(config)# interface <type><number>
- Ex : int Fa0/1
- int g0/1
- int range fa0/1 - 5
switch(config-if)#
switch(config-if-range)#
```

### Accès mode privilégié

Afin de pouvoir accéder au mode privilégié de la console, il faut saisir la commande suivante. Dans certains cas, un mot de passe est nécessaire.

```ABAP
enable / en
```

Pour repasser au mode utilisateur

```ABAP
disable
```

Pour passer en mode configuration globale .

```ABAP
configure terminal / conf t
```

### Résolution DNS

Parfois lorsqu'une commande est mal saisie, le switch ou le routeur va faire de la recherche dns pour vérifier si la commande existe, mais la procédure peut mettre du temps ce qui est pénible puisque le terminal durant l'opération est figé.... Pour éviter ça, il est possible de désactiver la recherche dns ainsi :

```
Routeur/Switch> en
Routeur/Switch#> conf t
Routeur/Switch(config)#>no ip domain-lookup
```

On peut aussi le faire pour les lignes distantes

```php
Routeur(config)# line vty 0 15
Routeur(config-line)# no ip domain-lookup
```

## Sauvegarde

Lorsque le switch est configuré correctement, afin de ne pas perdre ses paramètres au prochain démarrage, il est possible de sauvegarder la config pour qu'elle soit prise en compte à chaque démarrage.

Avant de faire la sauvegarde, voici les commandes qui permettent d'afficher quelques informations sur la configuration de l'appareil.

```php
Switch# show ip interface brief
Switch# show ipv6 interface brief
```

```
Switch# show vlan brief
```

Il est possible de filtrer les résultats via les mots clé **include**, **exclude**, **begin**, **section**. Comme un **grep** sous linux.

Ex :

```php
show  ip interface brief | include up
routerCG#show ip int brief | include up
GigabitEthernet0/0     192.168.10.1    YES manual up                    up 
Serial0/1/0            10.0.0.1        YES manual up                    up 
```



```php
Switch# copy running-config startup-config
```

## Accès sécurisé

Afin de limiter de limiter les accès aux différents modes et à distance, il est fortement recommandé de configurer un mot de passe.

### Mode privilégié

```php
Switch(Config)# enable secret <password>
```

### SSH

Dans la plupart des cas, il sera constaté que pour l'accès ssh toutes les lignes virtuelles de 0 à 15 seront dédiées au protocole ssh pour des mesures de sécurité afin d'empêcher l'accès en telnet.

```bash
Switch# show ip ssh
Switch(config)# ip domain-name <domain-name>
Switch(config)# ip ssh version 2
Switch(config)# crypto key generate rsa
How many bits in the modulus [512]: <key bit size>
Switch(config)# username admin secret <password>
Switch(config)# line vty 0 15
Switch(config-line)# transport input ssh
Switch(config-line)# login local
Switch(config-line)# exit
```

## Vlan

Par défaut les switchs cisco ont un vlan 1 déjà configuré sur lequel tous les appareils connecté au switch est relié. Celui-ci n'est ni modifiable, ni supprimable.

Pour créer un vlan et le configurer voici les commandes:

### Création

```php
Switch(config)# vlan <number>
Switch(config-vlan)# name <name>
Switch(config-vlan)# exit
```

### Mode access

```bash
Switch(config)# int <int number>
Switch(config)# switchport mode access
Switch(config)# switchport access vlan <number> (plusieurs sont possible)
```

### Mode trunk

```bash
Switch(config)# int <number>
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan <number> (plusieurs sont possible)
```

## Routeur

### Config interface

Pour configurer une interface réseau

```bash
Router(config)# int <number>
Router(config-if)# ip address <ip> <subnet mask>
router(config-if)# no shutdown
```

### Route

Pour configurer des routes voici la procédure

```bash
Router(config)# ip route <network> <mask> <remote link>
```

## OSPF

Le protocole OSPF (Open Shortest Path Fist) est un protocole de routage dynamique, qui fait partie de la famille des protocoles de routage à état de lien.

Il existe 3 familles:

|           | Vecteur de distance | Etat de lien            | Vecteur de chemin |
| --------- | ------------------- | ----------------------- | ----------------- |
| Classfull | IGRP                |                         | EGP               |
| Classless | EIGRP               | OSPFv2   IS-IS          | BGPv4             |
| IPv6      | EIGRP for IPv6      | OSPFv3   IS-IS for IPv6 | BGPv4 for IPv6    |

### Routage OSPF

```php
# La commande suivante permet d'activer OSPF
Router(config) # router ospf 1
# Il faut maintenant renseigner les adresses réseau de chaque IF du routeur 
Routeur(config-router)# network <network id> <subnet mask> area <number>
```

Pour afficher la configuration :

```
Routeur# show ip protocols
.........
Routeur# show ip ospf neighbor
```

| type         | commande              | Résumé                                                 |
| ------------ | --------------------- | ------------------------------------------------------ |
| routage ospf | show ip protocols     | affiche des infos sur les protocoles actifs du routeur |
| routage ospf | show ip ospf neighbor | affiche les routeurs ospf voisin                       |

