---
description: >-
  This is a summary of the OWASP pen testing book and some information found
  here and there. I haven't yet select the tools i prefer. I just explained some
  of them
---

# Hack tools

## Disclaimer

Je vais ici lister tous les outils par catégorie \(quand je l'a connais\). Au fur et à mesure je trierais mes préférés et expliquerais comment les utiliser.

Il y a en a pour tout, donc ça vaut la peine d'arpenter github pour trouver des perles.

## Liste / CheatSheet

* `rockyou.txt` -&gt; Une breach des mdp de 32 millions d'utilisateur \(dispo dans 
* [gtfobin](https://gtfobins.github.io/) Liste d'utilisation des commandes linux permettant de sudo/shell & co \(aka vim, etc\)
* [fuzzdb](https://github.com/fuzzdb-project/fuzzdb)
* [SecList](https://github.com/danielmiessler/SecLists)
* [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)
* [OWASP's CheatSheet](https://github.com/OWASP/CheatSheetSeries/tree/master/cheatsheets)
* [Google Hacking Database](https://www.exploit-db.com/google-hacking-database)
* Keywords for credentials/etc : [random\_robbie](https://github.com/random-robbie/keywords/blob/master/keywords.txt)
* [https://github.com/danielmiessler/SecLists/tree/master/Passwords/Leaked-Databases](https://github.com/danielmiessler/SecLists/tree/master/Passwords/Leaked-Databases)\)
* Password list : [skullssecurity](https://wiki.skullsecurity.org/Passwords)

## Proxy

[Burp Proxy](https://zcugni.gitbook.io/notes/tools/burp-proxy)

## Spider

Not tested

* Sam Spade
* Spike Proxy
* Xenu
* Webscarab

## Recon

### Fingerprint website

Not tested

* OWASP's amass
* whois \(command\)
* httprint
* httprecon
* builtwith \(site\)
* wappalyzer \(extension chrome\)
* [whatweb](https://github.com/urbanadventurer/WhatWeb) \(site\)
* blindElephant

### Wayback machine

Not tested

* [waybackurls](https://github.com/tomnomnom/waybackurls)
* [waybackunifier](https://github.com/mhmdiaa/waybackunifier)

### Forced browsing

* gobuster
  * installer via `apt-get install gobuster`
  * quite easy so read help in doubt
  * default command could be `gobuster dir -u ip/hostname -w wordlist_path -o result_file`
  * Don't forget to add `-x php,txt` \(for example\) to append ".php" and ".txt" to each name
    * It's gonna take time but it's worth it. Don't hesitate to add more threads
  * Increase thread numbers if you're doing a lot of recon
  * You can put it in a bash loop if you need to do it recursively \(directly in the cmd\):

    ```bash
      for $name in example1 example2 admin dev etc;
          do gobuster dir -u ip/$name w wordlist_path -o $name.txt;
      done
    ```

  * \(/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt default word list\)
  * Keep in mind that even if you can't access a dir \(/dev for example\), you might be able to access it's subdir/files \(/dev/backup for example\)

### Github dork

Not tested

* [gitrob](https://github.com/michenriksen/gitrob)

## Vulnérabilités

### Exploit

* `searchsploit` is the command-line utility giving easy access to the exploit db
* [https://exploits.shodan.io/](https://exploits.shodan.io/)
* [metasploit](https://zcugni.gitbook.io/notes/tools/metasploit-framework)
* Ces bdds ne sont pas toujours complètes, simplement faire une recherche google "nom exploit" peut s'avérer fructueuse
* [hacktricks](https://book.hacktricks.xyz/)

### XSS

Not tested

* bXSS \(github\)
* ezXSS \(github\)
* Jackmasa's XSS mindmap

#### Blind XSS

* XSS Hunter
* Sleepy Puppy
* KnoXSS

### CSRF

Not tested

* CSRF tester
* Cross Site Requester
* Cross Frame Loader
* Pinata-csrf-tool

### Directory Traversal

Not tested

* DotDotPwn : [https://github.com/wireghoul/dotdotpwn/blob/master/dotdotpwn.pl](https://github.com/wireghoul/dotdotpwn/blob/master/dotdotpwn.pl)

### Pad oracle

Not tested

* padbuster

### SQL Injection

Not tested

* SQLmap

### Vulnerability Scanner

Not tested

* OpenVAS
* Nexpose
* GFI Lan Guard
* Nessus

## Misc software scan

* Magento : [https://github.com/steverobbins/magescan](magescan)
* Joomla : [joomscan](https://www.securiteinfo.com/attaques/hacking/outils/joomscan.shtml)

## Stegonography

* steghide

## Password craking

* Cwel
  * Spider a website and generate a custom word list of words that could be used for password cracking by other tools such as John
* [JohnTheRipper](https://zcugni.gitbook.io/notes/tools/johntheripper)
* Hashcat
  * `hashcat -a 0 -m 20 hash:salt path/to/rockyou.txt`
    * `-a` Définit le type d'attaque \(0 pour une attaque dico je pense\)
    * `-m` Le mode de hash, y'en a plein de différents. 20 Correspond à un md5 avec un salt append
  * Assez optimisé \(mais ces améliorations ne marchent qu'avec certains hardware\)
  * Les modes semblent plus simple d'utilisation qu'avec John

## Privilege escalation

* [Peas suite](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite)
  * Assez verbose, énonce tous les tests qu'il fait même quand ils ne sont pas fructueux
  * Link vers ces explications, etc
  * \(Mais efficace\)
* [LinEnum](https://github.com/rebootuser/LinEnum)
  * Lancer `./LinEnum.sh -t` pour avoir les tests complets
  * To the point, il ne donne que les infos qu'il trouve

## Process Analysis

* [pspy](https://github.com/DominicBreuker/pspy) Permet de voir les process appelés sans être root, peut être utile pour trouver des programmes appelés sans qu'un chemin soit utilisé par exemple.
  * Penser à se reconnecter via ssh après l'avoir lancé \(dans une autre fenêtre\), pour voir ce qui est lancé à la co
  * Pour l'installation, lancer `go build` après avoir installé golang `sudo apt-get install golang`
    * S'il nous dit qu'il ne connait pas le paquet, d'abord lancé `go get github.com/dominicbreuker/pspy/cmd`

## Fuzzing

* wfuzz
  * Not good with SSL or with website using CSRF tokens
  * Will fuzz places of the string where you wrote "FUZZ"
  * `wfuzz -u url -d "username=asd&password=FUZZ" -w wordlist` \(d specifies post data\)
  * On peut le faire passer par un proxy avec `-p 127.0.0.1:8080`
  * On peut filtrer les résultats pour cacher les status code/nombre de lignes/nombre de mots/nombre de caractères spécifique avec `--hc/hl/hw/hh ..`
  * utiliser `''` au lieu de `""` autour des paramètres car certains char peuvent faire bugger le shell s'ils sont interprétés
* Hydra
* [Patator](https://zcugni.gitbook.io/notes/tools/patator)
* Seclist has good specific fuzzing lists

## Misc

* [https://gchq.github.io/CyberChef/](CyberChef) Permet d’enchaîner pleins d'actions de cmd sur une string/un fichier, etc \(genre décompresser puis base64 puis hexdump, etc\)

