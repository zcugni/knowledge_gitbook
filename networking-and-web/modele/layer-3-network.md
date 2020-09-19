# Layer 3 - Network

## Generalities

* Maps connectivity between hosts \("How do i get to this host ?"\)
* "A single chunk of network data is called a _**packet**_"
* Internet uses the _Internet Protocol_ \(IP\) which comes in 2 versions : **IPv4** & **IPv6** \(because there wasn't enough addresses in IPv4\)
  * Some host are only capable of using one of the 2 protocols
  * Others can do both and will responds by using the same one as the request when responding and their default configuration when requesting

## IP addresses & Netmask

* IP addresses are grouped into **networks** or **subnets**
* An host is identified by it's IP address, which is composed of :
  * A static part for the network
  * A variable part that changes for each host
* The mask tells you which part is what
  * For the IPv4 address 158.37.0.0, the mask **255.255.248.0** indicates that 21 bits are reserved for the network \(11111111.11111111.11111000.00000000\), giving you 1800 possible hosts
  * It can also be written **158.37.0.0/21**, this is called the **Classless Inter-Domain Routing** \(CIDR\) notation
* Some addresses of a network are reserved :
  * The first for the **network address** 
  * The last for the **broadcast address**
* Host can only communicate directly with hosts in the same subnet
  * To reach the outside, they send their packets to their **default gateway**, which is usually the **router**
* _Internet Service Provider_ \(ISP\) allocates subnets to organizations and network administrators can further divide it
* Persistence :
  * Root DNS server addresses are almost permanent
  * Servers addresses can change but that needs to be coordinated
  * Desktop addresses change as they move around the network or reboot

## IPv4 addresses

* 32-bit number, generally expressed in this format `203.0.113.1`
* Each subnet contains a number of addresses equal to a power of 2
* The netmask also is a 32-bit number, generally expressed in this format `255.255.255.0` or `/24` \(in this case\).
* They were divided in classes but not anymore because it was too restrictive

### Private addresses & NAT

* IPv4 only has so many adresses available and not all hosts need to access the public Internet which is why we reserved some subnets for private use :
  * 10.0.0.0/8
  * 172.16.0.0/12
  * 192.168.0.0/16
* To access Internet through these IPs, the host must use a proxy or a **Network Address Translation** \(NAT\) :
  * It rewrites packets in flight
  * Maintains a table of connections and track their state to properly open & close them
  * Most home routers are NAT device
  * It's efficient, but it implies lying to all side of the network connection, which not all protocols handle
    * FTP and VoIP needs special handling
  * You can apply filters to block some traffic
  * Not really secure

## IPv6 addresses

* 128-bit addresses, usually written like this : `2a03:2880:2130:cf05:face:b00c:0:`
  * Leading zeros aren't written so `:0:` = `:0000:`.
  * Consecutive blocks of `:0000:` can be written with `::` \(but only once per addresses\)
* Similar to IPv4
* Subnets are separated at the `:` so by multiple of 16
  * /64 is the standard subnet, divided as follows :
    * The first 48 bits identifies globally identifies the network 
    * The next 16 bits identifies a subnet within this network \(i think\)
    * The last 64 identifies the host
* Before, we could deduce the MAC address from the last part of the address, but for privacy reasons it's not the case anymore \(normally\)
* An host can have a primary address and temporary ones
  * Helps with the privacy issue of tying an IP to hardware
* There's no NAT in IPv6 because there's no shortage of addresses and the security that NAT could add isn't helpful anymore
* IPv6 has features that change IP addresses in flight \(like prefix translation\) for redundancy, routing or resilience

### Autoconfiguration & link-local addresses

* Basic network configuration is built into the protocol
* IPv6 clients on a /64 network automatically learn their IPv6 address and discover the router
* They can autoconfigure themselves even without a router present
* All addresses beginning with _fe8_ are _link-local addresses_, valid only on that specific interface's broadcast domain
  * So IPv6 hosts on an Ethernet network can find each other and communicate via the link-local address. 
  * The OS attach the interface name to the link-local address, it's often written as `%interface_nb` at the end of the address

## Loopback and localhost

* All host have a _Loopback interface_ that is only logical, without hardware representation and an underlying layer 2
* In IPv4 the whole 127.0.0.1/8 is _localhost_ 
* In IPv6 it's the `::1` address
* This address let the machine connect to itself. An host can only connect to it's own loopback interface, not others

## Multihoming and aliases

An host can :

* Have different interfaces on different subnets \(**multihomed**\)
  * To speak with the outside, it will use the closest address to the default gateway
  * You can transform it into a router if packet forwarding is enabled
* Attache multiple IP adresses to one network interface \(**alias**\)
  * To speak with the outside, it will use it's primary address 
* Both
* Locally, they'll use the address corresponding to the subnet with which they communicate

## Sources

* _Networking for Systems Administrators_ by Michael W. Lucas
* [openclassroom](https://openclassrooms.com/fr/courses/1561696-les-reseaux-de-zero/1562627-les-masques-de-sous-reseaux-a-la-decouverte-du-subnetting) \(in french\)
* Misc Research

