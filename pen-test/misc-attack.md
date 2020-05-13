---
description: >-
  Disclaimer : Some of theses are duplicates of concept i better explained
  elsewhere, i'll clean it one day
---

# Misc Attack

## HTTP Method

* Sometimes checks implemented for GET requests aren’t for HEAD one, which then permit to blindly simulate authorized GET requests
* Same goes for methods names that don’t exist
* The `TRACE` request, which simply returns back the request, can be used in XST \(_Cross-Site Tracing_\). If the attacker can force the browser to do this request, it will send cookies with it and so receive them back in the response, which can be read in js, bypassing the `HttpOnly` rule
* En général, accepter des méthodes HTTP non-nécessaire est plutôt une mauvaise chose et peut être abusée. \(Aka _HTTP Verb Tampering_\)

## HTTP Parameter pollution

* There’s no official protocol on how to deal with multiple get variables with the same name \(aka [http://test.com/?user=asd&user=xyz](http://test.com/?user=asd&user=xyz)\)
* Some parser will take the first one, some the last and others will concatenate all of them
* We can abuse this by overriding values, create a vicious payload by concatenating all of them, or taking advantage of parser differentials to pass different values to the different services.

Cette string sera interprétée ainsi par différents language :

\[img\]

## RIA Cross Domain Policy

Rich Internet Applications \(RIA\) use a `crossdomain.xml` or `clientaccesspolicy.xml` file to specifies the permissions that a web client such as Java, Adobe Flash, Adobe Reader, etc. uses to access data across different domains. There’s a master file at the root and maybe sub files in the subdirectory. Too permissive files will enable _CSRF_.

## Brute-Forcing Username

Les sites ne répondent pas toujours de la même façon face à un utilisateur inexistant ou un mdp invalide, ce qui permet d'énumérer les noms d'utilisateurs.

La différence peut être présente dans :

* La réponse textuelle
* La réponse de la requête
* Les pages de redirection
* Des paramètres de lien
* Les error code renvoyées
* Les titres de page 
* La réponse donnée par la page de récupération de message

## SSI Injection

_Server-Side Include_ permet aux serveurs web d’avoir un peu de code dynamique dans des pages html sans utiliser un langage complet.

Ce sont des directives que le serveur web parse avant de fournir la page finale.

Exemple \(liste non-exhaustive\):

* `<!--#echo var=”DATE_LOCAL”-->` Print l’heure
* `<!--#include virtual=”path”-->` Inclus le contenu du fichier
* `<!--#exec cmd=”ls”-->` Inclus le retour de la commande système spécifiée \(si la configuration le permet\)
* Des fichiers `.shtml` sous-entendent l'utilisation de _SSI_ \(mais l’extension n’est pas pas obligatoire, donc son absence ne signifie pas qu’elles ne le sont pas\).

Ces caractères sont utilisés dans des directives :

* `<` & `>`
* `#`
* `=`
* `/`
* `.`
* `“`
* `-`
* \[a-zA-Z0-9\]

S’ils ne sont pas sanitize, il est donc possible d’en abuser.

On peut aussi les utiliser dans les header HTTP si l’application les utilise pour générer dynamiquement des pages :

```text
GET / HTTP/1.0 
Referer: <!--#exec cmd=”/bin/ps ax”-->
User-Agent: <!--#include virtual=”/proc/version”-->
```

## Open redirect vulnerability

* Open redirects nous permettent de rediriger l'utilisateur vers n'importe quelle page
* It happens when a page redirect to a link given as an url parameter : abc.com/?url=xyz.com
* This can be used server-side, for example with php `header(“location: $var”)` or in flask/other framework’s `redirect()` function.
* As well as client side, with `window.location` in js and some meta tags in html
* It's one of the based concepts of phishing attacks
* Ca peut aussi être utilisé pour récupérer des cookies/etc si on fait en sorte que l'utilisateur atteigne notre site. [https://webhook.site/](https://webhook.site/) permet d'enregistrer les requêtes faite sur une adresse qu'il nous donne.
* Restricted redirect are on the other end restricted to only some subdomains
* Quand il y a un filtre vérifiant que l'adresse commence par "/", on peut par exemple mettre "//google.com", les navigateurs comprennent les "//".
* Don't forget that the whole 127.0.0.1/8 subnet is localhost, so if 127.0.0.1 is filtered, try that or localhost

## Web Messaging

En plus des CORS, il y a un autre moyen permettant à 2 domaines de communiquer : _Cross Domain Messaging_.

La messaging API introduit une fonction `postMessage()` contenant le message et le domaine de destination. De même, un event existe pour détecter la réception d'un message.

Exemple :

```javascript
// Send message :

iframe1.contentWindow.postMessage(“Hello world”,”http://
www.example.com”);

// Receive message :
window.addEventListener(“message”, handler, true);
function handler(event) {
    if(event.origin === ‘chat.example.com’) {
     /* process message (event.data) */
    } else {
     /* ignore messages from untrusted domains */
    }
}
```

Si l'origine est mal vérifiée ou si les dev sont trop confiant de la sécurité des données transmises, des failles peuvent survenir.

## Dom Clobbering

* Pour chaque élément ayant un id dans une page, une variable js correspondante est créée \(à part si elle existe déjà\)
* De plus, elle ne représente pas toujours la même chose en fonction du type de l'élément

## Dom Purify

* Il enlève les `<script>` mais aussi  des tags non terminés `<a`
* Par contre il accepte des tags entiers `<a>`, `<img>`, etc
* tel est l'un des rare protocoles accepté, donc `tel:alert(1337)` est accepté dans un href par exemple

## Click jacking

En intégrant une page dans une iframe et en la rendant transparente en CSS, on peut pousser l'utilisateur à cliquer sur des éléments sans qu'il ne le sache.

Client-Side on s'en protège avec le _frame busting_ mais j'ai pas tout compris et server-side avec `X-frame-options` qui définit qui peut mettre notre site dans une iframe.

## Path Injection

* En plus des path traversal/etc, si une partie de l'url se retrouve dans la page, des path _injection_ sont possible.

## Encoding

* Try to double url encode, aka '/' -&gt; `%252f` \(%25 = %, %2f = /\)

## LDAP vulnerabilities

* Faire un bind anonyme \(en ne mettant rien dans le body de la request\), peut permettre d'être authentifié \(d'autant plus si les droits d'accès sont mal configurés\)
* Authentication will often look like that : `(&(cn=[INPUT1])(userPassword=HASH[INPUT2]))`, to which we can give this payload `?name=admin)(userPassword=*))%00&password=*`. On ré-écrit le filtre nous même \(le `%00` permet d'empêcher le serveur de rajouter sa partie\)
* Tester la possibilité de casser des filtres avec `)`

## JSFuck

JSFuck is a valid javascritp syntax using only 6 characters : `[`, `]`, `(`, `)`, `+`, `!`. It can be use to bypass filters

## Multiple view of framework

Framework often automatically generate multiple view for a data. So an user.html may have an equivalent user.json format. However, since it's automatic and not necesseraly thought by the dev, some protection for sensible data might be missing on this format.

By the way, if you can't change the url by adding a `.json`, you can force it to fed you this type of responce by changing the accept header of the request to : `Accept : application/json`

## Server Side Template Injection

To test if that is possible, inject `{{4-3}}` if it's interpreted, you might be able to do somehting. This isn't specific to python flask's jinga 2 apparently, Server Side Template often use that.

### Jinga 2

To get it to execute something, you can naviguate the available class by iterating `{{.__class__.mro()[x]}}` until you stumble on one that might have multiple functions \(like _object_\).

You can then check it's subclasses with `{{.__class__.mro()[x].__subclasses__()}}` and search within it something useful \(like _popen_\).

You can finally use it with `{{.__class__.mro()[x].__subclasses__()[y](COMMAND)}}`. If it's subprocess.popen, there's some parameters to add in order to specify arguments of the command, etc, check the doc.

### Twig \(1.9.0\)

`{{_self.env.registerUndefinedFilterCallback('exec')}}{{_self.env.getFilter('uname')}}`

## Retrieve php source code

If you have a path traversal vul, you can do this `www.example.com?file=php://filter/read=convert.base64-encode/resource=page.php` \(or without the .php if it is added automatically\). This will base64 content before returning it. \(I'm guessing that if i don't base64 it, it won't return it because it's server code\)

## Serialization vuln

En php \(mais je suppose que ça fonctionne de la même façon sur d'autres langages\), on peut serialize un objet. ça le transforme en une string le représentant : `O:8:"siteuser":2:{s:8:"username";s:5:"admin";s:8:"password";s:3:"aaa"}` -&gt; Objet dont le nom fait 8 caractères contenant 2 attributs : des strings \(lire la doc pour la syntaxe complète\).

On peut aussi prendre une string du genre et la transformer en objet avec `unserialize`. **Doing this on unsanitized input can enable code execution** and other vuln.

## Particularité firefox

* Si le client reçoit des données depuis une vue json, en l'accédant via firefox, il va tenté de l'interprêter et la formatter, et son parseur eut buggé sur un payload alors qu'il fonctionne. **Utiliser burp pour analyser les réponses**
* Si on dl un ficher dans le navigateur, firefox mettra comme date de dernière modification la date du jour, tandis que si on le dl avec wget, on récup la vraie

## Tips &/ Tricks

* Keep in mind that even if you can't access an directory of a website \(ip/dev/ for example\), you might be able to access it's subdir/files \(ip/dev/backup for example\)
* when it ends with `==` it’s usually base64
* Resource : [https://repo.zenk-security.com/](https://repo.zenk-security.com/)
* MySQL's `LIKE` comparison isn't case sensitive
* MySQL's `=` comparison isn't case sensitive & doesn't care about trailing spaces
* To send yourself a ping back, you can do this on the victim `ping -c 1 ip` and launch `tcpdump -i tun0 -n icmp` on your machine
* A weird way to write a file is to `cat > fine_name << EoF`, then write your text, and write EoF at the end.
* Many web servers and application servers provide, in a default installation, sample applications and files that have vulnerabilities
* _HTTP Strict Transport Security_ \(HSTS\) is a request header specifying that all exchanged must be done over HTTPS.
* If `Cache-Control` is not  set to `must re-validate`, using the “back” button of the browser will show the previous page and it’s information, which may be sensitive if it was for a login for example. Other Cache-Control options will control how page are cached, and if wrongly set will enable finding sensitive information in the browser \(aka forced browsing\)
* Mobile website may differ from desktop website, and may have vulnerabilities not existent in the other version. Same for different browser version \(looking at you IE\). By using an interception proxy and modifying request, you can forge them/bypass checks used in the front end. De même pour des champs qui sont normalement cachés
* Processing times might give a hint to the attacker, for example if the error message takes longer to come for an invalid username + invalid password combo than for a valid username + invalid password one
* Test that some function can't be used more times than intended \(for example discount one\).
* **Session Fixation** : Si l'application ne fournit pas un nouvel id de session à chaque connexion il est possible de pousser la victime à utiliser un id dont l'attaqueur à la connaissance et donc permettre à celui-ci de se faire passer pour elle.

## Sources

* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)
* [LiveOverflow](https://www.youtube.com/channel/UClcE-kVhqyiHCcjYwcpfj9w/search?query=rce)
* [PwnFunction](https://www.youtube.com/channel/UCW6MNdOsqv2E9AjQkv9we7A/videos)
* [IppSec](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA)
* [Pentesterlab](https://pentesterlab.com/)
* Misc research

