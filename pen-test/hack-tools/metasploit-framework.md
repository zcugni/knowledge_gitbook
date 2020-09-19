---
description: 'Disclaimer : Je ne sais pas encore très bien l''utiliser'
---

# Metasploit Framework

## Generalities

* Suite of tools to use exploit
* The principal interface is the console `msfconsole` linked to a database `msfdb`
* In addition to the exploit, it can save information on the target \(like the hosts, result of nmap, etc\) in the db
  * With that, you can efficiently centralise information
* Exploit are called modules
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
| `search ...` |  |
| `use <module_path | module_number>` |  |
| `back` | Un-select a module \(you don't need to, the other cmds can be used even if you have a selected module\). |
| `info <module_path>` |  |
|  `exit` & `quit` |  |

### With a selected module

| Commande | Effet |
| :--- | :--- |
| `show options` |  |
| `advanced` |  |
| `set <option> <value>` |  |
| `unset <option>` |  |
| `check` | Check if target is vulnerable |
| `rcheck` | Same as check, but reload module before |
| `reload` | Reload module |
| `run` & `exploit` |  |

* `LHOST` \(a commun option\) wants an interface, `tun0` works in general
* Set a proxy : `set Proxies http:127.0.0.1:8080`
  * In burp, turn on the intercepter and before clicking forward, right click an `Do intercept -> Response to this request`
  * Forward rapidly because metasploit might consider its failed otherwise

## Sources

* [IppSec](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA)
* Help page of msfconsole
* Misc research \(aka StackOverflow\)

