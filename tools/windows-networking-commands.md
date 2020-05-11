---
description: >-
  Disclaimer : Ca va faire un peu doublon avec la version linux, les détails
  sont plus là-bas
---

# Windows networking commands

## Packet sniffing

* Wireshark
* winDump \(équivalent de [tcpdump](https://zcugni.gitbook.io/notes/tools/linux-bash-command/tcpdump)\)

## Creating packet

Windows doesn't include `netcat`, but it has been ported

## Following packet

* traceroute is `tracert` on windows \(disable dns lookups with `-d`\)
* You can automatically run traceroute with WinMTR
* Use traceroute servers

## Ping

* Sonar-like request basically saying "Hello ? Are you there ?"
* Does not give any information about the host's services
* Needs one argument, the hostname or ip address of the host
* Successful pings tells you the speed of the response

## ARP

* `arp -a` Montre la table dans windows & linux.
* `arp -a <ip>` Montre une entrée spécifique de la table

## ND

* Show the ND cache by using `netsh`, which is a powerful tool
* For this purpose use `netsh interface ipv6 show neighbors`

## Netsat

* J'ignore s'il est aussi déprécié sur Windows
* Shows which ports are open, current connection to other machines and what's listening on a port
* Usually specify :
  * The protocol
  * The local address & port
  * The foreing address & port
  * The state \(for tcp\)
* Local address 0.0.0.0 \(or \[::\] for IPv6\) means "all adresses on this machine"
* Foreign address 0.0.0.0 \(or \[::\] for IPv6\) means "any addresses", that's only possible when the software is listening, so waiting for an incoming connection.

### Command

* `n` Don't reverse DNS lookup IP addresses for hostname and check protocol names in the services file \(gain of time\)
* `-a` Shows open port and live connection
* `-p <protocol>` Specify which protocol you want to see \("tcp" will show IPv4 connections, to see IPv6 ones, use `tcpv6`\)
* `-b` \(As admin\) Shows the program or process name using the connection/socket

## nslookup \(DNS\)

* Good for basic queries
* For more complex one use BIND's `dig`, `drill` or `ldns`
* `nslookup <hostname>` Gives address back \(specify if the answer is authoritative and list the recursive name server it asked\)
* `nslookup <ip>` Gives hostname back
* For both case you can add a specific DNS IP as a third parameter to query it

## Other

* `ipconfig`

