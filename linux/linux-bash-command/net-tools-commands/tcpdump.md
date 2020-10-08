# tcpdump

## Generalities

* Ported on every OS, some change the default behavior quite a lot, install the unmodified version if it's problematic
* It's language, the **Berkeley Packet Filter** \(BPF\) has become a standard and almost all other packet-sniffer tools understand it
* Console-based
* Modern OS run tcpdump in a sandbox so there shouldn't be security problems
* Use with `sudo` 
* `tcpdump -D` to see which interface you can attach to
* `tcpdump -i <interface>` Attach to an interface, use name or number
* `tcpdump -n` Hide DNS request traffic
* To check if you received a ping, use `tcpdump -n -i <interface> icmp`
  * Use the interface linked to the ip you pinged

## Example of an UDP packet

> 14:59:50.351940 IP example.com.62368 &gt; google-public-dns-a.google.com.53: 20011+PTR? 255.255.255.255.in-addr.arpa. \(46\)

* In order, the fields are :
  * Timestamp with seconds
  * Protocols
    * IP for IPv4
    * IP6 for IPv6
    * 802.1 for Ethernet
    * etc
  * source\_hostname.source\_port \(or source ip\)
  * The arrow indicates that the packet is moving
  * destination\_hostname.destination\_port
  * Content \(here DNS request number 20011 asking for a PTR record associated with the following address. So a reverse DNS request\) 
  * Length in bytes

## Example of a TCP packet

> 16:19:24:326971 IP 203.0.113.50.39200 &gt; 108.61.84.26.80: Flag \[S\], seq 16603792222, win 65535, options \[mss 1460,nop,wscale 6, sackOK,TS val 2474478 ecr 0\], length 0

* In order, the fields are :
  * Timestamp with seconds
  * Protocols :
    * IP for IPv4
    * IP6 for IPv6
    * 802.1 for Ethernet
    * etc
  * source\_ip.source\_port \(or source hostname\)
  * The arrow indicates that the packet is moving
  * destination\_hostname.destination\_port
  * Flags \(their can me multiples\)

| Flag | Meaning |
| :--- | :--- |
| `S` | SYN packet |
| `.` | ACK packet |
| `R` | Refused or reset |
| `F` | FIN packet |
| `U` | Urgent |
| `W` & `E` | For congestion control \(?\) |
| `P` | Push \(?\) |

* The rest are packet sequence numbers, window size and options but he didn't explain what those are.

## Example of an ARP packet

Question : 

> 11:35:48.468357 ARP, Request who-has 203.113.205 tell 203.113.206, length 46

Response : 

> 11:35:48.468357 ARP, Reply 203.0.113.205 is-at 00:0c:29:4f:7d:91 \(oui Unknown\), length 28

## Filter traffic

* Use tcpdump's filter language to filter which traffic you _capture_ instead of grepping the result afterwards
  * On busy servers capturing everything can be resource intensive \(especially if you ssh'ed and the result is sent to you across the network\)
* The filter-expressions comes last in the command

| Expression | Meaning |
| :--- | :--- |
| `arp` | Only arp traffic |
| `icmp` | Only icmp traffic \(ping\) |
| `ether host <mac>` | Only traffic with the specified mac address |
| `ip` | Only ip traffic |
| `ip host <hostname_or_ip>` | Only ip traffic from the specified machine |
| `ip net 192.0.2.0/24` | Capture traffic with this entire network |
| `udp` & `tcp` | Filter by those protocols |
| `port <nb>` | Only traffic through this port |

* `and` : `arp and ether host <mac>`
* `or` : `ip host <ip> or <ip>` \(No need to repeat "host"\)
* You can use \(\) but they need to be escaped : `\(` on unix and \`\( on windows
  * Or simply wrap the entire filter between `'`
* `not` can be used either in front of the whole filter `not ip host mail.example.com` or in front of some part `ip net 192.0.2.0/24 not ip host 192.0.2.88`
* For port, you can add the protocol without "and" : `tcp port 80`

## Capture files

* Capture files are binary dumps of the packet \(possibly with sensible non encrypted data, so be cautious before sending them\)
* Capture with `-w <file>` \(add `-v` for feedback\)
* Extension :  `.pcap`
* Capture runs until you hit CTRL-c and the files can grew quite big
* `-r capture.pcap` Read capture file
  * You can use param end filters against them : `tcpdump -nr capture.pcap port 80`

## Source

* _Networking for Systems Administrators_ by Michael W. Lucas

