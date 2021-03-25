---
description: 'Disclaimer : This isn''t clean, don''t use it for now'
---

# Misc Attack

## SSRF - Server Side Request Forgery

* Make request in the name of the server, so with it's rights

## RCE

* I'm not sure if it stands for _**Remote Code Execution**_ or _**Remote Command Execution**_
* It allows us to **execute commands on the server**
* Language specific commands :
  * PHP : `system(<command>)`
  * Ruby : `command`
  * Python
    * `os.system(<command>)` and variants
    * Do that inside an `str()`
    * If you need to import `os.system`, use this function in `str()` : `__import__('os').system(...)`

## HTTP

### Parameter pollution

* There’s no official agreement on how to deal with multiple get variables with the same name \(aka `http://test.com/?user=asd&user=xyz)`
* Some parser will take the first one, some the last and others will concatenate all of them
* With that an attacker can : 
  * Override values
  * Create a vicious payload by concatenating all of them
  * Take advantage of parser differentials

### Cross-site Tracing

* The `TRACE` request simply returns back the request
* If we can force the browser to do it, it'll send cookies with it and so receive them back in the response, which can be read in js, **bypassing the `HttpOnly` rule**

### Redirect

* If a server responds with content after a 302 \(which it shouldn't\) but then redirects \(which it should\), we can use a proxy to change the status code of the response and change it from a 302 to a 202 \(preventing it from redirecting\)

### Method mismatch

{% hint style="info" %}
I'm not really sure how i wanted to abuse it but i wrote that : 

* Checks implemented for GET requests might not be for HEAD ones
* Same goes for methods names that don’t exist
{% endhint %}

### Verb Tampering

{% hint style="info" %}
When unnecessary HTTP methods are accepted, we can abuse it, but I haven't research how
{% endhint %}

## Brute-Forcing Username

* Website don't always respond the same way to a non-existent user and an incorrect password, we can use it to enumerate users
* Look for differences in :
  * HTTP response 
  * Text
  * Redirection
  * Link parameters
  * Error code
  * Page's title

## Open redirect

* Allows us to **redirect a user to any page**
* This happens when we can control the redirect instruction of a page, which can come from:
  * An url parameter : `https://abc.com/?url=xyz.com`
    * Look for `url=`, `redirect=`, `next=`, `page=`, ...
    * As well as single letter version : `u=`
  * JS's `window.location`
  * Html's `<meta>` tag
  * PHP's header : `header("location: $var")`
  * Server-side framework \(like flask's `redirect()`\)
* It's one of the based concepts of **phishing attacks**
* It can also be use to retrieve **cookies** if the target is our website
* **Restricted redirect** are restricted to only some subdomains
* Check [Bypassing Sanitizers](https://zcugni.gitbook.io/notes/pen-test/vulnerabilities#bypassing-sanitizers)

## Dom Clobbering

* For each element with an id in the page, a corresponding js var is created \(except if it already exist\)
  * It doesn't represent the same thing in function of the type of the element
* You can create artificial variable like this

{% hint style="info" %}
I forgot how to use that to our advantages
{% endhint %}

## Dom Purify

* Removes `<script>` and not terminated tags : `<a`
* `tel` is one of the few accepted protocols so `tel:alert(1337)` will work

## Unserialize

* If the app unserialize a string without checking it, you can abuse it

## Click jacking

* By integrating a page into an iframe and making in transparent, we can make the user click on element without him knowing
* To protect from that client side, use _**frame busting**_
* To protect from that server side, use `X-frame-options` which determine who can put our site into an iframe

## LDAP vulnerabilities

* We might be able to authenticate by doing an anonymous bind \(by putting nothing into the body of the request\)
* Example of payloads given this authentication : `(&(cn=[INPUT1])(userPassword=HASH[INPUT2]))` :
  * `?name=admin)(userPassword=*))%00&password=*`
* Try to break filters with `)`

## Multiple view of framework

* Framework often automatically generate multiple view for a data, so an `user.html` may have an equivalent `user.json` format
* However, since it's automatic some protection might be missing
* If you can't change the url by adding a `.json`, you can force it to fed you this type of response by changing the accept header of the request to : `Accept : application/json`

## Firefox particularities

* If the client receive data from a json view, if you access it via firefox it will try to interpret it and format it
  * However, the parser might fail when the application might not, giving you the impression that your payload does not work
  * **Use burp to analyze the response instead**
* If you download a file in the browser, firefox will set the modification date to today
  * Download it with `wget` if you want need the real one instead

## Kerberoast

* This is a vulnerability using Active Directory and Kerberos
* 2 aspects make this attack possible :
  * Kerberos users can ask for tickets for services they don't have the rights to use
  * AD services tickets are encrypted with the NTLM hash of the service account instead of a more random/secure option
* Because of all of that, we can ask for tickets and then crack the password of the service account

## Tips & Tricks

* When it ends with `==` it’s usually base64
* Resource : [https://repo.zenk-security.com/](https://repo.zenk-security.com/)
* A weird way to write a file is to `cat > file_name << EoF`, then write your text, and write EoF at the end.
* Many web servers and application servers provide, in a default installation, sample applications and files that have vulnerabilities
* If `Cache-Control` is not  set to `must re-validate`, using the “back” button of the browser will show the previous page and it’s information, which may be sensitive if it was for a login for example. 
  * Other Cache-Control options will control how pages are cached, and if wrongly set will enable finding sensitive information in the browser \(aka forced browsing\)
* Mobile website may differ from desktop website, and may have vulnerabilities not existent in the other version
  * Same for different browser version \(looking at you IE\)
  * By using an interception proxy and modifying request, you can forge them/bypass checks used in the front end
  * Same for hidden fields
* Processing times might give a hint to the attacker, for example if the error message takes longer to come for an invalid username + invalid password combo than for a valid username + invalid password one
* Test that some function can't be used more times than intended \(for example a discount one\)
* **Session Fixation** : If an application doesn't give a new session id to a user at each new connection, an attacker may force a victim to use a known id, making it possible to hijack the user session
* If a service with "Pi" in it's name is used, try to ssh with default Rasberry Pi Credentials \(Pi - Rasberry\)
* To enumerate user on wordpress, scripts use `url/?author=1`, then 2, etc because it redirects to the user name
* Linux's ping default ttl is of 64, while windows's is of 127

## Sources

* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)
* [LiveOverflow](https://www.youtube.com/channel/UClcE-kVhqyiHCcjYwcpfj9w/search?query=rce)
* [PwnFunction](https://www.youtube.com/channel/UCW6MNdOsqv2E9AjQkv9we7A/videos)
* [IppSec](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA)
* [Pentesterlab](https://pentesterlab.com/)
* For kerberoast : [geekeries.org](https://geekeries.org/2016/11/kerberoasting-active-directory-a-la-rotissoire/?cn-reloaded=1) \(in french\), [pentestlab.blog](https://pentestlab.blog/2018/06/12/kerberoast/), [scip.ch](%20https://www.scip.ch/en/?labs.20181011)
* _Real-World Bug Hunting: A Field Guide to Web Hacking_ by Peter Yaworski
* Misc research

