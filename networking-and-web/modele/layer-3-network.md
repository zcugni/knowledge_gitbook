# Layer 3 - Network

## Generalities

* The Internet Protocol \(IP\) deals with choosing a path \(**routing**\) between two hosts, enabling them to transfer data to each other
* "A single chunk of network data is called a **packet**"
* It's a **connectionless** protocol, meaning among other things that : 
  * A connection is not established beforehand between the hosts
  * IP packets sent to the same host won't necessarily go through the same route
* There's no reliability built into the protocol, meaning there is no acknowledgement of arrival, control of data \(except for the header checksum\) or re-transmission
  * This should be dealt by protocols in the transport layer, particularly TCP
* **Errors** are reported via the Internet Control Message Protocol \(**ICMP**\)
* It's comes in 2 versions :   **IPv4** & **IPv6** \(because there wasn't enough addresses in IPv4\)
  * Some host are only capable of using one of the 2 protocols
  * Others can do both and will responds by using the same one as the request when responding and their default configuration when requesting

## IP addresses & Netmask

* The Internet Protocol identifies host via addresses and they're grouped into **networks** or **subnets**
* The address is composed of : 
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
* _Internet Service Provider_ \(ISP\) allocates subnets to organizations and network administrators can further divide them
* Persistence :
  * Root DNS server addresses are almost permanent
  * Servers addresses can change but that needs to be coordinated
  * Desktop addresses change as they move around the network or reboot

## IPv4 addresses

* 32-bit number, generally expressed in this format `203.0.113.1`
* Each subnet contains a number of addresses equal to a power of 2
* The netmask also is a 32-bit number, generally expressed in this format `255.255.255.0` or `/24` \(in this case\)
* They were divided in classes but not anymore because it was too restrictive

### Private addresses & NAT

* IPv4 only has so many addresses available and not all hosts need to access the public Internet which is why we reserved some subnets for private use :
  * 10.0.0.0/8
  * 172.16.0.0/12
  * 192.168.0.0/16
* To access Internet through these IPs, the host must use a proxy or a **Network Address Translation** \(NAT\) :
  * It rewrites packets in flight
  * Maintains a table of connections and track their state to properly open & close them
  * Most home routers are NAT device
  * It's efficient, but it implies lying to all side of the network connection, which not all protocols handle \(FTP and VoIP needs special handling\)
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

## Packet

### Fragmentation

* Before describing the headers, I must explain fragmentation
* When the data needed to be sent is bigger than the MTU of the datalink layer, it's fragmented in multiple packets and re-assembled on arrival
* Part of the headers is used for that
* Packets can be mark as "Don't fragment", in which case they'll be discarded if they're too big



### Headers

* An ip packet is comprised of an header and it's data
* This is the structure of the header for **IPv4** packets :

![](../../.gitbook/assets/ip-headers.png)

* Version : 4 bits, indicates if it's IPv4 or IPv6
* Internet Header Length \(IHL\) : 4 bits,  the length of the header in 32-bits word
  * The length of the header can vary, so it indicates where the data section starts
  * The minimum value is 5
* The maximum length of an header is 60 bytes, and the typical one is 20 bytes
* Type of services ...
* Total length :  16 bits, gives the total length of the packets in bytes, headers included
  * The maximum possible length is 65,535 bytes, but it's advised against
  * Systems should be capable of sending and receiving packets up to 576 bytes \(after re-assembly if fragmentation was needed\)
* Identification : 16 bits
  * **WARNING** An identifying value assigned by the sender to aid in assembling the     fragments of a datagram
* Flags :  3 bits, used for fragmentation
  * Bit 0 : Reserved, must be zero
  * Bit 1 : 0 = May Fragment,  1 = Don't Fragment
  * Bit 2 :  0 = Last Fragment, 1 = More Fragments
* Fragment Offset :  13 bits
  * **WARNING** This field indicates where in the datagram this fragment belongs.The fragment offset is measured in units of 8 octets \(64 bits\).  The     first fragment has offset zero.
* Time to Live \(TTL\) :  8 bits  , the maximum time a packet can exist before it is discarded
  * Measured in seconds, but must be decremented by one on each hop even if a second hasn't passed
* Protocol :  8 bits
  * **WARNING** This field indicates the next level protocol used in the data     portion of the internet datagram
* Header Checksum :  16 bits  , a checksum on the header only
  * Since some header fields change    , it's recomputed and verified at time
  * While computing the checksum, the value of it's field is 0
* Source Address:  32 bits
* Destination Address:  32 bits
* **WARNING** Add options details
* Padding:  Added to ends the header on a 32 bit boundary

## Loopback and localhost

* All host have a _Loopback interface_ that is only logical, without hardware representation and an underlying layer 2
* In IPv4 the whole 127.0.0.1/8 is _localhost_ 
* In IPv6 it's the `::1` address
* This address let the machine **connect to itself**
  * An host can only connect to it's own loopback interface, not others

## Multihoming and aliases

An host can :

* Have different interfaces on different subnets \(**multihomed**\)
  * To speak with the outside, it will use the closest address to the default gateway
  * You can transform it into a router if packet forwarding is enabled
* Attache multiple IP adresses to one network interface \(**alias**\)
  * To speak with the outside, it will use it's primary address 
* Both
* Locally, they'll use the address corresponding to the subnet with which they communicate

## Packets

{% hint style="info" %}
Add IP headers

"The IP header for packets in this layer is 20 bytes in size and consists of various fields and bitflags as defined in RFC 791."

"the IP header has a protocol field to describe the type of data in the packet and the source and destination addresses for routing. In addition, the header carries a checksum, to help detect transmission errors, and fields to deal with packet fragmentation
{% endhint %}

* **Fragmentation** : the data can be too big to fit into one packet, in which case it's fragmented and divided between multiples packets, each with their own headers
  * The packets are numbered via a **fragment offset** written in the header
  * The fragment are re-assemble on arrival

## Sources

* _Networking for Systems Administrators_ by Michael W. Lucas
* [openclassroom](https://openclassrooms.com/fr/courses/1561696-les-reseaux-de-zero/1562627-les-masques-de-sous-reseaux-a-la-decouverte-du-subnetting) \(in french\)
* _Hacking : The Art of Exploitation_ by John Erickson
* Misc Research

