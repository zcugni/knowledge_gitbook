# DNS

## Generalities

* The _Domain Name System_ \(DNS\) is the world's must successful distributed database 
* Maps human-friendly hostnames to ip addresses
  * Hostname to addresses is called forward and it can return multiple addresses
  * Addresses to hostname is called reverse and should only return 1 entry
* Use UDP except when the request are too big \(for example for zone transfer\)
* Runs on port 53
* A DNS server \(also called a **nameserver**\) searches for and collects the mappings
* Domain names are divided by **zones** \(each dot separate them\)
  * All top level domains \(.com, .net, etc\) are contained in the _root zone_
* DNS is hierarchical and zone inside other ones are called child zones

![](../.gitbook/assets/dns.png)

## Authoritative & Recursive DNS

* DNS servers comes in 2 sorts and should be on different machines

### Authoritative

* Contains information for specific domains
* A complete collection of data for a zone is called a _zone file_
* Authoritative servers are duplicated 
  * To copy all the entries from a zone to another server, we use _zone transfers_
    * Done via AXFR with `dig`
    * By default, anybody can ask for a zone transfer

### Recursive

* Provides DNS lookups for clients
* Cache collected answers until a per-DNS-record timer expires
* When configuring an host, you need to give it the ip address of it's recursive DNS server
  * If the host use DHCP, it can do that automatically

### Request process

* When you request a page, your computer ask a recursive nameserver for the A record containing the corresponding IP address
* If it doesn't have the response in it's cache, the nameserver transfer the request to the corresponding root name server
* It gives back the address of the corresponding child zone and so on until you arrive at the authoritative server of the address
* So for www.example.com, you first ask .com, which gives back the address of www.example.com, which gives back example.com which returns the answer.

## Record types

* DNS servers grew organically and now handle many things
* Those are some of the records type :

<table>
  <thead>
    <tr>
      <th style="text-align:left">Record name</th>
      <th style="text-align:left">Info</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">A</td>
      <td style="text-align:left">Maps a hostname to an IPv4 address</td>
    </tr>
    <tr>
      <td style="text-align:left">AAAA</td>
      <td style="text-align:left">Maps a hostname to an</td>
    </tr>
    <tr>
      <td style="text-align:left">CNAME (canonical name)</td>
      <td style="text-align:left">Maps an alias to a hostname</td>
    </tr>
    <tr>
      <td style="text-align:left">PTR (pointer)</td>
      <td style="text-align:left">Maps an IPv4 address to a hostname. The reverse of an A record.</td>
    </tr>
    <tr>
      <td style="text-align:left">NS</td>
      <td style="text-align:left">Specifies a name server for a domain</td>
    </tr>
    <tr>
      <td style="text-align:left">SOA (Start of Authority)</td>
      <td style="text-align:left">Timing &amp; responsibility info for the zone you&apos;re searching (1)</td>
    </tr>
    <tr>
      <td style="text-align:left">SRV</td>
      <td style="text-align:left">Used to locate servers that host specific services</td>
    </tr>
    <tr>
      <td style="text-align:left">MX (mail exchanger)</td>
      <td style="text-align:left">Used to locate a mail server</td>
    </tr>
    <tr>
      <td style="text-align:left">TXT</td>
      <td style="text-align:left">
        <p></p>
        <p>Can contain various data. Often used for verifying domains and security
          reasons.</p>
      </td>
    </tr>
  </tbody>
</table>

\(1\) Aka "How long should a recursive nameserver cache entries" or "Who do i contact for problems with this domain ?"

## Response code

* _`NOERROR`_ Success \(doesn't mean that the answer is correct, simply that we receive one\)
  * Some tools print `NODATA` when no valid answer is found
* `NXDOMAIN` The protocol worked, but there's no authoritative answer for that name
* `SERVFAIL` Something went wrong and you can't get an answer

## Other location of mapping

* You can add local mappings into the host file
  * `/etc/hosts/`
  * `C:\\Windows\System32\drivers\etc\hosts`
  * Format : `ipaddress hostname aliases`
  * Lookup in an host file is faster than querying a DNS, but the difference shouldn't be important
* Name resolution can even be done with LDAP or other local db
* The system checks it's source in a specific order and takes the first answer it finds
  * Windows always checks the host file first
  * Unix let you config this in `/etc/nsswitch.conf` or `/etc/host`

## Dynamic DNS \(DDNS\)

* A method for clients to register and dynamically update their resource records with a DNS server
* This allows clients that use DHCP to auto update their DNS record when their IP address changes

## Sources

* _Networking for Systems Administrators_ by Michael W. Lucas
* [Techninjistics](https://www.youtube.com/user/techninjistics)
* [activedirectorypro.com](https://activedirectorypro.com/glossary/#dns)

