---
description: 'Disclaimer : Je ne sais pas encore très bien l''utiliser'
---

# Metasploit Framework

## Généralités

* Collection d'exploits, outils, payloads, etc permettant d'utiliser des vulnérabilités connues
* Pleins de plugins et d'ajout, le rendant assez customisable et puissant
* Ecrit en ruby

## msfconsole

* L'interface primaire du framework

### Commandes basiques

* `search service_name` Search the db for exploit on this service
* `use exploit_name` Select the exploit \(copy the name from the result of the search\)
  * `show options` Donnes les options de l'exploit
  * `set option_name value` Set the given option
  * `run` Run l'exploit avec les options désignées
  * \(il a set `LHOST` à `tun0` mais je suis pas sûre de comprendre pourquoi\)
  * \(Si ça marche, ça devrait ouvrir un command shell, quoique ça doit déprendre du type d'exploit, auquel cas je peux écrire `whoami` dedans pour voir si ça fonctionne\)
  * Pour voir le payload envoyé par metasploit quand c'est des exploit web, le plus simple est de le faire passer par burp en utiliser `set Proxies http:127.0.0.1:8080` \(c'est important de ne pas mettre les `//`, je sais pas pourquoi\)
  * Dans burp, faut avoir mis l'intercept on et avant de forward la request : clic droit "do intercept" -&gt; "response to this request"
  * \(Faut forward vite car sinon metasploit considère que ça n'a pas marché\)

## msfdb

### Généralités

* Contient les exploits, etc
* On peut aussi y importer les résultats de nmap ou d'autre info similaire pour la remplir et toutes les avoir au même endroit. Cela permet aussi de spécifier les options des exploits directement via la db

### Lancement

* Sur kali il faut lancer `systemctl start postgresql` puis `sudo msfdb init`
* On peut ensuite utiliser `msfdb run` pour lancer la db et run msfconsole
* Si j'ai cette erreur `cannot load such file — bundler/setup`, c'est probablement parce que j'ai pas installé ruby. Pour corriger ça, faire ceci : 
  * `sudo gem update --system`
  * `sudo gem install bundler:1.17.3`
  * `cd /usr/share/metasploit-framework`
  * `sudo bundle install`

