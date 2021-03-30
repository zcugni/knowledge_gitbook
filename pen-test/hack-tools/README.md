# Tools

## Liste / CheatSheet

* `rockyou.txt` A breach of 32 millions user's password
* [gtfobin](https://gtfobins.github.io/) List of linux command that may facilitate pric esc \(like vim through some parameters\)
* [fuzzdb](https://github.com/fuzzdb-project/fuzzdb)
* [SecList](https://github.com/danielmiessler/SecLists)
* [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)
* [OWASP's CheatSheet](https://github.com/OWASP/CheatSheetSeries/tree/master/cheatsheets)
* [Google Hacking Database](https://www.exploit-db.com/google-hacking-database)
* [random\_robbie](https://github.com/random-robbie/keywords/blob/master/keywords.txt) Keywords for credentials, etc
* [skullssecurity](https://wiki.skullsecurity.org/Passwords) Password list

## Proxy

[Burp Proxy](https://zcugni.gitbook.io/notes/tools/burp-proxy)

## gobuster \(File Busting\)

* Has 3 mode, to enumerates files/dir, dns or vhost
* `apt-get install gobuster`
* My default command for dir : `gobuster dir -u <ip | hostname> -w <wordlist_path> [-x php, txt] [-t 10] -o <result_file> 2> <error_file>` 
  * `/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt` is my default word list
  * `-x` To add extension, `-t` for threads number
* Keep in mind that even if you can't access a dir \(/dev for example\), you might be able to access it's subdir/files \(/dev/backup for example\)
* Sometimes, a dir won't show up if you don't explicitly use a `/` after it \(aka `/cgi-bin` don't exist, but `/cgi-bin/` does\). Use  `-f` to add the slash and test for it
  * The majority of the time, it seems to work without, i'm not sure what the diff is
* You can put it in a bash loop if you need to do it recursively \(directly in the cmd\):

  ```bash
    for $name in example1 example2 admin dev etc;
        do gobuster dir -u ip/$name w wordlist_path -o $name.txt;
    done
  ```

## Exploit database

* `searchsploit` is the command-line utility giving easy access to the exploit db
  * Remember to use `-u` to update it's db
* [https://exploits.shodan.io/](https://exploits.shodan.io/)
* [metasploit](https://zcugni.gitbook.io/notes/tools/metasploit-framework)
* [hacktricks](https://book.hacktricks.xyz/)
* Don't hesitate to simply search "exploit x" on google

## CMS Mapping / scan

* cmsmap
* WPscan

## SMB mapping <a id="m_4937061005691869274gmail-smb-mapping"></a>

* `smbmap`
* The man is well done, check it
* `-H <ip>` \(Mandatory\)
* `-d <domain_name>`
* `-u <username>`
* `-R [share_name]` Recursively list all dirs & files within the share
  * If you don't specifies the share, it will go through all of them
  * To download a file, use : `smbmap -H <ip> -R [share_name] -A <file_name> [-q]`
    * The `-q` is useful because it's quite verbose

## Impacket \(Network protocol scripts\)

* A collection of python scripts for working with network protocols
* There's a lot of example scripts that you can use as is, like :
  * GetADUsers.py, which enumerate AD users
  * GetUserSPN.py, which will get a user SPN that you can crack \(for kerberoast\)

## Misc software scan

* Magento : [magescan](https://github.com/steverobbins/magescan)
* Joomla : [joomscan](https://www.securiteinfo.com/attaques/hacking/outils/joomscan.shtml)

## Password craking

* Cwel : Spider a website and generate a custom word list
* [JohnTheRipper](https://zcugni.gitbook.io/notes/tools/johntheripper)
* Hashcat
  * `hashcat -a 0 -m 20 hash:salt path/to/rockyou.txt`
    * `-a` Define the attack type \(0 for dictionary i think\)
    * `-m` The hash mode, there's multiple available, check the example to find the right one
      *  20 is for md5 with an appended salt
  * Quite optimized \(but only for some hardware\)
  * Easier to use than john

## Privilege escalation

* [Peas suite](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite)
  * Quite verbose, shows even the non successful test
  * Link to explanations
  * I still like it quite a lot
* [LinEnum](https://github.com/rebootuser/LinEnum)
  * Use`-t` for complete tests 
  * To the point,gives only the info it finds
  * However, list a lot of files so not always practical

## Pspy - Process Analysis

* [pspy](https://github.com/DominicBreuker/pspy) Lets you see called process without being root
* Don't forget to connect again via ssh after launching it to see what is run when somebody connects
* To install run  `go build` 
  * \(Golang needs to be installed `sudo apt-get install golang`\)
  * If it says that it doesn't know a packet, first run `go get github.com/dominicbreuker/pspy/cmd`

## Fuzzing

* wfuzz
  * 
* Hydra
* [Patator](https://zcugni.gitbook.io/notes/tools/patator)
* Seclist has good specific fuzzing lists

## Misc

* [https://gchq.github.io/CyberChef/](CyberChef)  Let you chain commands on a string/file \(like decompress, then base64, then dexdump, etc\)
* Stegonography : steghide

## Not yet tested

These are advised by OWASP or i stumble upon them but i haven't yet used them

* CSRF :
  * CSRF tester
  * Cross Site Requester
  * Cross Frame Loader
  * Pinata-csrf-tool
* Directory traversal : [DotDotPwn](https://github.com/wireghoul/dotdotpwn/blob/master/dotdotpwn.pl)
* Enumerate subdomain :
  * [https://dnsdumpster.com/](https://dnsdumpster.com/)
  * [https://github.com/aboul3la/Sublist3r](https://github.com/aboul3la/Sublist3r)
* Fingerprint website :
  * OWASP's amass
  * httprint
  * httprecon
  * builtwith
  * wappalyzer \(chrome extension\)
  * [whatweb](https://github.com/urbanadventurer/WhatWeb)
  * blindElephant
* Github dork : [gitrob](https://github.com/michenriksen/gitrob)
* Pad Oracle : padbuster
* Spider : 
  * Sam Spade
  * Spike Proxy
  * Xenu
  * Webscarab
* SQL injection : SQLmap
* Vulnerability scanner
  * OpenVAS
  * Nexpose
  * GFI Lan Guard
  * Nessus
* Wayback machine
  * [waybackurls](https://github.com/tomnomnom/waybackurls)
  * [waybackunifier](https://github.com/mhmdiaa/waybackunifier)
* XSS
  * bXSS
  * ezXSS
  * Jackmasa's XSS mindmap
* XSS \(blind\)
  * XSS Hunter
  * Sleepy Puppy
  * KnoXSS

## Sources

* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)
* [IppSec](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA)
* Misc research

