---
description: 'Disclaimer : Je ne sais pas encore très bien l''utiliser'
---

# Patator

## Generalities

* Different module to brute-force \(list-based\)
* Created to centralise Hydra, nmap's script, metasploit, etc in the same place
* As of know you can :
  * `ftp_login` Brute-force FTP
  * `ssh_login` Brute-force SSH
  * `telnet_login` Brute-force Telnet
  * `smtp_login` Brute-force SMTP
  * `smtp_vrfy` Enumerate valid users using the SMTP VRFY command
  * `smtp_rcpt` Enumerate valid users using the SMTP RCPT TO command
  * `finger_lookup` Enumerate valid users using Finger
  * `http_fuzz` Brute-force HTTP/HTTPS
  * `rdp_gateway` Brute-force RDP Gateway
  * `ajp_fuzz` Brute-force AJP
  * `pop_login` Brute-force POP
  * `pop_passd` Brute-force poppassd \(not POP3\)
  * `imap_login` Brute-force IMAP
  * `ldap_login` Brute-force LDAP
  * `smb_login` Brute-force SMB
  * `smb_lookupsid` Brute-force SMB SID-lookup
  * `rlogin_login` Brute-force rlogin
  * `vmauthd_login` Brute-force VMware Authentication Daemon
  * `mssql_login` Brute-force MSSQL
  * `oracle_login` Brute-force Oracle
  * `mysql_login` Brute-force MySQL
  * `mysql_query` Brute-force MySQL queries
  * `rdp_login` Brute-force RDP \(NLA\)
  * `pgsql_login` Brute-force PostgreSQL
  * `vnc_login` Brute-force VNC
  * `dns_forward` Brute-force DNS
  * `dns_reverse` Brute-force DNS \(reverse lookup subnets\)
  * `ike_enum` Enumerate IKE transforms
  * `snmp_login` Brute-force SNMPv1/2 and SNMPv3
  * `unzip_pass` Brute-force the password of encrypted ZIP files
  * `keystore_pass` Brute-force the password of Java keystore files
  * `sqlcipher_pass` Brute-force the password of SQLCipher-encrypted databases
  * `umbraco_crack` Crack Umbraco HMAC-SHA1 password hashes

## Usage

* `python3 patator.py <module> base_option=<...> -other -options`
* Each module has some base option like "host", "port", etc
* These keywords lets you specify what to fuzz :
  * `FILE` Iterate over a file
  * `COMBO` Iterate over a combo file
  * `NET` Iterate over every hosts of a network subnet
  * `RANGE` Iterate over hexadecimal, decimal or alphabetical ranges
  * `PROG` Iterate over the output of an external program
  * Use KEYWORD0 as a placeholder, and later 0=...
* It returns all tests, even invalid one
  * Use options or grep to filter

## Sources

* Patator's [github](https://github.com/lanjelot/patator)
* Test

