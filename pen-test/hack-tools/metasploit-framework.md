---
description: 'Disclaimer : Je ne sais pas encore très bien l''utiliser'
---

# Metasploit Framework

## Generalities

* Suite of tools to use exploit
* The principal interface is the console `msfconsole` linked to a database `msfdb`
* In addition to the exploit, it can save information on the target \(like the hosts, result of nmap, etc\) in the db
  * With that, you can efficiently centralise information
* Written in ruby

## Launch

* Run `sudo msfconsole`, it also launches the db
  * You can also launch only the db with `sudo msfdb run`
* The first time on kali run first :
  * `systemctl start postgresql`
  * `sudo msfdb init`
* If you get this error`cannot load such file — bundler/setup`, its probably because you don't have the right set up for ruby, use `sudo gem install bundler`

## Commands

### General

You can use them everywhere

| Command | Effet |
| :--- | :--- |
| `search ...` | Montre les modules correspondant aux termes cherchés |
| `use <module_path | module_number>` | Select module \(exploit\) |
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

