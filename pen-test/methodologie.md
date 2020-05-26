---
description: >-
  Disclaimer : This page will change a lot. I'm trying to define *my*
  methodology to follow.
---

# Méthodologie

## Général

Les sections en dessous devraient être mélangées afin de varier les trouvailles et de se faire une idée globale de la situation.

Commencer avec une broad research en ensuite se concentrer sur les quelques points critiques.

Exemple d'un déroulement :

1. Premier nmap
2. Si web, recherche du langage
3. Lancement du 2ième nmap et du gobuster basique
4. Recherche searchpsloit & co par technos
5. Run du second gobuster
6. Explorations des trouvailles sur les technos /go/etc

## Nmap

1. Do a quick [nmap](https://zcugni.gitbook.io/notes/tools/linux-bash-command/nmap) to get you started \(only most popular ports\)
2. Once it's finished, and while you explore the first result, do a full nmap

## Web

1. Test `index.html`, `index.php`, etc pour définir le langage
2. Check `robots.txt` & `sitemap.xml`
3. Run [gobuster](https://zcugni.gitbook.io/notes/tools/hack-tools#forced-browsing) 
   1. En utilisant les extensions \(au moins txt\)
   2. En utilisant le /
4. Faire ces 3 étapes récursivement si on trouve des dossiers intéressants
5. Check le code source
   1. Pour des parties générées par des technos facilement identifiable
   2. Chaque commentaires pour voir si ça leak pas des infos
6. Check how links are composed, if it's wrongly configured it can be a bit weird \(aka [http://index.php/rest\_of\_link](http://index.php/rest_of_link)\)
7. For every input :
   1. Check reaction to `'` & `"` \(depending on reaction, check basic sqli injection\)
   2. Check basic XSS \(script & img, note what type of protection is in place\)
   3. If it's in php and some input are reflected, try to do `<?php phpinfo(); ?>`
8. For every url :
   1. Check if anything is reflected on the page \(and if `'` & `"` break things\)
   2. Accessing page out of orders \(for example directly an /edit/\)
   3. Changing GET request for POST and vice-versa

## Pour chaque technos

1. Récupérer la version
   1. Chercher "enumerate techno" sur google, y'a peut-être déjà des outils
   2. Si c'est open source, check le github pour regarder l'arborescence des fichiers et où l'on pourrait trouver ces infos
2. Searchsploit, metasploit et google "techno exploit"

