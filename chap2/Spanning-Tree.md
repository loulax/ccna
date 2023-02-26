# Spanning-Tree



## Introduction



Le spanning-tree est un protocole réseau qui agit au niveau 2 du modèle OSI. Il permet dans une topologie entre plusieurs switchs de gérer la redondance et empêcher les boucles. Cela évite la saturation d'un réseau local suite aux tempêtes de broadcast.



## Fonctionnement



Voici un exemple:

La topologie contient 3 switchs chacun reliés à son voisin sur 2 interfaces. Une technique pour une tolérance de panne au plus proche de 0. De ce fait, lorsqu'une interface tombe, l'autre prendra le relai ou si carrément un switch tombe, c'est l'autre qui fera la commutation.

Mais pour ça, il va d'abord élire le switch principale (root) grâce au numéro appelé Bridge ID. Plus celui-ci sera faible, plus il aura de chance d'être élu.
