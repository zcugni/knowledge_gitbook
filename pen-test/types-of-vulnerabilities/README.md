# Vulnerabilities



## Injection

* Nearly all attacks are an injection of a form or another.
* The goal is to get a vicious string interpreted by whatever technology is used.
* Often, a payload will contain comment characters to comment out the genuine part of the code \(therefore replacing it by the vicious one\)
* Or the vicious part might close the original statement and add a new one afterwards

## SSRF - Server Side Requestion Forgery

* Make request in the name of the server, so with it's rights.

## Types of attacks

* **Inband** : Le résultat nous est directement renvoyé/affiché
* **Out-of-band** : On doit forcer l'app a nous faire parvenir le résultat autrement \(par exemple via une requête vers notre server contenant les données\)
* **Blind \(oracle\)** : Le résultat ne nous est pas directement retourné, mais l'application réagit différement en fonction de celui-ci. 
  * Ca nous permet par exemple de trouver un mdp caractère par caratère. 
  * Look for : 
    * Difference in wording
    * Difference in response status code
    * Time-based difference
* **Error based** : On fait ressortir les données qu'on veut via les messages d'erreurs

## Bypassing sanitizers

* Try to unicode encode \(`%2e` for `/` for example\) or double unicode encode \(`%c0%af` for `/` for example\)
* Certain dev sanitize aussi à la main les chaines, et cela peut souvent être bypass en doublant des éléments/les mélangeant de façon étrange
* Si les sanitizer ne sont pas récursif, on peut aussi les bypass en fragmentant nos inputs.
* Adding null byte `%00` to terminate a string earlier

