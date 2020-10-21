# Vulnerabilities

## Basic terminology

* **Sniffing**  : Intercepting messages between devices, you can forward/redirect or discard them if you want
* **Spoofing** : Forging the source address of the packet to be different from what it should be
  * Protocols don't/can't check for that
* **ARP poisoning** : Send spoofed ARP replies to the ARP cache entries to be overwritten with our data
* **ARP redirection** : 
  * Poison the arp cache of the two victims so that they both think you're the other one
  * Forward the packets after you've intercept them
  * The victims will periodically sent out ARP request and receives replies, so you need to poisened them regularly
* These can also be done on the **default gateway**

## TCP/IP hijacking

* We must be on the same network as the victim in order to sniff the sequence number in the TCP header
* There's different use of that :
  * Impersonate the victim by sending a spoofed packet from it's IP and sending an ACK packet with the correct sequence number \(but i'm not sure if the ack packet needs to be also spoofed\)
  * Send an RST packet with the correct sequence number to close the victim connection
  * Desynchronizing the victim and the host
    * Send a spoofed valid packet with the correct sequence number to the host, it will respond to the victim with an incremented sequence number, so the victim will reject it \(and further packets from the host\)
    * However, since the host as legitimately incremented the sequence number, it will also reject packet from the victim from now on
    * You can then impersonate the victim by using the incremented sequence number and continuing the connection with the host

## Iddle scan

* Used to scan ports on a machine
* Find an host that isn't sending or receiving traffic and that uses predictable IP IDs \(they change by a fixed amount for each packet\)
* Get the current IP ID of the idle host by sending it a SYN packet or an unsolicited SYN/ACK packet
  * Repeat this a few time to get the increment
* Send a spoofed SYN packet with the idle host's IP address to a port on the target machine
  * If it's listening, a SYN/ACK packet will be send to the idle host, that will respond to this unsolicited confirmation with an RST packet, incrementing it's ID
  * If it's not listening, it won't send anything, therefore not causing the idle host to increment it's ID

{% hint style="info" %}
Shouldn't i put this in the nmap section ?
{% endhint %}

## Denial of service

### Flooding

* This consist of sending multiples request to the victim so that it uses all it's bandwidth to respond to us, making it impossible to accepts legitimate request
* With ping, it's called a **ping flood**
* **SYN flood** sends multiples SYN request to the victim with spoofed non-existent source
  * The OS keeps track of connexion that started an handshake, but they have only so much space for it
  * The goal is to fill this space with half-open connexions that will timeout, effectively denying real connexion
  * This is now prevented by **SYN cookies**
* **Amplification** consists of sending ICMP or UDP echo request to a broadcast address with a spoofed source of the victim's IP
  * The whole broadcast domain will send replies to the victim
* **Distributed DOS** uses compromised machines, called **bots** \(organized in **bot nets**\), to flood a victim 
  * This also as the advantages of hiding the attacker within the bots

### Ping of death

* This has been patched for a long time
* ICMP echo message should be a maximum of 84 bytes with headers, but IPv4 packets \(including ping\), can contains up to 65'536 bytes of data
  * System receiving pings of this size often crashed do to a buffer overflow when reassembling the fragmented packets

### Teardrop

* Sending packets with overlapping fragmented offset made the system crash on reassembly

## Sources

* _Hacking : The Art of Exploitation_ by John Erickson

