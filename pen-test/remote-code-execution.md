# Remote Code Execution

## Principe

* J'ignore si l'abréviation RCE correspond à _Remote Code Execution_ ou _Remote Command Execution_, les deux étaient utilisés pour le même genre de failles dans le cours.
* Possibilité d'exécuter des commandes de l'OS sur le serveur.

## Workflow

1. Discover on which language the server runs \(python, php, ruby, etc\), and if possible which version
2. Try to break the input by inserting a `'` or an `"`
3. Try to reconstruct it after the break with just simple text
4. In function of the language, try to run a simple OS command in lieu of the dummy text
5. Construct the final payload

## Language specifics to run commands

* PHP
  * `system("command")`
* Python
  * os.system\("command"\) and variants
  * Do that inside an `str()`
  * If you need to import os.system, use this function in str\(\) : `__import__('os').system(...)`
* Ruby
  * `command`

## General tips & trick

* A basic OS command for test is `uname` \(but i suppose it would work only on a linux system, however it's usually the case no ?\)
* When you payload is included inside a string, **use concatenation** : `" . system('cat /etc/passwd') ."`
* Don't forget to **urlencode +**
* If some characters seems to be sanitized when the request first arrive, try to base64 encode them. Since the code you inject is executed, you can make the server decode it the moments it uses it.

## Php specific tips and tricks

* Test injection with a simple `phpinfo()`
* If you've got input reflected into the html page, try to inject \`&lt;?php phpinfo\(\); ?&gt;
* Try to get warnings instead of errors as they won't stop the execution flow
* Php source code is available, which is useful to see how you can break out of a fonction
* In php, dev usually sort either by using sql's `order by` or php's `usort`. And in usort, they often used a function created with `create_function` \(however it's now deprecated\)
* In an example using usort and then create\_function, the easiest is to try and break out create\_function, even more so since we can have it's source code. This is the pentesterlab payload that would break it \(Essential - Code Execution 2\) : `?order=id);}system('cat /etc/passwd');//`. I thinks that stems from the fact that it's used in as `sprintf` as a `%s`.
* It's been deprecated in 5.5 and removed in 7, but you could use the regex modifier `/e` to make `preg_replace` evaluate the replacement before using it :  `www.example.com/?replacement=phpinfo()&pattern=/hello/e&base=hello`
