# HTTP Vulnerabilities

## Parameter pollution

* There’s no official agreement on how to deal with multiple get variables with the same name \(aka `http://test.com/?user=asd&user=xyz)`
* Some parser will take the first one, some the last and others will concatenate all of them
* With that an attacker can : 
  * Override values
  * Create a vicious payload by concatenating all of them
  * Take advantage of parser differentials
* A good first place to look at is social media links

## Cross-site Tracing

* The `TRACE` request simply returns back the request
* If we can force the browser to do it, it'll send cookies with it and so receive them back in the response, which can be read in js, **bypassing the `HttpOnly` rule**

## Redirect

* If a server responds with content after a 302 \(which it shouldn't\) but then redirects \(which it should\), we can use a proxy to change the status code of the response and change it from a 302 to a 202 \(preventing it from redirecting\)

## Carriage return line feed injection

* `\n` is a carriage return
* `\r` is a line feed
* Those two are referred as CRLF
* Server & browser rely on them to identify the parts of an HTTP message
* Search for input that is used in return headers, especially if they're setting cookies
* Test by submitting `%0D%0A`

### Request smuggling

* An attacker can smuggle an HTTP request by exploiting a CRLF injection and appending the malicious request to a legitimate one
* This can result in cache poisoning, firewall evasion, request hijacking and http response splitting



## Method mismatch

{% hint style="info" %}
I'm not really sure how i wanted to abuse it but i wrote that : 

* Checks implemented for GET requests might not be for HEAD ones
* Same goes for methods names that don’t exist
{% endhint %}

## Verb Tampering

{% hint style="info" %}
When unnecessary HTTP methods are accepted, we can abuse it, but I haven't research how
{% endhint %}

## Sources

* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)
* _Real-World Bug Hunting: A Field Guide to Web Hacking_ by Peter Yaworski

