# Layer 3 - Network

## Généralités

* Maps connectivity between hosts \("How do i get to this host ?", "_Can_ i get to this host ?"\).
* Fournit une interface consistante pour les couches supérieurs, quelle que soit l'implémentation des couches inférieurs.
* "A single chunk of network data is called a _**packet**_".
* Internet utilise l'_Internet Protocol_ \(IP\) qui vient en 2 versions : IPv4 & IPv6.
* Les hosts peuvent utiliser uniquement l'un des 2 protocoles, ou les 2 à la fois, auquel cas ils répondront avec le même protocole que la requête arrivante et suivront leur configuration pour les requêtes sortantes.
* On a local network, you need and IP address & a subnet mask. To communicate beyond that, you also need a default gateway and the address of your DNS is a definite plus.

## Addresses IP

* Each host is given a globally unique \(on it's network\) IP address
* A block of IP addresses is called a _network_ or _subnet_
* Each addresses comes with a netmask indicating the size of the subnet by specifying which part of the address is constant and which part change.
* The first address of the subnet is reserved to be the network address and the last address to be the broadcast one.
* Host can only communicate directly with hosts on the same IP subnet. To reach the outside, they need to go through a router even if they're on the same Ethernet.
* _Internet Service Provider_ \(ISP\) allocates subnet to an organization and a network administrator can further divide it.
* Root DNS server addresses are almost permanent, servers addresses can change but that needs to be coordinated and desktop addresses change as they move around the network or reboot.
* Un certain nombre d'addresses IP sont réservées pour différentes utilisations

### IPv4 addresses

* 32-bit number, generally expressed in this format `203.0.113.1`
* Each subnet contains a number of addresses equal to a power of 2.
* The netmask also is a 32-bit number, generally expressed in this format `255.255.255.0` or `/24` \(in this case\).
* The `192.0.2.1/24` notation is called the _Classless Inter-Domain Routing_ \(CIDR\) notation.
* Elles étaient avant divisées en classes mais c'était trop restrictif.

#### Private addresses & NAT

* IPv4 only has so many adresses available and not all hosts need to access the public Internet which is why we reserved some adresses for private use, these 3 networks :
  * 10.0.0.0/8
  * 172.16.0.0/12
  * 192.168.0.0/16
* Pour accéder à Internet malgré l'utilisation d'une de ces ip, l'host doit passer par un proxy ou un _Network Address Translation_ \(NAT\)
  * NAT rewrites packets in flight
  * Maintains a table of connections and track their state to properly open & close them
  * Most home routers are NAT device
  * It's efficient, but it implies lying to all side of the network connection, which not all protocols handle. FTP and VoIP need special handling in NAT.
  * You can apply filters to block some traffic
  * Not really secure

### IPv6 addresses

* 128-bit addresses, usually written like this : `2a03:2880:2130:cf05:face:b00c:0:`
  * Leading zeros aren't written so `:0:` = `:0000:`.
  * Consecutive blocks of `:0000:` can be written with `::` \(but only once per addresses\)
* Globalement similaire à l'IPv4
* Avant, on pouvait déduire la MAC address depuis la dernière partie de l'addresse, but for privacy reasons it's not the case anymore \(normally\). \(C'était en cours de changement quand le livre était écrit, j'ignore où ça en est depuis\)
* En plus de son adresse primaire, un host peut aussi avoir plusieurs addresses temporaires. This helps with the privacy issue of tying an IP address to hardware. Before that the burden of this question lied on the protocols.
* There's no NAT in IPv6 because there's no shortage of addresses and the security that NAT could add disappeared years ago
* IPv6 has features that change IP addresses in flight \(like prefix translation\), but it's for redundancy, routing or resilience.
* Les subnet sont généralement séparé au niveau des ":", donc par multiple de 16 : /16, /32, /64, ...
* /64 est le subnet standard
* Généralement, les 48 premier bits sont utilisés pour l'host, les 16 suivant pour le subnet id et les 64 derniers pour l'interface id \(le client\).
* Quand le livre a été écrit, IPv6 n'était pas encore bien établi partout donc ce traffic était des fois transféré via des tunnels IPv4, auquel cas c'est pas super performant.

#### Autoconfiguration & link-local addresses

* Basic network configuration is built into the protocol.
* IPv6 clients on a /64 network automatically learn their IPv6 address and discover the router.
* They can autoconfigure themselves even without a router present
* All addresses beginning with _fe8_ are _link-local addresses_, valid only on that specific interface's broadcast domain.
* So IPv6 hosts on that Ethernet network can find each other and communicate via the link-local address. 
* They're always /64
* The OS attach the interface name to the link-local address, it's often written as `%interface_nb` at the end of the address
* Server with static addresses should be careful with IPv6 autoconfiguration.

## Loopback and localhost

* All host have a _Loopback interface_ that is only logical \(pure software\), without hardware representation and an underlying layer 2.
* In IPv4 the whole 127.0.0.1/8 is _localhost_ 
* In IPv6 it's the `::1` address
* This address let the machine connect to itself. An host can only connect to it's own loopback interface, not others.

## Router

* Sends traffic from one IP subnet to another
* Might also convert one physical layer to another
* They can connect to multiple subnets and make intelligent routing decision
* When an host needs to get to a system that's not on the local network, it will send it's packet to the _default gateway_ which is usually the router.

### Multihoming and aliases

An host can :

* Have different interfaces on different subnets \(multihomed\)
* Attache multiple IP adresses to one network interface \(alias\)
* Both
* Localement, les 2 utilisent une adresse correspondant au subnet avec lequel ils communiquent
* Pour rejoindre l'extérieur, un multihomed utilisera l’adresse la plus proche du default gateway alors qu'un host avec alias utilisera son adresse "primaire".
* A multihomed host can be transformed into a router if packet forwarding is enabled.

