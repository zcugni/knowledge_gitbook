---
description: 'Disclaimer : Je ne sais pas encore très bien l''utiliser'
---

# Metasploit Framework

## Généralités

* Suite d'outils permettant d'utiliser des exploits et de faciliter le pen test
* La principae interface est la console : `msfconsole` fonctionnant avec une db : `msfdb`
* En plus des exploits, on peut aussi enregistrer des informations sur un target \(comme les hosts, les résultats de nmap, etc\) dans la db.
  * Ca permet de tout centraliser efficacement mais j'ai pas vraiment testé cette partie de l'outil
* Ecrit en ruby

## Lancement

* On lance la console simplement avec `sudo msfconsole` elle launch aussi la db \(je crois\)
* On peut faire de même via  `sudo msfdb run`
* La première fois sur kali il faut peut-être lancé ça avant :  `systemctl start postgresql` puis `sudo msfdb init`
* Si j'ai cette erreur `cannot load such file — bundler/setup`, c'est probablement parce que j'ai pas le bon setup pour ruby. Simplement faire `sudo gem install bundler`

## Commandes

Ces commandes peuvent être utilisées constamment :

| Commande | Effet |
| :--- | :--- |
| `search ...` | Montre les modules correspondant aux termes cherchés |
| `use <module_path>` | Select module \(exploit\) |
| `back` | Un-select a module \(you don't need to, the other cmds can be used even if you have a selected module\). |
| `info <module_path>` | Shows basic descriptions, options, etc of a module |
|  `exit` & `quit` | Exit the console |

* &lt;module\_path&gt; Is found in the result of search

Ces commandes peuvent être utilisée avec un module sélectionné :

| Commande | Effet |
| :--- | :--- |
| `show options` | Montre les paramètres du module |
| `advanced` | Shows advanced options |
| `set <option> <value>` |  |
| `unset <option>` |  |
| `check` | Check if target is vulnerable |
| `rcheck` | Same as check, but reload module before |
| `reload` | Reload module |
| `run` & `exploit` | Run l'exploit avec les options désignées \(1\) |

* \(1\) `LHOST` accepte une interface, `tun0` fonctionne généralement
* Utiliser un proxy : `set Proxies http:127.0.0.1:8080`
  * Dans burp, mettre l'intercept sur "on" et avant de forward la request : clic droit "do intercept" -&gt; "response to this request"
  * Faut forward vite car sinon metasploit considère que ça n'a pas marché

## Sources

* [IppSec](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA)
* Help page of msfconsole
* Misc research \(aka StackOverflow\)

