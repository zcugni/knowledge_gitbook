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

* Works over IP
* Way simpler & faster than TCP but not reliable at all
  * Application that uses it handle all problem of lost/damaged/out of order packets
* The combination of the header + data is called a **datagram**
* **Connectionless**
* **Stateless**
  * Each datagram are independent from each other
* VPNs use it when they don't use VPN specific protocols like IPSec
* It's easy to forge the source of an UDP packet, so it's often heavily filtered

### Headers

![](../../.gitbook/assets/udp%20%281%29.png)

* Source Port :  16 bits, optional
* Destination Port : 16 bits
* Length : 16 bits, the length in bytes of the datagram
* Checksum : 16 bits
  * Verifies the integrity of the datagram
  * Before computing the checksum, a pseudo-header is prefixed to the datagram, in order to protect against misrouted datagram. It contains :
    * The source  & destination addresses
    * The protocol
    * The  UDP       length
    * It retrieves the first 3 info from the IP
  * It's value is the 16-bits 1's complement of the 1's     complement sum of the pseudo-header, the header and this data
    * For computation, it's value is set at 0

## TCP - _Transmission Control Protocol_

See this [page](https://zcugni.gitbook.io/notes/networking-and-web/modele/clean-tcp-2)

## Logical Ports

* TCP & UDP use _logical ports_ to multiplex connections between machines, permitting one host to serve different services to multiple hosts
* A service **binds** itself to a port
* A port is a number between 0 & 65'535
* TCP & UDP ports aren't the same \(but the range is\)
* Each internet service has a standard port defined by the _Internet Assigned Numbers Authority_ \(IANA\)
  * Ports between 49'152 & 65'53 aren't attributed to anything
* A connection is defined by the IP address + port of the source and the IP address + port of the destination
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



