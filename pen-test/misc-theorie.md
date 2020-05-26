# Theory

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

## Logging good practice

* Should not contains sensitive information 
  * Debug information, stack traces, application source code
  * Usernames
  * System component names, internal IP addresses
  * Session identification values, access tokens
  * Sensitive personal data & PII
  * Authentication passwords
  * Database connection strings
  * Encryption keys
  * Data of a higher security classification than the logging system is allowed to store
  * Commercially-sensitive information
  * Information it is illegal to collect in the relevant jurisdiction
  * Information a user has opted out of collection, or not consented to e.g. use of do not track, or where consent to collect has expired
  * ...
* Not in a dedicated server \(so could be erased/modified by an attacker that gains access to the main server\)
* Can be used for DDOS \(by filling the disk with logs\) -&gt; growth of logs should be monitored and stored in it’s own partition
* Logs are rotated after a set amount of time and when doing so compressed and the permissions updated to be stricter. If logs are automatically rotated after a certain size, it it must be check that an attacker cannot force the behavior to hide his actions.

## Good practice to mitigate brute-forcing of account/password

* Captcha helps but isn’t a solution by itself
* Instaurer a lock-out of account after 3-5 try
* Instaurer a waiting period of 5-30min before the user can unlock it’s account
* Unlocking by admin is the most secure but cumbersome solution \(also enable DDOS by locking all the known account\). The admin also should have a way to unlock it’s own account.
* Unlocking via a self-service mechanism \(must be secure\)

## Loggout Good practice

XSS et CSRF nécessitent qu’un utilisateur soit déjà authentifié sur l’application. Simplifier le logout ou le rendre automatique après un certain temps d’inactivité aide donc à restreindre ces attaques.

En dehors de l’accessibilité, etc, il faut tester si après un logout, les cookies fournis à l’authentification sont encore accepté et si l’accès à toutes les pages critiques est bien supprimé.

## History of big exploit

### Heartbleed

### Mirai

## Sources

* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)
* Misc research

