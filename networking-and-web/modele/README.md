# Models & Protocols

## Generalities

* Tech, protocols, etc are divided by layer
* If an inferior layer doesn't work, the ones above won't either
* Each layer communicates only with the 2 adjacents one
* There's 2 models :
  * Open System Interconnect \(OSI\) : 7 layers, detailed but not representative
  * TCP/IP : 5 layers, more representative, I detailed it here

## Communication between layers

* Data is transmitted by creating a pack of the payload and the header of the protocol and giving it to the next layer
* The header + payload combo becomes the payload of the next layer, which then adds it's header to it
* This process is called _encapsulation_

![](../../.gitbook/assets/encapsulation.png)

* Information in the data link layer \(like the mac address\) change at each hop
* While the ip address of the destination stays the same

## Layer 1 - Physical

The first layer is composed of the physical aspect, like cable, radio waves, etc

## Troubleshooting

| Layer | Tools |
| :--- | :--- |
| Physical | link light, ipconfig/ifconfig, cable replacement |
| Datalink | arp, ND, tcpdump |
| Network | ping, traceroute |
| Transport | netstat, netcat, tcpdump |
| Application | Logs, debuggers |

## Sources

* _Networking for Systems Administrators_ by Michael W. Lucas
* Pentesterlab
* Misc Research

