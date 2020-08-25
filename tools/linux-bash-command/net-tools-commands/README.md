# Net tools / commands

### Remplacement

Les net-tools \(`netstat` & `ifconfig`\) ont été dépréciés car ils n'étaient plus maintenu. Voici l'équivalent de certaines commandes :

* `netstat` : `ss`
* `nestat -r` : `ip route` \(iproute2 ? check that\)
* `netstat -i` : `ip -s link`
* `netstat -g` : `ip maddr`
* `ifconfig` : `ip a`

### Ports stat

* Run as root

| Commande | Définition |
| :--- | :--- |
| `ss -t` | See tcp port |
| `ss -u` | See udp port |
| `ss -p` | See program attach to the port |
| `ss -l` | See listening port |
| `ss -n` | Numeric name |

### Ping

* Sonar-like request basically saying "Hello ? Are you there ?"
* Does not give any information about the host's services
* Needs one argument, the hostname or ip address of the host
* Successful pings tells you the speed of the response

#### Sweep ping

* `fping -a -g 10.54.12.0/24`
  * `-a` To show only live host
  * `-g` To do a ping sweep instead of a simple ping 
  * Redirects error to `2>/dev/null` because you'll always get some

### ARP

* `arp -a` montre la table contenant ces infos :
  * Le hostname \(représenté par un ? s'il est inconnu\). Il est trouvé via le _System Name Service_, ce qui peut être inutilement long. Utiliser `-n` pour ne pas l'obtenir.
  * Le temps d'expiration
  * Souvent, la propre mac adresse de l'appareil est harcodée et notée comme permanente
* `apr ip` Montre une entrée spécifique de la table

### ND command

They're different on each distro

### Packet sniffing

* [tcpdump](https://zcugni.gitbook.io/notes/tools/linux-bash-command/tcpdump)
* Wireshark
  * Traffic analysis tool
  * Graphic interface
  * Always install Wireshark in a disposable virtual machine for security reasons

### Netcat

* Lets you **generate arbitrary TCP/IP traffic** and **simulate sockets** to listen to traffic
* It's an old tool that has been forked, rewritten, extended and improved many times.
  * The openBSD version is the most popular \(and it support IPv6\).
* **Defaults using TCP**, for UPD use `-u` \(the protocol doesn't imply an answer, but netcat will listen for one and display it\)
* `netcat <hostname> <port>` If the client establish a TCP session, you'll get a blank line.
  * The three-way handshake is done, but no request has been made. You can then write your own :`GET / HTTP/1.1`
* The basic listen command is `nc -lnvp <port>`
  * `-l` Listen mode
  * `-n` Numeric-only IP address, no DNS
  * `-v` Verbose
  * `-p <port>` Specify local port
* `-c <command>` Execute command after connect \(use with caution\)

### Traceroute

* Lets you follow the packets as they travel between hosts
* `traceroute destination_host`
* It sends 3 packets to each hop as test. You get the time it took them de faire un aller-retour jusqu'à cet host
* You also get the hostname \(if available\) and ip of each host along the way
* A lost packet is represented by a \*. A few one is normal
* Disable DNS lookups with `-n`

#### Errors

* A line of `***` means that the previous host forwarded a packet, but no response came back so traceroute can't print an hostname or ip.
  * It can mean that the host filters UDP or ICMP.
  * Sometimes this will be the case only for one host so let traceroute run.
  * However if there's multiple lines like that you probably won't get more info.
* Time Spikes : Responding to a traceroute request takes more time that forwarding a packet, and it's not the prioriy of the router when it's busy. That's why an hop might have a longer time response that the next one.
* Multiple hostname might respond at one hop because of load balancers making the whole trace more confusing
* `!H` Means the next host is unreachable
* `!N` Means that the entire destination network is unreachble
* `!A`, `!X` & `!Z` Means that further communication is refused
* traceroute follows the path _to_ the target, but not the return one, which might differ \(aka, asymmetric routing\), so don't bother too much with high roundtrip times.
* Traceroute servers \(find them on internet\) lets you run traceroute from them, so that you can see if the problem lies within the host you're hoping through or from the return route it's using with you.
* To see if the dropped packets are a separate occurrence or an ongoing issue, just run multiple traceroute. `mtr` does that automatically.

### DNS

* `nslookup` Has been deprecated and then brought back so depending on the version it's not the best, just use other tools
* `host` is the simple one 
  * `host <hostname> [dns_server]` Gives ip back \(you can specify a dns server to use\)
  * `host <ip> [dns_server]` Gives hostname back \(you can specify a dns server to use\)
  * `-v` Gives more details \(response code, authority, records, etc\)
* `dig` is the more complete one
  * `dig <hostname> [@dns_server]`
  * If no dns server is specified, dig will look into /etc/resolv.conf and if that doesn't work send the query to the local host
  *  `dig -x <ip>` Reverse look-up
  * `dig axfr [zone] [@dns_server]`
    * Ask for a zone transfer
    * If the zone isn't specified it will use `.` aka root.

### nmap

Go see [Nmap](https://zcugni.gitbook.io/notes/tools/nmap)

### httprint

* Let's you fingerprint a web server
* `httprint -P0 -h <target ip> -s <signature file>`
* `P0` to avoid pinging \(web server generally don't respond\)

