# Security hardware

### Firewall

* Network access control device
* Often a combination of a packet filter, proxy server and NAT
* Means everything & nothing nowadays
  * Can go from very basic to very secure and complex
* Enforce policy by dictating what traffic may pass from one segment to another
* Normally this control is based on TCP/IP ports & IP addresses and protocols, but servers can also have firewalls, usually software-driven
* In general use a _default deny_ policy : every thing is forbidden unless explicitly permitted

### Intrusion Detection System \(IDS\)

* Scan payload to detect attack vectors it knows the signature of
* They come in 2 forms : 
  * Network IDS \(NIDS\) which monitor incoming traffic
  * Host IDS \(HIDS\) which monitor logs, file-system & os configuration changes

### Routers & Ethernet switches

* Can perform packet filtering or use A_ccess Control List_ \(ACL\)
* Switches routes packets through the local network using mac addresses
* Router routes the packets through the whole network using ip addresses
  * And locally i guess

### Proxy server

* Accepts requests, sanity-checks them and requests the resource on behalf on the client
  * May also modify the packets
* Can be very secure but limits the activity users can perform
* Not all network protocols can go through a proxy

### Load balancers

* Distribute network traffic between multiples hosts
* They might also change the packets in the process

## Source

* _Networking for Systems Administrators_ by Michael W. Lucas

