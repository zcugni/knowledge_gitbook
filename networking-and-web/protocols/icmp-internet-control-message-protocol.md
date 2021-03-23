# ICMP - Internet Control Message Protocol

## Generalities

* Low-level connectivity message between hosts \(availability, routing, status, errors, etc\)
* Every host implementing IP needs ICMP, it runs silently in the background
* It's often block for security reasons but that isn't useful because attackers can gain info in other ways and it might cause problems for you
* Transmit similar kind of message over IPv4 & IPv6 but internally very different
* Usage example :
  * **Ping** \(use **ICM Echo Request** & **Reply**\)
  * Datalink error message \(for example frame is too large\)
  * Tell traffic to go another way

## Sources

* _Networking for Systems Administrators_ by Michael W. Lucas
* _Hacking : The Art of Exploitation_ by John Erickson

