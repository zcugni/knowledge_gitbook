# Vulnerabilities

* **Sniffing**  : Intercepting messages between devices, you can forward/redirect or discard them if you want
* **Spoofing** : Forging the source address of the packet to be different from what it should be
  * Protocols don't/can't check for that
* **ARP poisoning** : Send spoofed ARP replies to the ARP cache entries to be overwritten with our data
* **ARP redirection** : 
  * Poison the arp cache of the two victims so that they both think you're the other one
  * Forward the packets after you've intercept them
  * The victims will periodically sent out ARP request and receives replies, so you need to poisened them regularly
* These can also be done on the **default gateway**

