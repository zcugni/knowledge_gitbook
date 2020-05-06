# Machines

### Firewall

* Network access control device
* Often a combination of a packet filter, proxy server and NAT
* Means everything & nothing nowadays
* Can go from very basic to very secure and complex
* Enforce policy by dictating what traffic may pass from one segment to another
* Normally this control is based on TCP/IP ports & IP addresses and protocols, but servers can also have firewalls, usually software-driven.
* In general use a _default deny_ policy : every thing is forbidden unless explicitly permitted

### Intrusion Detection System \(IDS\)

* Scan payload to detect attack vectors it knows the signature of
* They come in 2 forms : 
  * NIDS \(Network IDS\), which monitor incoming traffic
  * HIDS \(Host IDS\), which monitor logs, file-system & os configuration changes

### Routers & Ethernet switches

* Can perform packet filtering or use _access control list_ \(ACL\)

### Proxy server

* Accepts requests, sanity-checks them and requests the resource on behalf on the client. \(May also modify the packets\)
* Can be very secure but limits the activity users can perform
* Not all network protocols can go through a proxy

### Load balancers

* Distribute network traffic between multiples hosts
* They might also change the packets in the process

