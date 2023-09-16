# Etherchannel



**Définition:**

Etherchannel est un protocole qui agit au niveau 2 du modèle OSI. Il est applicable principalement sur les équipements de niveau 2 (Switchs,Backbones).

Il permet de regrouper plusieurs liens physiques en un seul lien logique. Cela augmente la bande passante ainsi que la redondance, lorsqu'un lien tombe, les autres prennent le relai. Il possède 2 modes de configuration:

- LACP (LInk Aggregation Control Protocol)
  - Standardisé par l'IEEE 802.3.ad
  - Permet à des équipements de communiquer et négocier la création d'un agrégat de liens.
  - L'agrégat est créé si les paramètres LACP son t compatibles des deux côtés.
  - Le mode LACP à 2 sous-modes possibles:
    - Active : L'appareil envoie des paquets LACP pour initier la négociation
    - Passive : L'appareil n'initie pas la négociation, mais il répond s'il reçoit des paquets LACP d'un autre appareil en mode actif.
- PAgP (Port Aggregation Protocol)
  - PAgP est spécifique à Cisco est n'est pas normalisé par l'IEEE.
  - Il fonction de manière similaire à LACP, mais propre aux équipements cisco.
  - Il a 2 sous modes possibles:
    - Auto : L'appareil attend que l'autres extrémité initie la négociation. S'il ne reçoit pas de requête PAgP, il devient un membre individuel.
    - Desirable : l'appareil envoie des requêtes PAgP pour initier la négociation. S'il ne reçoit pas de réponse, il devient un membre individuel

### Configuration

La syntaxe de la commande est simple:

```
channel-group <group number> mode <mode>
```

Configuration LACP sur 2 liens en GigabitEthernet 3/0 et GigabitEthernet 3/1

```
(config)# int range g3/0-1
(config-if-range)#channel-group 1 mode ?
  active     Enable LACP unconditionally
  auto       Enable PAgP only if a PAgP device is detected
  desirable  Enable PAgP unconditionally
  on         Enable Etherchannel only
  passive    Enable LACP only if a LACP device is detected
(config-if-range)#channel-group 1 mode active
```

L'interface "Po1" a était créé. Pour regarder le status du lien:

```
SW# show etherchannel summary
Flags:  D - down        P - bundled in port-channel
        I - stand-alone s - suspended
        H - Hot-standby (LACP only)
        R - Layer3      S - Layer2
        U - in use      N - not in use, no aggregation
        f - failed to allocate aggregator

        M - not in use, minimum links not met
        m - not in use, port not aggregated due to minimum links not met
        u - unsuitable for bundling
        w - waiting to be aggregated
        d - default port

        A - formed by Auto LAG


Number of channel-groups in use: 1
Number of aggregators:           1

Group  Port-channel  Protocol    Ports
------+-------------+-----------+-----------------------------------------------
1      Po1(SU)         LACP      Gi3/0(P)    Gi3/1(P)   
```

**Attention de bien configurer à l'identique sur chaque liens sinon ça ne fonctionnera pas!**
Il faut bien vérifier Po1(SU), c'est cet indicateur qui permettra de s'assurer que le lien est bien monté et actif.

Pour voir le status des interfaces actives :

```
sh int status

Port      Name               Status       Vlan       Duplex  Speed Type 
Gi0/0                        disabled     1            auto   auto RJ45
Gi0/1                        disabled     1            auto   auto RJ45
Gi0/2                        disabled     1            auto   auto RJ45
Gi0/3                        disabled     1            auto   auto RJ45
Gi1/0                        disabled     1            auto   auto RJ45
Gi1/1                        disabled     1            auto   auto RJ45
Gi1/2                        disabled     1            auto   auto RJ45
Gi1/3                        disabled     1            auto   auto RJ45
Gi2/0                        disabled     1            auto   auto RJ45
Gi2/1                        disabled     1            auto   auto RJ45
Gi2/2                        disabled     1            auto   auto RJ45
Gi2/3                        disabled     1            auto   auto RJ45
Gi3/0                        connected    trunk      a-full   auto RJ45
Gi3/1                        connected    trunk      a-full   auto RJ45
Gi3/2                        disabled     1            auto   auto RJ45
Gi3/3                        disabled     1            auto   auto RJ45
Po1                          connected    trunk      a-full   auto 
```

On peut voir ici l'interface g3/0, g3/1 et Po1 en status "Connected". Si le status est en "suspended", il faut voir les points suivants:

- Config des liens (config channel-group, trunk, speed/duplex, vlan)
- Status up/down des liens (Gigabit/Port-channel)

Si ces points sont déjà vérifiés, il faudra shut/no shut les interfaces et revérifier ensuite l'état du lien.
