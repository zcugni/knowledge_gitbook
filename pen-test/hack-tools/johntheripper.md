---
description: 'Disclaimer : Je ne sais pas encore très bien l''utiliser'
---

# JohnTheRipper

## Generalities

* Password cracker
* `sudo john <file_to_crack> --wordlist=rockyou.txt`
* [Jumbo version](https://github.com/magnumripper/JohnTheRipper) has scripts available \(in the `run` folder\) to convert other format to a john crackable format :
  * `ssh2john` Convert an ssh key to crack the password \(not the passphrase, or so i think\)
  * `zip2john` Convert a zip file that needs a password to crack it
* It auto detects cummon hash mode
* Seems a bit complicated to use when dealing with salt and other modification

## Installation

* Either directly in kali \(but it doesn't have all the scripts of the jumbo version even if some are in `/usr/share/john`\)
* Either `apt-get install`\(same problem\)
* Either the jumbo version in github
  * Install required tools : `sudo apt-get install build-essential libssl-dev`
  * Install recommended ones : `sudo apt-get install yasm libgmp-dev libpcap-dev libnss3-dev libkrb5-dev pkg-config libbz2-dev zlib1g-dev`
  * git clone it : `git clone git://github.com/magnumripper/JohnTheRipper -b bleeding-jumbo john`
  * Go to it's `/src` and compile : `./configure && make -s clean && make -sj4`

## Sources

* JohnTheRipper's man page
* JohnTheRipper's github
* Misc research \(aka StackOverflow\)

