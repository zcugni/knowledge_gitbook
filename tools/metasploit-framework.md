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

* `search <service>` Search the db for exploit on this service
* `use <exploit_path>` Select the exploit \(copy the name from the result of the search\)
  * `show options` Donne les options de l'exploit
  * `set <option> <value>` Set the given option
  * `run` Run l'exploit avec les options désignées
  * Il a set `LHOST` à `tun0` mais je suis pas sûre de comprendre pourquoi
  * On peut faire passer les explois web par un proxy`set Proxies http:127.0.0.1:8080`
    * Dans burp, faut avoir mis l'intercept on et avant de forward la request : clic droit "do intercept" -&gt; "response to this request"
    * Faut forward vite car sinon metasploit considère que ça n'a pas marché

## msfdb

### Généralités

* Contient les exploits, etc
* On peut aussi y importer les résultats de nmap ou d'autre info similaire pour la remplir et toutes les avoir au même endroit. Cela permet aussi de spécifier les options des exploits directement via la db

### Lancement

* Sur kali il faut lancer `systemctl start postgresql` puis `sudo msfdb init`
* On peut ensuite utiliser `sudo msfdb run` pour lancer la db et run msfconsole
* Si j'ai cette erreur `cannot load such file — bundler/setup`, c'est probablement parce que j'ai pas le bon setup pour ruby. Simplement `sudo gem install bundler`

## Sources

* [IppSec](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA)
* Misc research \(aka StackOverflow\)

