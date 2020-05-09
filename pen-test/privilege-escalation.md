---
description: >-
  Disclaimer : Tricks i learned while doing hack the box, by no mean complete.
  It's not my main focus currently.
---

# Privilege Escalation

J'utilise évidemment les scripts, mais je voulais noter ici les éléments spécifiques à chercher pour résoudre certaines boxes.

* Check la **date de la distribution** linux, si elle a ne serait-ce qu'un peu plus d'une année c'est probable qu'elle est une exploit \(check google, searchsploit les a pas toutes\)
* Fichiers `.bak` sensible ?
* **Ports** ouverts sur localhost ? \(Port forward si besoin\)
* `sudo -l` Check les commandes \(root ?\) possible pour cet utilisateur. Si on les lances avec `sudo` ce qu'elles font sera exécuté par root
* Un job **cron** lancé par un utilisateur aura ces droits, donc si on arrive à faire en sorte que ce qu'il appelle lance un shell, on en aura un avec des droits
* Obtenir la liste de tout les fichiers own par root mais n'appartenant pas au groupe de root \(script custom\) : `find / -user root ! -group root -ls 2> /dev/null` \(`-ls` is an option of find that runs ls -dils format on what it finds\)
  * Si on arrive à devenir un des membres d'un de ces groupes et si un de ces fichiers nous permet de lancer une commance, celle-ci sera lancé en tant que root 
* Si on peut écrire dans un des répertoires spécifiés dans le **$PATH**, en particulier si on peut écrire dans le premier, il nous suffit de trouver un programme appelé sans chemin absolu pour le hijack. \(En créant un fichier du même nom dans le dit répertoire, attention : **le rendre exécutable**\).
  * On peut trouver un process appelé ainsi via pspy par exemple, qui regarde tous les process appelé au fil du temps et donne leur commandes au passage.
* On peut lancer un shell depuis **vim** avec `:!/bin/bash`, donc si celui-ci est lancé par root, le shell créé le sera aussi
* Si on y a accès, check `.bash_history` pour voir s'il ne leak pas des mdp ou des fichiers intéressants
* Vérifier que les fichiers ayant un **SUID** ne puisse pas être hijack
* Si on arrive sur un **serveur web** chercher le fichier de confid de la db et tenter de se co pour ensuite explorer les tables d'utilisateurs & co
* Si un script change les droits d'un fichier, si on fait un **lien symbolique** entre ce nom de fichier et un fichier qui nous intéresse, cela changera aussi les droits du fichier qui nous intéresse.

