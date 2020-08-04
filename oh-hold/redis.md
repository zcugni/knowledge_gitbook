# Redis

## Généralités

> Redis is an open source \(BSD licensed\), in-memory data structure store, used as a database, cache and message broker.

* De ce que j'en comprends, c'est une sorte de db dans la RAM que tu peux ensuite écrire dans des fichiers.
* Redis est fait pour être utilisé et accessible uniquement dans un environement protégé, et cela ouvre de nombreuses vuln quand c'est pas le cas
* [https://book.hacktricks.xyz/pentesting/6379-pentesting-redis](https://book.hacktricks.xyz/pentesting/6379-pentesting-redis) pour des exemples d'exploit & plus d'utilisations
* [https://redis.io/commands](https://redis.io/commands) Liste & explication des commandes

## Connexion & Cli

* Si redis a été mal protégé, on peut lui envoyer des commandes sans se co
* Une cli rends l'utilisation plus aisée
  * Installation : `apt-get install redis-tools`
  * Utilisation : `redis-cli -h ip`
    * Pour set une variable dans redis à partir d'un cat/echo : `cat key.txt | redis-cli -h ip -x set ssh_key`
    * `-x` permet de récupérer la dernière valeur de stdin \(voir commande pour set\)
* On peut aussi directement utiliser netcat pour envoyer les commandes `nc ip 6379`
* Le _home directory_ de redis est `/var/lib/redis`

## Commandes

* `info`
* `config get *`
* `config set dir <dir_name>` Nous déplace dans le répertoire \(s'il existe, no clue si on peut en créer un sur le tas\)
* `config set dbfilename <name>` Définit le fichier dans lequel on va sauvegarder les données
* `set <key> <"value">` Crée une clé \(aka variable\) redis
* `get <key>` Lit la variable
* `save` Ecrit la mémoire en RAM dans le fichier configuré. Si on a injecté quelque chose dans celui-ci, l'entouré de "/n/n" pour isoler notre partie de la junk actuellement en mémoire

