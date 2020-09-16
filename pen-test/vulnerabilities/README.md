# Vulnerabilities

## Injection

* Nearly all attacks are an injection of a form or another
* The goal is to get a vicious string interpreted by whatever technology is used
* Often, a payload will contain comment characters to comment out the genuine part of the code \(therefore replacing it by the vicious one\)
* Or the vicious part might close the original statement and add a new one afterwards

## SSRF - Server Side Request Forgery

* Make request in the name of the server, so with it's rights

## Types of attacks

* **Inband** : The result is directly visible \(sent or displayed\)
* **Out-of-band** : We must force the app to send the result from another way
  * For example by adding it as a cookie or parameter to a request made to our server
* **Blind \(oracle\)** : The result isn't retrievable, but the application reacts differently in function of it
  * With that, you can get an mdp char by char for example
  * Look for differences in : 
    * Wording
    * Response status code
    * Response time
* **Error based** : Extract the data via error messages

## Bypassing sanitizers

* Try to unicode encode \(`%2e` for `/` for example\) or double unicode encode \(`%c0%af` for `/` for example\)
* Add a null byte `%00` to terminate a string earlier
* Create a string without `"` :
  * eval regex : `eval(/alert(1337)/.source())`
  * tostring a number with a base : `17795081..toString(36)`
* Create a string from numbers : `eval(String.fromCharCode(97, 108, 101, 114, 116, 40, 49, 41))`
* Fragment a string : `eval("al"+"ert(1)")`
* Break out of html comment : 
  * If `<?php>` \(or `<?>`, it's shortcut\) are in the html response, browser will transform it to `<!--php-->` because server code shouldn't be on the client
  * However, nested comment aren't possible in html, so if we do that inside of an existing comment, it terminate it earlier
* https can be in all caps \(HTTPS\)
* Try doubling element or mixing them in strange ways
* If the sanizizers aren't recursive, you can bypass them by fragmenting the inputs

{% hint style="info" %}
Add the stuff that's in xss & rce \(checks others too\)
{% endhint %}

