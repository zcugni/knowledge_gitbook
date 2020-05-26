# Layer 4 - Transport

## Généralités

* C'est là que le transfert se fait 
* "A piece of transport layer data is called a _**segment**_". \(But generally we speak of UDP or TCP packets, referencing a segment already wrapped inside an IP packet\)
* _Transport Control Protocol_ runs over IP but the name TCP/IP refers to the whole family of protocols related to these two : ICMP, UDP and even SCTP, ESP, AH, etc.
* Transport protocols run over both IPv4 & IPv6. The headers change but the concept remains the same.

## _ICMP - Internet Control Message Protocol_

* Low-level connectivity message between hosts \(availability, routing, status messages, etc\)
* Tous les hosts implémentant IP doivent aussi implémenter ICMP. Il tourne silencieusement en arrière-plan.
* Vital, don't block it \(intruders can get information other ways anyway\)
* Transmit similar kind of message over IPv4 & IPv6 but internally very different
* Exemple d'utilisation :
  * Ping
  * Datalink error message \(for example frame is too large\)
  * Tell traffic to go another way

## _UDP - User Datagram Protocol_

* Most minimal transport protocol \(0 verifications/feedback\)
* UDP is used for applications that do their own data flow error management
* VPNs use it when they don't use VPN specific protocols like IPSec.

### Flaws

* It considers each packet has being independent from the others, there's no notion of "data stream". \(It's sometime called _connectionless_\). The packets have no defined order and won't react to others having problems.
* The source of a packet is easily forged \(which is why they are often heavily filtered\).
* A host transmitting UDP packets has no way to tell if it actually reaches its destination.

### Advantages

* It lets the applications build their own correction mechanismes tuned exactly to their needs.
* It's really fast

## _TCP - Transmission Control Protocol_

* Carry data between hosts and includes error checking, congestion control and retransmission of lost data
* More complex than UDP
* _connected_ protocol since it consider packets as a stream
* Application using TCP expects that exactly the traffic sent is delivered.
* Includes much of the error handling UDP lacks :
  * The receiver acknowledges every packet received
  * The sender retransmits any packet not acknowledged
  * Packets have a specific order \(switches and router might send them out of order but the receiver will re-arrange them back before transmitting to the application\)

### Handshakes

The _three-way handshale_ establish a connection between 2 hosts :

1. _SYN-SENT_. A _synchronization request_ \(SYN request\) is sent by the client. It comes from a random high-numbered port on the client to a specific port on the server. A random _seq_ number is chosen
2. _SYN-ACK_. The server acknowledge the request \(the _ack_ number is the initial syn seq one + 1\) and respond with it's own SYN request \(with another randon seq number\). The request comes from the requested port on the server to the client source port. 
3. _ACK_. The client acknowledge the server SYN request, the connection is established. \(Also by upping the seq number by 1\)

![](../../.gitbook/assets/ack.png)

* The OS forward the data stream to a program only after the three-way handshake is done. So if the connection stop after it, the problem lies in the server program but if it fails before, the OS didn't complete the connection.
* A SYN flood attack consist of sending lots of SYN request that never goes past the SYN-ACK stage.
* When the server and client finished exchanging data, both sides request and acknowledge teardown with states like _CLOSE_, _WAIT, TIME\_WAIT, FIN\_WAIT & LAST\_ACK \(\_four-way hanshake_\).

### Failures

* If a server or firewall rejet/block a connection, it generate a "connection refused" message on the client
* If the server simply ignores it, the client has a "connection timed out" message
* If the client or server has a problem during the connection, they can send a _TCP reset_ message. With that, they don't do the teardown shuffle.

## Logical Ports

* TCP & UDP use _logical ports_ to multiplex connections between machines, permitting one host to serve many different services to many hosts.
* For example, when a web server starts, it _binds_ to one or more logical port. \(I think that only one service can listen to a port at a time\)
* A port is a number between 0 & 65'535
* TCP & UDP ports aren't the same \(but the range is\)
* Each internet service has a standard port defined by the _Internet Assigned Numbers Authority_ \(IANA\)
* A connexion is defined by the IP address + port of the source and the IP address + port of the destination.
  * In IPv4 it's written ip:port
  * In IPv6 \[ip\]:port
* Client should use port in the 49'152 - 65'535 to connect to services because those aren't attributed to anything

## Services File

* `/etc/services/` On Unix
* `C:\\Windows\System32\drivers\etc\services` On windows
* List services commonly used on the machine and port normally used for them
* Some programs use this file to see on what port they should bind on and `tcpdump` use it look up which services are running and on what
* 5 fields by line :
  * Name of the protocol
  * Port number
  * Transport protocol \(separated from the port number by a /\)
  * Other name of the service \(optional\)
  * `# Comments`
* You're not obliged to follow it and not doing it might be a bypass for badly configured firewalls
* However some programs/host won't accept any port and you might get strange result by using non standard port

## Socket

* Communication endpoint for a process
* Virtual construction to plug communication into
* Used beside the network
* Both Windows & Linux have _local sockets_ : system entities on the filesystem or memory that accept connections from other programs
* _Inter-Process Communication_ \(IPC\) is another socket protocol, contained entirely in memory
* In TCP/IP, sockets listen for a network connection
* A process \(for example a web server\) open a socket on the port they listen to
* One process can open any number of sockets as long as the OS doesn't object
* _network socket_ is synonym to "open TCP/IP port"
* A socket waiting for a connection is said to be _open_ or _listening_
* Unlike a physical socket, a network one can accept any numbers of connections so long as all the clients have unique source IP addresses & ports

## Root User

* Most Unix systems only permit the root account to open TCP & UDP ports below 1024 \(because those are normally assigned to the most popular & important Internet services\)
* Unprivileged users can run servers on higher port numbers
* If an intruder takes control of a software listening on a port, it gains the privilege of the user running the process -&gt; **Don't run process listening to port as root**
* There's a trick to start as root and drop the privilege afterwards, etc, pour éviter cela
* Windows doesn't reserve port for admin \(but there's a registry setting to enable that\) so you should never run process as it

## More Transport Protocol

There's a lot more than just these 3 protocols and you can find a list in :

* `C:\Windows\System32\drivers\etc\protocol`
* `/etc/protocols` \(a lot of those are now out of date\).

Each protocol has an assigned number used when analyzing & filtering packets.

