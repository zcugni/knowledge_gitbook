# Nmap

## Generalities

* Initially created for port scanning but added a lot of functionalities with time
* It's THE cmd to use for those type of things
* I detailed the main options, but there's more
* My default command : `sudo nmap -v [-p-] [-Pn] -sC -sV -oN <result_file> <ip>`

## Define target

* A target can be :
  * IP addresses \(v4 & v6\)
  * hostname
    * If it resolve to more than one ip, only the first one is used. Use `--resolve-all` to scan all
  * CIDR-like format for network \(v4 & v6\)
  * Octet-range \(Only on IPv4\)
* `-iL <file>` Reads target from file
  * Must be separated by spaces, tabs or newlines
  * Can have comment starting with "\#"
* `-iL -` Reads target from standard input
* `--exclude host1[, host2, ..]`
* `--excludefile <file>` Same restriction as -iL

## Define ports

In both host discovery & port scanning you can specify the port you target :

* Individual port numbers separated by commas
* Range specified via `-` : 1-1023
* If the start or end values are omitted, nmap will use 1 & 65535
* You can also use name and  _: `-p ftp,http`_

## Misc options

By default, nmap does a host discovery and then a port scan on live host

* `-sn` No port scan \(so only host discovery\)
* `-Pn` No host discovery \(so only port scan\)
* Theoretically you can modify the packet's content with  `--data` & `--data-string` but they aren't mentionned/detailled in some version of the man
* `--resume <file>` Resume a scan from a normal/grepable output file
* `-6` Enable IPv6 scanning
  * If you use IP address, they obviously must be IPv6 ones
  * Both the source and the target must be configured for IPv6
* `-V` & `--version`
* `-h` & `--help`
* `--max-retries <nb>` For performance issue, change this

## Host discovery

* Identify which host are live within an ip range by sending different types of probes
* In the type section, i speak of open & close ports, but it's not to determine if they are. It's just that if we receive a response of any type, the host is live
* When we send probes, if the host is live we'll receive responses to requests our machine didn't actually made, so it'll responds by aborting the connection
* Dead host will timeout, so the whole process can take time
* On unix, normally only the root user can send & receive raw TCP packets, so the `connect` syscall is used instead for unprivileged user

### Default settings

* By default, as root, it'll send :
  * ICMP echo requests \(ping ?\)
  * TCP SYN packets to port 443
  * TCP ACK packets to port 80
  * ICMP timestamp requests \(only IPv4\)
  * ARP & Neighbor Discovery scans are done for local ethernet network
* By default, as an unprivileged user, it'll send :
  * SYN packets to port 80 & 443 using the `connect` system call

### Types of "pings"

* Basic syntax : `-PX[port_list]`
  * X being replaced by the packet type letter
  * Each type has a default port, so the list is optional
  * Example : `-PX22`, `PX22-25,80`
