---
description: >-
  Disclaimer : This page will change a lot. I'm trying to define *my*
  methodology to follow.
---

# Methodology

## Example

* Start broad to get an idea of attack vectors
* An example of the process : 
  * Do a rapid nmap \(only the principle ports\)
  * If there's a web techno, do the first 3 steps
  * Run a second nmap when the first one is finished \(with all the port this time\)
  * Follow the instructions regarding each techno

## Nmap

1. Do a quick [nmap](https://zcugni.gitbook.io/notes/tools/linux-bash-command/nmap) to get you started \(only most popular ports\)
2. Once it's finished, and while you explore the first result, do a full nmap

## Web

* Test `index.html`, `index.php`, etc to define the language used
* Check `robots.txt` & `sitemap.xml`
* Run [gobuster](https://zcugni.gitbook.io/notes/tools/hack-tools#forced-browsing) 
  * First with extensions \(at least `txt`\)
  * Then recursively on interesting directories
  * Eventually with `-f` for the `/` \(also recursively\)
  * This should be done while you research other stuff
* Check the source code for :
  * Automatically generated content \(by framework, etc\)
  * Interesting comments
  * The code in itself
* Check how links are composed, if it's wrongly configured it can be a bit weird \(for example : [http://index.php/rest\_of\_link](http://index.php/rest_of_link)\)
* For every input :
  * Check reaction to `'` & `"` \(depending on reaction, check basic sqli injection\)
  * Check basic XSS \(script & img, note what type of protection is in place\)
  * If it's in php and some input are reflected, try to do `<?php phpinfo(); ?>`
* For every url :
  * Check if anything is reflected on the page \(and if `'` & `"` break things\)
  * Try accessing page out of orders \(for example directly an /edit/\)
  * Changing GET request for POST and vice-versa

## For each tech

* Get the version
  * Search "enumarate &lt;tech&gt;" or "scan &lt;tech&gt;" on google to see if there's some tools already
  * If it's open source, check the github 
* Test searchsploit, metasploit & google "exploit tech"

