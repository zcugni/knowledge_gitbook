# Table of contents

* [Hello World !](README.md)

## Pen test

* [Methodology / Recon](pen-test/test.md)
* [Get Shell](pen-test/get-shell.md)
* [Linux Privilege Escalation](pen-test/privilege-escalation.md)
* [Vulnerabilities](pen-test/vulnerabilities/README.md)
  * [HTTP Vulnerabilities](pen-test/vulnerabilities/http-vulnerabilities.md)
  * [XXE and other XML vulnerabilities](pen-test/vulnerabilities/xml-xxe-vuln.md)
  * [XSS - Cross-Site Scripting](pen-test/vulnerabilities/xss.md)
  * [SQL Injection](pen-test/vulnerabilities/sqli.md)
  * [CSRF - Cross-Site Request Forgery](pen-test/vulnerabilities/csrf.md)
  * [URL/Path vulnerabilities](pen-test/vulnerabilities/broken-access-control-and-file-inclusion.md)
  * [Misc Attack](pen-test/vulnerabilities/misc-attack.md)
* [Tools](pen-test/hack-tools/README.md)
  * [Wfuzz](pen-test/hack-tools/wfuzz.md)
  * [JohnTheRipper](pen-test/hack-tools/johntheripper.md)
  * [Metasploit Framework](pen-test/hack-tools/metasploit-framework.md)
  * [Patator](pen-test/hack-tools/patator.md)
* [Network tools](pen-test/network-tools/README.md)
  * [Burp Proxy](pen-test/network-tools/burp-proxy.md)
  * [Nmap](pen-test/network-tools/nmap.md)
  * [tcpdump](pen-test/network-tools/tcpdump.md)
* [Good Practices](pen-test/good-practices.md)

## Binary Exploitation / Low-level <a id="binary-exploitation"></a>

* [Memory](binary-exploitation/memory/README.md)
  * [Registers](binary-exploitation/memory/registers.md)
  * [glibc malloc](binary-exploitation/memory/draft.md)
* [Executable creation](binary-exploitation/executable-creation/README.md)
  * [ELF Linking](binary-exploitation/executable-creation/elf-file-linking.md)
  * [ELF Files](binary-exploitation/executable-creation/elf-files.md)
  * [Compilers](binary-exploitation/executable-creation/compilers.md)
* [Binary, hex & bytes](binary-exploitation/binary-hex-and-bytes.md)
* [Microcorruption.com](binary-exploitation/microcorruption-notes.md)
* [Terminology](binary-exploitation/terminology.md)
* [Tools](binary-exploitation/tools/README.md)
  * [GDB - GNU Debugger](binary-exploitation/tools/gdb-gnu-debugger.md)
* [Vulnerabilities](binary-exploitation/vulnerabilities/README.md)
  * [Format String Vulnerabilities](binary-exploitation/vulnerabilities/format-string-vulnerabilities.md)
  * [Shellcode](binary-exploitation/vulnerabilities/shellcode.md)
  * [Malloc vulnerabilities](binary-exploitation/vulnerabilities/malloc-vulnerabilities.md)

## Linux

* [Theory](linux/linux-nix.md)
* [Command / Tools](linux/linux-bash-command/README.md)
  * [Hacking related cmd](linux/linux-bash-command/hacking-related-cmd.md)
  * [Mount](linux/linux-bash-command/mount.md)
  * [Net tools / commands](linux/linux-bash-command/net-tools-commands.md)
  * [Sudo, su, sudoers, etc](linux/linux-bash-command/sudo-su-sudoers-etc.md)
  * [Tmux](linux/linux-bash-command/tmux.md)
  * [Find](linux/linux-bash-command/find.md)
  * [SSH](linux/linux-bash-command/ssh.md)
  * [Git Commands](linux/linux-bash-command/git-commands.md)
* [Cgroups](linux/cgroups.md)
* [Cron & Crontab](linux/cron-and-crontab.md)
* [File system](linux/file-system.md)
* [Process](linux/process.md)
* [Systemd](linux/systemd.md)

## Networking & Web

* [Models](networking-and-web/models.md)
* [Security hardware](networking-and-web/machines.md)
* [Protocols](networking-and-web/protocols/README.md)
  * [Ethernet](networking-and-web/protocols/layer-2-datalink.md)
  * [HTTP - Hypertext Transfer Protocol](networking-and-web/protocols/http-hypertext-transfer-protocol.md)
  * [ICMP - Internet Control Message Protocol](networking-and-web/protocols/icmp-internet-control-message-protocol.md)
  * [Internet Protocol](networking-and-web/protocols/layer-3-network.md)
  * [SOP & CORS](networking-and-web/protocols/sop-and-cors.md)
  * [TCP - Transmission Control Protocol](networking-and-web/protocols/clean-tcp-2.md)
  * [UDP - User Datagram Protocol](networking-and-web/protocols/udp-user-datagram-protocol.md)
  * [Misc](networking-and-web/protocols/layer-5-application.md)
* [DNS](networking-and-web/dns.md)
* [Vulnerabilities](networking-and-web/vulnerabilities.md)
* [Misc](networking-and-web/misc.md)

## Languages

* [General Concepts](languages/general-concepts.md)
* [Assembly](languages/assembly.md)
* [Bash](languages/bash.md)
* [C](languages/c.md)
* [Haskell](languages/draft.md)
* [Javascript](languages/javascript/README.md)
  * [Jquery](languages/javascript/jquery.md)
* [LDAP](languages/ldap.md)
* [MongoDB](languages/mongodb.md)
* [PHP](languages/php.md)
* [Python](languages/python/README.md)
  * [Syntax](languages/python/syntax.md)
  * [Style guide](languages/python/norme.md)
  * [Pandas](languages/python/libs.md)
* [XML](languages/xml/README.md)
  * [XML DTD](languages/xml/xml-dtd.md)
  * [XPATH](languages/xml/xpath.md)
  * [Other addons](languages/xml/autre-ajouts.md)

## Technology / Protocols

* [Active Directory](technology-protocols/active-directory.md)
* [ElasticSearch](technology-protocols/elasticsearch.md)
* [Finger](technology-protocols/finger.md)
* [Kerberos](technology-protocols/kerberos.md)
* [Redis](technology-protocols/redis.md)
* [SMB](technology-protocols/smb.md)

## Misc Theory <a id="theory"></a>

* [Cryptography](theory/cryptographie-and-hash.md)
* [Bitcoin](theory/bitcoin.md)
* [MCD / MLD](theory/mcd-mld.md)
* [Terminology](theory/terminologie-misc.md)
* [Tor](theory/tor.md)
* [Machine Learning](theory/machine-learning/README.md)
  * [Udemy Course Summary](theory/machine-learning/udemy.md)
  * [Fonction de minimisation](theory/machine-learning/fonction-de-minimisation.md)
  * [Fonction de coûts](theory/machine-learning/fonction-de-couts.md)
  * [Coursera](theory/machine-learning/coursera.md)
  * [Backward Elimination](theory/machine-learning/backward-elimination.md)
* [Git](theory/git.md)

## Oh hold

* [Windows networking commands](oh-hold/windows-networking-commands.md)
* [MySQL](oh-hold/mysql.md)
* [Methodology reverse engienering](oh-hold/methodologie-reverse-engienering.md)
* [BDD Language differencies](oh-hold/bdd-language-differencies.md)
* [Misc](oh-hold/misc.md)

