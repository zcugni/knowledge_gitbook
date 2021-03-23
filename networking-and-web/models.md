# Models

## Generalities

* 2 models divided by layers describe the hierarchy between network technologies, protocols, etc
  * Open System Interconnect \(OSI\) : 7 layers, detailed but not representative of real word implementation 
  * **TCP/IP** : 5 layers, more representative of the real world, I detail it here
* If an inferior layer doesn't work, the ones above won't either

{% hint style="info" %}
Write this elsewhere :

There's a lot more than just these 3 protocols and you can find a list in :

C:\Windows\System32\drivers\etc\protocol

/etc/protocols \(a lot of those are now out of date\).  
Each protocol has an assigned number used when analyzing & filtering packets
{% endhint %}

## Layers

* 1 - **Physical** : Composed of the physical aspect, like cable, radio waves, etc
* 2 - **Datalink** : Deals with transferring data between 2 network entities
  * A single lump of datalink data is called a _**frame**_
  * Example protocol : [Ethernet](https://zcugni.gitbook.io/notes/networking-and-web/protocols/layer-2-datalink)
* 3 - **Network** : Deals with choosing a path between two hosts \(**routing** them\), enabling them to transfer data to each other
  * A single lump of datalink data is called a _**packet**_
  * Example protocol : [IP](https://zcugni.gitbook.io/notes/networking-and-web/protocols/layer-3-network)
* _4 - **Transport** :_ Provides host-to-host communication services for the upper layer
  * A single lump of datalink data is called a _**segment**_
  * Example protocols : [TCP](https://zcugni.gitbook.io/notes/networking-and-web/protocols/clean-tcp-2), [UDP](https://zcugni.gitbook.io/notes/networking-and-web/protocols/udp-user-datagram-protocol), [ICMP](https://zcugni.gitbook.io/notes/networking-and-web/protocols/icmp-internet-control-message-protocol), etc
* _5 - **Application**_
  * The OSI model divides this layer in 3 \(session, presentation & application\) but in reality they are often mixed, like in the TCP/IP model
  * Example protocols : [HTTP](https://zcugni.gitbook.io/notes/networking-and-web/protocols/http-hypertext-transfer-protocol), LDAP, SMTP, etc

### Communication between layers

* Each layer communicates only with the 2 adjacent ones
* Data is **encapsulated** between each layer
  * The header + payload  becomes the payload part of the next layer

![](../.gitbook/assets/encapsulation.png)

{% hint style="info" %}
I need to write this somewhere else :

Information on the data link layer \(like the mac address\) change at each hop, while the ip address of the destination stays the same
{% endhint %}

## Sources

* _Networking for Systems Administrators_ by Michael W. Lucas
* Pentesterlab
* Misc Research

