# Layer 2 - Datalink

## Généralités

* Transforme les couches supérieurs en signaux à envoyer dans les cables
* Ethernet est le protocole standard
* "A single lump of datalink data is called a _**frame**_"
* It has a _Maximum Transmission Unit_ \(MTU\) defining it's size
  * Older Ethernet equipment have an MTU of 1500 bytes and all gigabit Ethernet support 9000 byte "jumbo" frames.
  * Packets are usually larger than that so they are divided into multiple one that are then reassembled on reception.

## Ethernet

* Broadcast protocol
* A section of Ethernet where all the hosts communicate directly without involving a router is called \(depending on the vendor\) :
  * _broadcast domain_
  * _segment_ 
  * _local area network_ \(LAN\)
* Ethernet cables are ranked by category number, higher ones being better.
  * cat5 is the current standard, at 100 megabits.

## Switch

* Each host is wired to a port on an Ethernet switch. Switch separate _collision domain_ to prevent them.
* Comparé au _Hub_, un Switch transmet les packets uniquement au destinataire \(avant tout le monde les recevaient mais ceux que ça ne concernait pas les ignoraient\)
* Comparé au _Shared Bus_, les appareils sont sur des cables différents qui sont eux reliés au Switch
  * A l'époque du _Shared Bus_, les appareils suivait le CSMA/CD pour gérer les collisions \(en gros tout les appareils se taisaient puis 1 reprenaient la communication après une période aléatoire\)

## MAC Address

* Every device on an Ethernet network needs a unique identifier, called a _MAC address_ or _Ethernet address_.
* 48 bits long
* Usually written in hex like this `52:54:00:3b:2b:25` \(windows uses `-` instead of `:`\)
* The first 6 numbers identifies the Ethernet card manufacturer
* Universally unique

## Speed and duplex

* I don't completely understand but the Ethernet speed isn't how fast the card passes traffic, but how fast the datalink protocol can pass traffic between the network card and the switch \(given that the hardware can keep up\). What that means is that the protocol speed indicate that both sides speak the same language, but not how they actually exchange traffic. Aka, a gigabit card don't actually exchange data at this rate.
* Speed wise, we're generally at 100 megabits/1 gigabit per second for desktop and 10 gigabits for servers
* Half-duplex can either send or receive data while **full duplex** can do both \(all modern connections do full\)
* Both sides must agree on speed and duplex
* Modern equipment **autonegotiate** that and agree on the fastest setting supported by both side. For gigabits equipment, the autonegotation also deals with other stuff and is a mandatory part of the protocol.
* If autonegotiation fails, ethernet cards set themselves by default at 10 megabits, half duplex.

## _ARP - Address Resolution Protocol_

* The glue \(for IPv4\) that attach the network & datalink layers by translating MAC addresses to IPv4 addresses.
* Si l'ip n'est pas dans l'_ARP table_ l'host qui émet la requête broadcast sur tout le _broadcast domain_ en demandant à qui appartient cette IP.
* Un autre host reconnaissant l'adresse réponds et la correspondance est ajoutée à la table ARP
* Le cache expire après quelques temps, et les connexions vers un host dont la MAC address a changée mais dont l'ancienne est encore dans la table n'aboutiront pas

## _ND - Neighbor Discovery_ 

* The glue \(for IPv6\) that attach the network & datalink layers by translating MAC addresses to IPv6 addresses.
* Made to work on all datalink protocols, not just Ethernet \(even though Ethernet is still the most current\).
* Globally similar to ARP, there's just more possible state for addresses :
  * Reachable : Currently live on the network
  * Stale : Were lived but since expired
  * Permanent : Either local or special-purpose that are always present
  * Failed : Searched for but not found

## VLAN

* Pour avoir un host sur plusieurs LAN \(par exemple un firewall ayant accès aux 2 côtés du réseau\), on peut :
  * Doubler les network interfaces \(peu optimale sauf si des questions de sécu & débit rentrent en jeu\)
  * Utiliser des _Virtual LAN_ \(VLAN\)
* Une VLAN permet de simuler plusieurs LAN sur un même équipement physique
* Représentée par un tag supplémentaire dans la frame Ethernet allant de 1 à 4096.
* S'il est absent, la frame appartient à la LAN par défaut, sinon à celle indiquée par le tag
* Les OS utilisent des virtuals interfaces ou sub-interfaces \(le terme varie en fonction de l'os\) pour les gérer. 
* Chaque virtual interface nécessite sa propre configuration IP et le switch doit aussi être configuré en conséquence.
* VLAN are sometimes referenced as 802.1Q as it's the standard that won this protocol war.
* VLAN, tagging and VLAN tagging all refer to VLANs
* Depending on the vendor a _Network Trunk_ :
  * Combine multiple physical layers into one datalink layer \(aka, multiple cables for one connection, so that you have redundancy\)
  * Is one network cable that carries multiple VLANs.

## Errors

* The datalink layer can go bad without completely failing.
* Les erreurs les plus communes sont :
  * frame errors
  * drops
  * overruns
  * collisions

