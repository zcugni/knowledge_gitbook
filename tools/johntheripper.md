---
description: 'Disclaimer : Je ne sais pas encore très bien l''utiliser'
---

# JohnTheRipper

## Généralités

* `sudo john file_to_crack --wordlist=rockyou.txt`
* * [Jumbo version](https://github.com/magnumripper/JohnTheRipper) has scripts available \(in the run folder\) to convert other format to a john crackable format :
    * `ssh2john` Convert an ssh key to crack the password \(not the passphrase, or so i think\)
    * `zip2john` Convert a zip file that needs a password to crack it
* Il auto-detect les modes de hash les plus connus
* Il semble un peu chiant à utiliser quand il y a des salt ou d'autre modifs du genre, faudrait que je regarde les formats précisément un jour

## Installation

* Soit installé dans kali \(mais on a pas tout les scripts de la version jumbo même si certains sont dispo dans /usr/share/john\)
* Soit apt-get install \(mais pareil je crois\)
* Soit la version jumbo via github
  * Install required tools : `sudo apt-get install build-essential libssl-dev`
  * Install recommanded ones : `sudo apt-get install yasm libgmp-dev libpcap-dev libnss3-dev libkrb5-dev pkg-config libbz2-dev zlib1g-dev`
  * git clone it : `git clone git://github.com/magnumripper/JohnTheRipper -b bleeding-jumbo john`
  * Go it it's `/src` and compile : `./configure && make -s clean && make -sj4`

