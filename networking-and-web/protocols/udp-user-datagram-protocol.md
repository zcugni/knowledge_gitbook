# UDP - User Datagram Protocol

## Generalities

* Works over IP
* Way simpler & faster than TCP but not reliable at all
  * Application that uses it handle all problem of lost/damaged/out of order packets
* \*\*\*\*[**Connectionless**](https://zcugni.gitbook.io/notes/theory/terminologie-misc#types-of-communication-protocols)\*\*\*\*
* \*\*\*\*[**Stateless**](https://zcugni.gitbook.io/notes/theory/terminologie-misc#types-of-communication-protocols)\*\*\*\*
* The combination of the header + data is called a **datagram**
  * Each datagram are independent from each other
* VPNs use it when they don't use VPN specific protocols like IPSec
* It's easy to forge the source of an UDP packet, so it's often heavily filtered

## Headers

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

## Sources

* _Networking for Systems Administrators_ by Michael W. Lucas
* _Hacking : The Art of Exploitation_ by John Erickson
* [RFC 768](https://tools.ietf.org/html/rfc768)

