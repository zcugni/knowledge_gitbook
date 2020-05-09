---
description: >-
  Disclaimer : This page will change a lot. I'm trying to define *my*
  methodology to follow.
---

# Méthodologie

## Début

* Do a quick nmap to get you started \(only most popular ports\)
* Once it's finished, and while you explore the first result, do a full nmap

## Si c'est du web

* Test `index.html`, `index.php`, etc pour définir le langage
* Check `robots.txt` et `sitemap.xml`
* run gobuster \(Récursivement ainsi que les 2 premières étapes sur les infos intéressantes\)
* Check le code source
  * Pour des parties générées par des technos facilement identifiable
  * Chaque commentaires pour voir si ça leak pas des infos
* Check how links are composed, if it's wrongly configured it can be a bit weird \(aka [http://index.php/rest\_of\_link](http://index.php/rest_of_link)\)
* For every input :
  * Check reaction to `'` & `"` \(depending on reaction, check basic sqli injection\)
  * Check basic XSS \(script & img, note what type of protection is in place\)
  * If it's in php and some input are reflected, try to do `<?php phpinfo(); ?>`
* For every url
  * Check if anything is reflected on the page \(and if `'` & `"` break things\)
  * Accessing page out of orders \(for example directly an /edit/\)
  * Force browsing & co
* Changing GET request for POST and vice-versa

## Pour chaque technos

* Si elle est connue, chercher "enumerate techno" sur google, y'a peut-être des outils
* Si c'est open source, regarder sur github pour voir si y'a pas des fichiers que l'on peut accéder qui nous renseigneraient sur la version
* Searchsploit la techno
* Metasploit search la techo
* Chercher aussi sur google "techno exploit" pour compléter

