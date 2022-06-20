Installation
=====

Cette procédure décrit les différents tâches à réaliser afin de mettre en production le portail captif. Le script d'installation automatique a été conçu pour fonctionner sur un système **Debian 11**, les autres systèmes d'exploitation ne sont pas pris en charge actuellement.

Pré-requis
------------

.. image:: images/config.example.png

Le portail captif se positionnant en coupure de flux, la machine doit posséder deux interfaces réseaux. Dans l'exemple d'installation détaillé ici, les interfaces suivantes sont disponibles :

 - Interface clients : ``ens192`` *10.251.200.1/24*.
 - Interface externe : ``ens224`` *10.251.201.2/24*.

La machine possédant plusieurs interfaces, il est nécessaire de bien configurer sa table de routage. La route par défaut doit passer par l'interface externe ``ens224`` et des routes statiques doivent être positionnées sur l'interface client ``ens192`` dans le cas ou elle comprendrait plusieurs subnets.

La configuration suivante peut être mise en place dans le fichier **/etc/network/interfaces** :

.. code-block:: bash

   source /etc/network/interfaces.d/*

   # The loopback network interface
   auto lo
   iface lo inet loopback

   # Interface interne
   allow-hotplug ens192
   iface ens192 inet static
           address 10.251.200.1/24

   # Interface externe
   allow-hotplug ens224
   iface ens224 inet static
           address 10.251.201.2/24
           gateway 10.251.201.1
           dns-nameservers 8.8.8.8

La table de routage initiale doit alors ressembler à :

.. code-block:: bash

   Destination     Passerelle      Genmask         Indic Metric Ref    Use Iface
   default         10.251.201.1    0.0.0.0         UG    0      0        0 ens224
   10.251.200.0    0.0.0.0         255.255.255.0   U     0      0        0 ens192
   localnet        0.0.0.0         255.255.255.0   U     0      0        0 ens224

Par la suite il vous faudra probablement ajouter une ou plusieurs routes statiques à destination du routeur interne si vous souhaitez utiliser le portail captif sur plusieurs sous réseaux *(VLANs)*. Nous recommandons alors de dédier un super-subnet comme un /16 à découper ensuite pour tous vos réseaux invités afin de simplifier les règles de routage.
