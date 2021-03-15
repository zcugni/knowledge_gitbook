# Layer 2 - Datalink

## Generalities

* Deals with transferring data between 2 network entities
* **Ethernet** is the standard protocol
* "A single lump of datalink data is called a _**frame**_"
* It has a _Maximum Transmission Unit_ \(MTU\) defining it's size
  * Older Ethernet equipment have an MTU of 1500 bytes
  * Gigabit Ethernet support 9000 byte "jumbo" frames
  * Packets are usually larger than that so they are divided into multiple ones that are then reassembled on reception

## Ethernet

* Broadcast protocol
* A section of Ethernet where all the hosts communicate directly without involving a router is called a \(depending on the vendor\) :
  * Broadcast domain
  * Segment 
  * Local area network \(LAN\)
* Ethernet cables are ranked by category numbers, higher ones being better
  * cat5 is the current standard, at 100 megabits

{% hint style="info" %}
To Do : Describe ethernet headers

"An Ethernet header is 14 bytes in size and contains the source and destination MAC addresses for this Ethernet packet."

"Any packet’s Ethernet header includes a type value that describes the packet. This type is used to specify whether the packet is an ARP-type message or an IP packet."
{% endhint %}

## Switch

* Each host is wired to a port on an Ethernet switch
  * Since each host has its own cable, **collision domain** are isolated
  * In a switched network, packets are only sent to their recipient
    * In a unswitched one, they were sent to every one, expecting the device not to look at the contents of others
    * A device can be made to look into packet by setting the **promiscuous** mode with `ifconfig`
* The **hub** and the **shared bus** are unswitched netwok
  * The shared bus also had only one cable for every device, and needed to use CSMA/CD for collision
  * When a collision occurred, all machines shut up and waited a random period of time before starting communication again

## MAC Address

* Every device on an Ethernet network has a universally unique identifier, called a _MAC address_ or _Ethernet address_
* 48 bits long
* Usually written in hex like this `52:54:00:3b:2b:25` \(windows uses `-` instead of `:`\)
* The first 6 numbers identifies the Ethernet card manufacturer

## Speed and duplex

* I don't completely understand but the Ethernet speed isn't how fast the card passes traffic, but how fast the datalink protocol can pass traffic between the network card and the switch \(given that the hardware can keep up\)
  * What that means is that the protocol speed indicate that both sides speak the same language, but not how they actually exchange traffic
  * Aka, a gigabit card doesn't actually exchange data at this rate
* Speed wise, we're generally at 100 megabits/1 gigabit per second for desktop and 10 gigabits for servers
* Half-duplex can either send or receive data while **full duplex** can do both
  * All modern connections do full
* Both sides must agree on speed and duplex
  * Modern equipment **autonegotiate** that and agree on the fastest setting supported by both side
  * For gigabits equipment, the autonegotation also deals with other stuff and is a mandatory part of the protocol
    * If it fails, ethernet cards set themselves by default at 10 megabits, half duplex

## _ARP - Address Resolution Protocol_

* The glue \(for IPv4\) that attach the network & datalink layers by translating MAC addresses to IPv4 addresses
* The **ARP table** contains the correspondences
  * If an IP isn't in it, an **ARP request** is sent on the broadcast \(meaning that it will reach every host of the broadcast domain\), asking to which MAC address it corresponds
  * The machine with the desired ip will send an **ARP reply**
  * An host that receives a reply \(even without having send a request\) will change the content of it's table \(except if it was explicitly marked as permanent\)
* A cache keeps old entries but expires after some time
  * If an host changed it's mac address but the old one is still in the ARP table, the connection will fail

## _ND - Neighbor Discovery_ 

* The glue \(for IPv6\) that attach the network & datalink layers by translating MAC addresses to IPv6 addresses
* Made to work on all datalink protocols, not just Ethernet \(even though Ethernet is still the most current\)
* Globally similar to ARP, there's just more possible state for addresses :
  * Reachable : Currently live on the network
  * Stale : Were lived but since expired
  * Permanent : Either local or special-purpose that are always present
  * Failed : Searched for but not found

## VLAN

* To have a host on multiple LAN \(for example a firewall that access both side of the network\), there's 2 possibilities :
  * Double the network interfaces \(not optimal except if security & flow rate is important\)
  * Using Virtual Lan \(VLAN\) to simulate multiple LAN on the same equipment
* There represented by an additional tag in a frame going from 1 to 4296
  * If absent, the frames comes from the default LAN
* OS use virtual interfaces or sub-interfaces to deal with them
  * Each needs it's own ip configuration and the switch also needs to be configured for it
* VLAN are sometimes referenced as 802.1Q as it's the standard that won this protocol war.
* VLAN, tagging and VLAN tagging all refer to VLANs
* Depending on the vendor a _Network Trunk_ :
  * Combine multiple physical layers into one datalink layer \(aka, multiple cables for one connection, so that you have redundancy\)
  * Is one network cable that carries multiple VLANs

## Errors

* The datalink layer can go bad without completely failing.
* Most common errors are :
  * frame errors
  * drops
  * overruns
  * collisions

## Sources

* _Networking for Systems Administrators_ by Michael W. Lucas
* _Hacking : The Art of Exploitation_ by John Erickson
* Misc Research