* If multiple probes are specified, they'll be send in parallel
* `-PS[port_list]` TCP SYN ping
  * Port 80 by default
  * Live if we receive an `RST` or `SYN/ACK` \(or a success/`ECONNREFUSED` for `connect)` 
* `-PA[port_list]` TCP ACK ping
  * Port 80 by default
  * Live if we receive an `RST` \(the target drops the connection because we're skipping the first step of the handshake\)
  * Used because small firewalls sometimes only drop SYN packets
  * Root only \(`connect` can't send ACK\)
* `-PU[port_list]` UDP ping
  * Port 40125 by default
  * Empty UDP packets are used \(some protocol-specific payloads might be useful, check this [page](https://nmap.org/book/nmap-payloads.html)\)
  * Live if we receive an `ICMP port unreachable` or other ICMP errors/TTL expires from closed ports
    * Open ports usually won't respond, which is why it uses an unlikely port
  * Useful against firewall that only filter TCP packets
* `-PY[port_list]` SCTP Init ping
  * Port 80 by default
  * Works kind of like the TCP SYN but instead use the SCTP four-way-handshake
    * It sends an `INIT Chunk` and receives either an `ABORT` or `INIT-ACK`
  * Only root
* `-PE`, `-PP` & `-PM` ICMP ping
  * `-PE` for the typical ICMP type 8 \(echo request, aka ping\), expecting a type \(echo reply\)
  * `-PP` for the ICMP type 13 \(timestamp request\), expecting an ICMP 14
  * `-PM` for the ICMP type 17 \(address mask request\), expecting an ICMP 18
  * ICMP are often blocked, especially echo request. The other two might be forgotten
* `-PO <protocol_list>` IP protocol ping
  * Sends IP packets with the specified protocol number set in the header
  * Comma-separated list
  * By default, it sends multiple packet for the ICMP \(protocol 1\), IGMP \(protocol 2\) & IP-in-IP \(protocol 4\) protocols
  * For ICMP, IGMP, TCP \(protocol 6\), UDP \(protocol 17\) & SCTP \(protocol 132\) the protocol headers are added, for other, not
  * Live if we receive the corresponding protocol response or an `ICMP Unreachable` message \(meaning that the protocol isn't supported\)
* `-PR` ARP ping / Neighbor Discovery
  * OS aren't designed to deal with lots of ARP request in a short period of time \(which would be needed by all the above options since the host probably hasn't populated it's whole ARP table\)
  * To remove this burden, on local network nmap will by default take care of that itself \(even if other types of scan are specified\)
  * To absolutely not do it, use `--disable-arp-ping` \(which will be useful on network where a router speculatively replies to all ARP request, making every target appears to be up\)
  * If it get a response, it doesn't care about IP request since it already knows the host is live
  * For IPv6, nmap will use the Neighbor Discovery protocol

### Other options

* `--traceroute` Trace path to host
  * Performed post-scan using the gained info to determine the most likely port and protocol
  * Works with all scan types except `-sT` \(connect\) and `-sI` \(idle\)
  * Traceroute works by sending packets with a low TTL to elicit `ICMP Time Exceeded` on intermediate host
    * Standard implementation starts with a TTL of 1 and increments it
    * Nmap starts with an high one and decrements it until it reaches zero, so it can smartly cache result to speed up the process
* `-n` No dns resolution
* `--dns-servers` Go read the man, it seems interesting but is too specific for me for now

## Port Scanning

### Etats

* Nmap use 6 state to describe port :
  * **Open** : Actively accepting connections
  * **Closed** : Accessible but no application is listening on it. It might be interesting to re-scan them later.
  * **Filtered** : Cannot determine whether the port is open because packet filtering is in place, they either :
    * Drops the packets \(which forces nmap to send multiple requests to check it's not a connection problem\)
    * Responds with ICMP error message such as a type 3 code 13 `Destination unreachable : Communication Administratively Prohibited`
  * **Unfiltered** : \(Only for ACK scan\) Accessible but nmap can't determine if it's open or not
  * **Open\|Filtered** : Can't determine if the port is open or filtered
    * It's the case for scans to which open port don't respond, such as UDP, IP protocol, FIN, NULL & Xmas scans
  * **Closed\|Filtered** : Can't determine if it's closed or filtered, only used by the IP ID idle scan

### Types of scan

* `-sS` TCP SYN Scan
  * Default, quick, stealthy \(except against good IDS\), clear differentiation of state
  * Referred as half-open scanning, since you never truly finish the connexion and open the port
  * Open if it receive a SYN or SYN/ACK
  * Closed if it receives an RST
  * Filtered if no response is received after multiple tries or an `ICMP Unreachable` error is received \(type 3 code 0, 1, 2, 3, 9, 10 & 13\).
* `-sT` TCP Connect Scan
  * For unprivileged user, uses the `connect` syscall
  * It's part of the Berkeley Sockets API
  * It establish a full connection, so it's not stealthy and slower
* `-sU` UDP Scans
  * Sends empty UDP packets, except for some known protocol's port, in which case specific headers are added
  * Closed if an `ICMP Unreachable` error \(type 3, code 3\) is received
  * Filtered if other `ICMP Unreachable` error \(type code, code 0, 1, 2, 9, 10 & 13\) are received
  * Open if the host respond with an UDP packet \(rare as the protocol doesn't need that\)
  * Open\|Filtered if no response is received after a few time \(version detection `-sV` might help determine that\)
  * Particularly slow because open & filtered port rarely responds, so nmap must send a lot of packets to check for connection issues and the rate at which ICMP error are send on closed port is often limited \(quite harshly even\)
    * Nmap detects and adjust to this rate to prevent flooding the host\)
  * Can be used with a TCP scan
* `-sY` SCTP INIT Scan
  * SCTP is an alternative to TCP & UDP, combining their characteristic and adding features like multi-homing & multi-streaming
  * This scan is equivalent to the TCP SYN Scan, it's fast, stealthy and differentiate between port state
* `-sZ` SCTP Cookie Echo Scan
  * More advanced SCTP scan
  * Close if we receive an `ABORT` to a packet containing a `COOKIE ECHO` chunk \(open port will simply drop the packets\)
  * Stealthier than an INIT scan, but good IDS can still detect them
  * Cannot differentiate between open & filtered ports
* `-sN`, `-sF` & `-sX` TCP NULL, TCP FIN & TCP Xmas scan
  * If the host follows the RFC to the letter \(which is not often the case\), any packet not containing a SYN, ACK or RST flag should be dropped on an open port or receive an RST response on a closed one
  * The NULL scan doesn't set any flag, the FIN one just the FIN flag and the Xmas one the FIN, PSH & URG flag
  * Can bypass some firewalls and can be stealthier than the syn scan \(even though IDS can be configured to detect them\)
  * However it's not reliable because a lot of devices \(especially Windows, Cisco, BSDI & IBM one\) will always responds with RST
  * It also cannot differentiate open & filtered port
* `-sA` ACK Scan
  * Won't determine if a port is open or not, only if it is filtered or not
  * Unfiltered if we receive an RST
  * Filtered if  there's no response or we receive an ICMP unreachable error \(type 3, code 0, 1, 2, 3, 9, 10 & 13\)
* `-sW` TCP Windows Scan
  * Like the ACK Scan, but use a windows specificity to determine if the port is open or not
  * However only a minority of implementations follows that so you can't really trust it
* `-sM` Maimon Scan
  * Systems should always respond with an RST if only the FIN/ACK flags are set
  * However some unix system actually drop it instead when they're open \(same logic as `-sN`, `-sF` & `-sX`\)
* `-sI zombie host[:probeport]` Idle Scan
  * Truly blind TCP scan \(meaning that no packets are sent from your IP address\)
  * "A unique side-channel attack exploits predictable IP fragmentation ID sequence generation on the Zombie host to glean info on the open port of the target"
  * The zombie machine must be up and respect certain criteria
  * More details on [https://nmap.org/book/idlescan.html](https://nmap.org/book/idlescan.html)
  * Maps the IP-trust relationship between machines \(aka, you might get different result from different zombie machine as the target respond to them\)
* `-sO` IP Protocol Scan
  * Determine which IP protocols \(TCP, ICMP IGMP, etc\) are supported by the machine. It's not exactly a port scan, but it still uses port to do it, so it can work for that
  * It sends IP packet headers and iterates through the 8-bit IP protocol field
  * The headers are usually empty, except for the TCP, UDP, ICMP, SCTP & IGMP protocols
  * Open if any protocol respond
  * Closed if ICMP protocol unreachable error \(type 3, code 2\) is received
  * Filtered if other ICMP unreachable errors are received \(type 3, code 1, 3, 9, 10 & 13\)
  * open\|filtered if no response is received after multiple tries
* `-b` FTP Bounce Scan
  * Mostly fixed now so probably won't work
  * Abuse a feature of FTP server that let's you use proxy FTP connection, aka, you connect to a server but send files to another
  * Because of that, you can use an FTP server to port scan other host \(which is useful as FTP servers usually have more access than random host\)
  * Check the man for more detail
* `--scanflags` Specify custom TCP flag \(read the man for more detail\)

### Target port

* By default, nmap scans the most common 1000 ports for each protocol
* `-p` Lets you specified which port to scan
  * If follows the same logic explained earlier, you just need to specify the protocol with `T:` for TCP and `U:` for UDP
  * Example : `-p U:53,111,137,T:21-25,80,139,8080`
  * The last protocol specified is used
  * If no protocols are specified, is uses the 2 of them
  * For IP protocol scan, this options specifies the protocols numbers to scan \(0-255\)
* `-F` Fast scan \(scan only the 100th most common port\)
* `--top-ports n` Scan the _n_ most common port

## Service & Version detection

* When you know which ports are open, you can now scan them to discover which services they run, their version, etc
* `-sV` Enables version detection
* The probes are classified by rarity, from 1 to 9
  * This is called the version-intensity, and by default nmap goes up to a rarity of 7

## OS Detection

* nmap can detect the OS running via TCP/IP fingerprinting
  * It sends a series of TCP & UDP packets and examine pratically each bit of the response, comparing it to it's db of signature. Read the man for more detail on how it's done.
* `-O` Enable OS detection
* `--osscan-limit` Only perform OS detection if 1 open & 1 closed TCP port are found \(greatly increase the chance of success\)

## NSE \(Nmap scipting engine\)

* Extends nmap features via scripts written in the Lua language
* Scripts are categorized :
  * **Auth** : Deals with authentication credentials or bypassing them
  * **Broadcast** : Discovery of hosts not listed on the command line by broadcasting the local network
  * **Brute** : Use brute force attacks to guess creds
  * **Default** : Use by default when script scan is enable. They must respect these criteria : 
    * Fast
    * Useful
    * Not too verbose
    * Reliable
    * Not too intrusive \(but some still aren't classified as safe\)
    * Not privacy-invasive
  * **Discovery** : Discover more about the network by querying public registries, SNMP-enabled devices, directory services, etc.
  * **Dos** : The script may cause DDOS, voluntarily or not
  * **Exploit** : Actively exploit vulnerabilities
  * **External** : May send data to a third-party db \(for example whois\)
  * **Fuzzer** : Scripts designed to send software unexpected or randomized fields in each packet to detect bugs & vuln
    * Slow, bandwidth-intensive and may crash the target
  * **Intrusive** : May crash the target, use it's ressource or be considered as malicious by the system admin
  * **Malware** : Test whether the target platform is infected by malware or backdoors
  * **Safe** : Opposite of intrusive
  * **Version** : Extension of the version detection feature
  * **Vuln** : Checks for specific known vulnerabilities and report results if found
* Scripts are not run in a sandbox and could damage your system. Don't run third parties scripts unless you trust them.
* Scripts are inside `/usr/share/nmap/scripts` \(at least in kali\)
* `-sC` Performs a script scan using the default set of scripts
* `--script ...` Launch a script
  * `--script <filename>`
  * `--script=category`
  * `--script "http-*"`
  * `--script "not intrusive"`
  * `--script "default and safe"`
* `--script-args n1=v1,n2={n3=v3},n4={v4,v5}`
* To use an nmap script through a proxy, we need to add a proxy listener in burp and have nmap target it. For example, to test a script against 10.10.10.56 :
  * Create a proxy listener that redirect 127.0.0.1:8081 to 10.10.10.56:80
  * Launch nmap against 127.0.0.1, port 8081

## Timing and performance

* There's a lot of options to set min & max timing, retries, delays, etc
* Default settings seems good, so i won't detail everything, but 2 options are interesting :
  * `--defeat-rst-ratelimit` Some host implements rate limits on RST message, which can slow nmap a lot
    * For some scan, such as SYN, it migth be worth to just not care about those
    * We won't be able to differentiate between closed and filtered port, but we'll still discover open ones
  * `-T paranoid|sneaky|polite|normal|aggressive|insane` Set a timing template
    * We can specify the template by it's name or it's number
    * paranoid \(0\) & sneaky \(1\) are for IDS evasion. Paranoid doesn't even scan port in parallel
    * polite \(2\) can be quite slow
    * normal \(3\) is the default and shouldn't cause any problem on decent network
    * aggressive \(4\) is for reasonably modern & reliable networks, it also has some fine-grained tuning not possible with options
    * insane \(5\) assume that you're on an extraordinarily fast network or willing to sacrifice accuracy for speed

## Firewall / IDS / IPS Evasion and Spoofing

* Nmap provides options that can help evade protection
* _Intrusion Detection System_ \(IDS\) have rules to detect nmap scans since they're a good indicator of an incoming attack
* _Intrusion Prevention System_ \(IPS\) actively block traffic deemed malicious. However they give a lot of false positives
* `-f` & `--mtu <size>` Fragment packet in multiple parts
  * Fragment the TCP header in multiple packets to make it harder to detect
  * Some programs have trouble dealing with these, and some setup will defragment them so it won't do anything
  * Use `-f` or `-ff`to divide them in 8/16-bytes packets \(+ the ip header\)
  * Specify an offset with `--mtu <size>`, it must be a multiple of 8
  * Only supported by TCP & UDP scans \(except the connect one\) and OS detection
* `-D decoy1[,decoy2,ME,...]` Cloak a scan with decoys
  * Makes it appear to the target that the host you specify as decoy are also scanning the network
  * This can be defeated by router path tracing, response-dropping, etc, but is fairly effective
  * You can specify ME in the decoy list. If you put it in the sixth position or later, some detector might not even report your address at all
  * If you don't specify ME, nmap will place it in a random position
  * The decoy host must obviously be up
  * Decoys do not work with version detection and the connect scan
* `-S <ip>` Spoof source address
  * Forge the source address to make it looks like somebody else is scanning the host
  * However responses won't be addressed to you, so you won't get a report
* `--source-port <port>` & `-g <port>` Spoof source port number
  * A common misconfiguration is to trust traffic based on the source port number \(particularly DNS & FTP one because it's a quick but insecure workaround to some problems\)
  * Most scanning operations using raw sockets, such as SYN & UDP scans, can use this
  * DNS request, TCP connect scan, version detection, script scanning & os detection can't use it
* `--data-length <nb>` Append random data to packets, OS detection isn't concerned by this
* `--ip-options ..` Set specific IP options, read the man for more details
* `--ttl <nb>` Set the IPv4 ttl value
* `--randomize-hosts` Randomize target host order
  * Instead of scanning them in incrementing order, does it randomly, which may be less obvious
* `--spoof-mac ..` Spoof the mac address, read the man for more details
* `--badsum` Send packets with bogus TCP/UDP checksums
  * Nearly all host IP stacks would drop them
  * So if a response is received, it's probably coming from a firewall or IDS that didn't bother to check

## Output & Verbosity

* `-oN <file>` Write normal output \(human readable\) to the specified file. This corresponds to the interactive output written on stdout \(with a few differences\)
* `-oX <file>` Write XML output to the specified file
* `-oG <file>` Write grepable output to the specifed file, actually deprecated because the XML one is way more powerful
* `-oA filename` Write normal, xml & grepable output to 3 files having this filename \(so don't specify extension\)
* `-v` Increase the verbosity level
  * Concern mostly the interactive & normal output 
  * use `-vv` or `-v3` to increase it even more
* `-d` Increase debug level
  * Use `-dd` and up to `-d9` to increase it even more
  * The higher it goes, the less readable it becomes. It's aimed at dev
* `--reason` Expain why a state was chosen for an host/port
  * Always added to the xml output
* `--stats-every <time>` Print periodic timing stats
  * Example : `--stats-every 10s`
  * Printed to the interactive and xml output
* `--packet-trace` Print all packets sent and received
  * Use `--version-trace` to only trace the version detection ones
  * Use `--script-trace` to only trace the script one
* `--open` Only show open \(or possibly open\) ports
* `--log-errors` Adds the errors to the normal output \(usually they're only shown on the interactive one\)
* `--append-output` Appends instead of replace file \(don't work well for xml files\)

## Interactive mode

* Some commands are usable while nmap is running :
  * v / V : Increase/Decrease verbosity level
  * d / D : Increase/Decrease debug level
  * p / P : Turn on / off packet tracing
  * ? : Print help

## Source

* nmap's man page

