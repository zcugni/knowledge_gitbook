# Layer 4 - Transport

## Generalities

* Provides host-to-host communication services for the upper layer
* "A piece of transport layer data is called a _**segment**_"

## _ICMP - Internet Control Message Protocol_

* Low-level connectivity message between hosts \(availability, routing, status, errors, etc\)
* Every host implementing IP needs ICMP, it runs silently in the background
* It's often block for security reasons but that isn't useful because attackers can gain info in other ways and it might cause problems for you
* Transmit similar kind of message over IPv4 & IPv6 but internally very different
* Usage example :
  * **Ping** \(use **ICM Echo Request** & **Reply**\)
  * Datalink error message \(for example frame is too large\)
  * Tell traffic to go another way

## _UDP - User Datagram Protocol_

* Most minimal transport protocol \(0 verification/feedback\)
* UDP is used for applications that do their own data flow error management
* VPNs use it when they don't use VPN specific protocols like IPSec

{% hint style="info" %}
headers : The UDP header, defined in RFC 768, is relatively tiny. It only contains four 16-bit values in this order: source port, destination port, length, and checksum.
{% endhint %}

* This   protocol  assumes  that the Internet  Protocol  \(IP\)  \[1\] is used as the

  underlying protocol.

* Source Port is an optional field, when meaningful, it indicates the port

  of the sending  process,  and may be assumed  to be the port  to which a

  reply should  be addressed  in the absence of any other information.  If

  not used, a value of zero is inserted.

* Length  is the length  in octets  of this user datagram  including  this

  header  and the data.   \(This  means  the minimum value of the length is

  eight.\)

* Checksum is the 16-bit one's complement of the one's complement sum of a

  pseudo header of information from the IP header, the UDP header, and the

  data,  padded  with zero octets  at the end \(if  necessary\)  to  make  a

  multiple of two octets.

  * The pseudo  header  conceptually prefixed to the UDP header contains the

    source  address,  the destination  address,  the protocol,  and the  UDP

    length.   This information gives protection against misrouted datagrams.

    This checksum procedure is the same as is used in TCP.

* he UDP module  must be able to determine  the  source  and  destination

  internet addresses and the protocol field from the internet header.

### Flaws

* Called _connectionless_ because it considers each packet has being independent from the others, there's no notion of "data stream"
  * The packets have no defined order and won't react to others having problems
* The source of a packet is easily forged \(which is why they are often heavily filtered\)
* A host transmitting UDP segment has no way to tell if it actually reaches its destination

### Advantages

* It lets the applications build their own correction mechanisms tuned exactly to their needs
* It's really fast

## TCP

See ...

## Logical Ports

* TCP & UDP use _logical ports_ to multiplex connections between machines, permitting one host to serve different services to multiple hosts
* A service **binds** itself to a port
* A port is a number between 0 & 65'535
* TCP & UDP ports aren't the same \(but the range is\)
* Each internet service has a standard port defined by the _Internet Assigned Numbers Authority_ \(IANA\)
  * Ports between 49'152 & 65'53 aren't attributed to anything
* A connexion is defined by the IP address + port of the source and the IP address + port of the destination
  * In IPv4 it's written `ip:port`
  * In IPv6 `[ip]:port`

### Services File

* `/etc/services/` On Unix
* `C:\\Windows\System32\drivers\etc\services` On windows
* List services commonly used on the machine and ports normally used for them
  * Some programs use this file to see on what port they should bind on
  * `tcpdump` Use it to look up which services are running and on what
* 5 fields by line :
  * Name of the protocol
  * Port number
  * Transport protocol \(separated from the port number by a /\)
  * Other name of the service \(optional\)
  * `# Comments`
* You're not obligated to follow it and not doing it might be a bypass for badly configured firewalls
* However some programs/host won't accept certain port and you might get strange result by using non standard port

## Sockets

* Communication endpoint for processes
  * Abstraction that takes care of the detail of the transport layer
    * **Raw sockets** let you deal with it yourself, but they demand root access and are inconsistent across systems
  * They're kind of like pipes, but more complex
  * Processes \(for example a web server\)  open a socket on the port they listen to
    * One process can open any number of sockets as long as the OS doesn't object
    * An _open_ or _listening_ socket is waiting for a connection
    * It can accept any numbers of connections so long as all the clients have unique source IP addresses & ports
* 4 types, 2 most common :
  * **Stream** socket for TCP/IP
  * **Datagram** socket for UDP
* Sockets are identified via socket descriptors \(which are actually **file descriptor**\)
* Both Windows & Linux have **local sockets** : system entities on the filesystem or memory that accept connections from other programs
* One of the socket protocol is _Inter-Process Communication_ \(IPC\) which is contained entirely in memory

## Root User

* Most Unix systems only allows the root account to open TCP & UDP ports below 1024
  * Because those are normally assigned to the most popular & important Internet services
* Unprivileged users can run servers on higher port numbers
* If an intruder takes control of a software listening on a port, it gains the privilege of the user running the process -&gt; **Don't run processes listening to port as root**
* There's a trick to start as root and drop the privilege afterwards, etc, pto prevent that
* Windows doesn't reserved port for admin \(but there's a registry setting to enable that\) so you should never run a process as admin

## More Transport Protocol

* There's a lot more than just these 3 protocols and you can find a list in :
  * `C:\Windows\System32\drivers\etc\protocol`
  * `/etc/protocols` \(a lot of those are now out of date\).
* Each protocol has an assigned number used when analyzing & filtering packets

## Sources

* _Networking for Systems Administrators_ by Michael W. Lucas
* _Hacking : The Art of Exploitation_ by John Erickson
* Pentesterlab
* Misc Research



