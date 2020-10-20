# Net tools / commands

## Replacement of net-tools

* `netstat` & `ifconfig` are depreciated, use this instead :
  * `netstat` : `ss`
  * `nestat -r` : `ip route` \(iproute2 ? check that\)
  * `netstat -i` : `ip -s link`
  * `netstat -g` : `ip maddr`
  * `ifconfig` : `ip a`

## Nmap

Go see [Nmap](https://zcugni.gitbook.io/notes/tools/nmap)

## Netcat

* Generate traffic & simulate sockets to listen
* It's an old tool that has been forked, rewritten, extended and improved many times
  * The openBSD version is the most popular \(and it supports IPv6\)
* Defaults using TCP
  * For UDP use `-u` \(the protocol doesn't imply an answer, but netcat will listen for one and display it\)
* `netcat <hostname> <port>` If the client establish a TCP session, you'll get a blank line
  * The three-way handshake is done, but no request has been made. You can then write your own :`GET / HTTP/1.1`
* The basic listen command is `nc -lnvp <port>`
  * `-l` Listen mode
  * `-n` Numeric-only IP address, no DNS
  * `-v` Verbose
  * `-p <port>` Specify local port
* `-c <command>` Execute command after connect \(use with caution\)

## Traceroute

* Allows you to follow the packets as they travel between hosts
* `traceroute <host>`
* It sends 3 packets to each hop as a test, returns :
  * The time it took them to go to the host & return
  * The hostname \(if available\) and ip of each host along the way
* Disable DNS lookups with `-n`

### Errors

* A lost packet is represented by a `*`, a few is normal
* A line of `***` means that the previous host forwarded a packet, but no response came back so traceroute can't print an hostname or ip
  * It can mean that the host filters UDP or ICMP
  * Sometimes this will be the case only for one host so let traceroute run
  * However if there's multiple lines like that you probably won't get more info
* Errors letters :
  * `!H` The next host is unreachable
  * `!N` The entire destination network is unreachable
  * `!A`, `!X` & `!Z` Further communication is refused
* Time Spikes : Responding to a traceroute request takes more time that forwarding a packet, and it's not the prioriy of the router when it's busy
  * That's why an hop might have a longer time response that the next one.
* Multiple hostname might respond at one hop because of load balancers making the whole trace more confusing
* Traceroute use **asymmetric routing**, it follows the path _to_ the target, but not the return one, which might differ
  * Don't bother too much with high roundtrip times
  * You can use traceroute servers \(available on the internet\), to run a traceroute through them and verify if the problem comes from the hosts you're hopping through or from your destination
* To see if the dropped packets are a separate occurrence or an ongoing issue, just run multiple traceroute
  * `mtr` does that automatically.

## DNS

* Specifies host in : `/etc/hosts`
* Specifies which DNS server to use in : `/etc/resolv.conf`
* `nslookup` Has been deprecated and then brought back so depending on the version it's not the best, just **use other tools** :
  * `host` is the simple one 
    * `host <hostname> [dns_server]` Gives ip back
    * `host <ip> [dns_server]` Gives hostname back
    * `-v` Gives more details \(response code, authority, records, etc\)
  * `dig` is the more complete one
    * `dig <hostname> [@dns_server]`
    * If no dns server is specified, dig will look into `/etc/resolv.conf` and if that doesn't work send the query to the local host
    *  `dig -x <ip>` Reverse look-up
    * `dig axfr [zone] [@dns_server]`
      * Ask for a zone transfer
      * By default it's the root zone \(`.`\)

## Ports stat

Run as root

| Command | Definition |
| :--- | :--- |
| `ss -t` | See tcp port |
| `ss -u` | See udp port |
| `ss -p` | See program attached to the port |
| `ss -l` | See listening port |
| `ss -n` | Numeric name |

## Ping

* Basic tool to check if you can communicate with a machine
* `-c <nb>` Number of pings to send
  * Be cautious when you force a server to ping continuously, it might hold the it \(it won't do anything else\)
* Use [tcpdump](https://zcugni.gitbook.io/notes/tools/linux-bash-command/net-tools-commands/tcpdump) to check for pings arriving to your machine

### Sweep ping

* Ping all the addresses in a range, used to determine which host are live
* `fping -a -g <ip>/<netmask>`
  * `-a` Show only live host
  * `-g` Ping sweep instead of a simple ping 
  * Redirects error to `2>/dev/null` because you'll always get some

## ARP

* `arp -a` Show the table with :
  * The hostname
    * Represented by a `?` if it's unknown
    * Use `-n` to not search it
  * The expiration time
  * Often, the mac address of the machine is hardcoded and noted as permanent
* `apr <ip>` Show a specific entry

## ND command

They're different on each distro

## httprint

* Allows you to fingerprint a web server
* `httprint -P0 -h <ip> -s <signature_file>`
  * `P0` Avoid pinging \(web server generally don't respond\)

