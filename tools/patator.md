---
description: 'Disclaimer : Je ne sais pas encore très bien l''utiliser'
---

# Patator

## Généralités

* [github](https://github.com/lanjelot/patator)
* Different module for brute-force \(list-based\) attack. Créer pour centraliser Hydra, les scripts nmap, les modules metasploit, etc.

Actuellement permet de :

* ftp\_login      : Brute-force FTP
* ssh\_login      : Brute-force SSH
* telnet\_login   : Brute-force Telnet
* smtp\_login     : Brute-force SMTP
* smtp\_vrfy      : Enumerate valid users using the SMTP VRFY command
* smtp\_rcpt      : Enumerate valid users using the SMTP RCPT TO command
* finger\_lookup  : Enumerate valid users using Finger
* http\_fuzz      : Brute-force HTTP/HTTPS
* rdp\_gateway    : Brute-force RDP Gateway
* ajp\_fuzz       : Brute-force AJP
* pop\_login      : Brute-force POP
* pop\_passd      : Brute-force poppassd \(not POP3\)
* imap\_login     : Brute-force IMAP
* ldap\_login     : Brute-force LDAP
* smb\_login      : Brute-force SMB
* smb\_lookupsid  : Brute-force SMB SID-lookup
* rlogin\_login   : Brute-force rlogin
* vmauthd\_login  : Brute-force VMware Authentication Daemon
* mssql\_login    : Brute-force MSSQL
* oracle\_login   : Brute-force Oracle
* mysql\_login    : Brute-force MySQL
* mysql\_query    : Brute-force MySQL queries
* rdp\_login      : Brute-force RDP \(NLA\)
* pgsql\_login    : Brute-force PostgreSQL
* vnc\_login      : Brute-force VNC
* dns\_forward    : Brute-force DNS
* dns\_reverse    : Brute-force DNS \(reverse lookup subnets\)
* ike\_enum       : Enumerate IKE transforms
* snmp\_login     : Brute-force SNMPv1/2 and SNMPv3
* unzip\_pass     : Brute-force the password of encrypted ZIP files
* keystore\_pass  : Brute-force the password of Java keystore files
* sqlcipher\_pass : Brute-force the password of SQLCipher-encrypted databases
* umbraco\_crack  : Crack Umbraco HMAC-SHA1 password hashes

## Utilisation

* `python3 patator.py module_name base_option=... -other -options`
* Each module has some base option like "host", "port", etc
* These keywords lets you specify what to fuzz :
  * `FILE` Iterate over a file
  * `COMBO` Iterate over a combo file
  * `NET` Iterate over every hosts of a network subnet
  * `RANGE` Iterate over hexadecimal, decimal or alphabetical ranges
  * `PROG` Iterate over the output of an external program
  * Use KEYWORD0 as a placeholder, and later 0=...
* Example : \`
* Il a tendance à afficher tout les tests, ce qui peut être relou pour faire ressortir les valides. Y'a des options en fonctions des modules pour trier les résultats, au pire grep dessus.

