# DNS

## Généralités

* DNS \(_Domain Name System_\) is the world's must successful distributed databse.
* Map human-friendly hostnames to ip addresses
* Forward DNS maps hostnames to IP addresses, reverse DNS does the opposite
* Forward DNS can return multiple addresses, reverse should only return 1
* Runs on TCP & UDP on port 53
* A DNS server \(also called a _nameserver_\) searches for and collects address to hostname mappings
* Domain names are divided by zones \(each dot separate them\). All top level domains \(.com, .net, etc\) are contained in the _root zone_.
* DNS is very hierarchical and zone inside other ones are called child zones.

![](../.gitbook/assets/dns.png)

## Authoritative & Recursive DNS

* DNS servers come in 2 varieties : authoritative & recursive
* The first one contains information for specific domain. \(A complete collection of data for a zone is called a _zone file_\)
* The second one provide DNS lookups for clients.
* They should be on different machines.
* Recursive DNS servers cache collected answers until a per-DNS-record timer expires
* When configuring an host, you need to give it the ip address of it's \(recursive\) DNS server \(if the host use DHCP, it can do that automatically\).

### Request process

1. When you ask for a page, your computer ask a recursive nameserver for the A record containing the corresponding IP address. 
2. If it doesn't have the response in it's cache, the nameserver transfer the request to the corresponding root name server. 
3. It gives back the address of the corresponding child zone and so on until you arrive at the authoritative server of the address. 
4. So for www.example.com, you first ask .com, which gives back the address of www.example.com, which gives back example.com which returns the answer.

## Record types

DNS servers grew organically and now handle way too many things, each of these is contained within a record. Here's a few :

| Record name | Info |
| :--- | :--- |
| A | IPv4 address |
| AAAA | IPv6 address |
| PTR \(pointer\) | Hostname |
| SOA \(Start of Authority\) | Timing & responsibility info for the zone you're searching \(1\) |
| CNAME \(canonical name\) | DNS alias, redirecting one name to another |
| MX \(mail exchanger\) | Identifies one of the mail servers for a zone |

\(1\) Aka "How long should a recursive nameserver cache entries" or "Who do i contact for problems with this domain ?"

## Response code

* _`NOERROR`_ Success \(doesn't mean that the answer is correct, simply that we receive one\)
  * Some tools print `NODATA` when no valid answer is found
* `NXDOMAIN` The protocol worked, but there's no authoritative answer for that name
* `SERVFAIL` Something went wrong and you can't get an answer

## Zone Transfer

*  Les serveurs DNS \(autoritaire ?\) pour des zones données sont généralement dupliqués pour éviter les problèmes
* Les "Zones Transfer" permettent de copier toutes les entrées \(et donc les host\) d'une _zone_ sur un autre serveur
  * Donc même si ça ne fonctionne pas pour la root, ça vaut la peine de tester pour d'autres zones
* Généralement fait via AXFR \(avec `dig`\)
* Par défaut, n'importe qui peut demander une Zone Transfer. Il faut modifier le fichier de config pour restreindre cette fonctionnalité à certaines ip
* Généralement, les requêtes DNS se font via UDP, elles passent par TCP quand elles sont trop grosses, ce qui est le cas pour un Zone Transfer, ça peut donc être un bon indicateur que cela est faisable

## Other location of mapping

* You can add local mappings into the host file
  * `/etc/hosts/`
  * `C:\\Windows\System32\drivers\etc\hosts`
  * Format : ipaddress hostname aliases
  * Lookup in an host file is faster than querying a DNS, but the difference shouldn't be important
* Name resolution can can even be done with LDAP or other local db
* The system checks it's source in a specific order and takes the first answer it finds.
* Windows always checks the host file first
* Unix let you config this in `/etc/nsswitch.conf` or `/etc/host`

