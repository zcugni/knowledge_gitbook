# Pen test related theory

## Parser Differentials

De **nombreux** bugs viennent de différence de comportement entre des parsers pour une même string :

* In client vs in server
* In js context vs no js context \(browser vs template with dom purify for example. `noscript` would react differently in those two\). \(Obviously already solved, just an example\)
* html parser \(because `<div>` context\) vs js parser \(because `<script>` context\)
* etc..

C’est notamment un problème récurrents dans les “urls parsing” \(même les librairies de base de python ne sont pas toujours d’accord sur l'interprétation par exemple\).

C’est aussi un facilitateur des mutation xss \(ou l’idée en elle-même ?\).

## Inband, Out-of-band & error based

Certains genre d'attaque se retrouvent un peu partout :

* Inband : Le résultat nous est directement renvoyé
* Out-of-band/blind : On doit forcer l'app à nous fait parvenir le résultat autrement \(par exemple via une requête contenant les données vers notre serveur\)
* Error based : On fait ressortir les données qu'on veut via les messages d'erreurs

## Bypassing sanitizers

1. Assez couramment, seul certains encodages sont vérifiés donc certains de ces cas pourraient par exemple passés :

Pour `../` :

* %2e%2e%2f 
* %2e%2e/ 
* ..%2f
* ..%c0%af

Pour `../` :

* %2e%2e%5c
* %2e%2e\
* ..%5c
* %252e%252e%255c
* ..%255c
* ..%c1%9c
* Certain dev sanitize aussi à la main les chaines, et cela peut souvent être bypass en doublant des éléments/les mélangeant de façon étrange.

Par exemple ces tests :

```text
filename = Request.QueryString(“file”); 
Replace(filename, “/”,”\”);
Replace(filename, “..\”,””);
```

Aurait cette vulnérabilité :

```text
file=....//....//boot.ini 
file=....\\....\\boot.ini 
file= ..\..\boot.ini
```

1. Si les sanitizer ne sont pas récursif, on peut aussi les bypass en fragmentant nos inputs :

\[ajouter un example\]

1. Adding null byte `%00` to terminate a string earlier

## Local Storage

Aussi appelé _Web Storage_ ou _Offline Storage_ permet de stocker des données en pair de clé-valeur sur le client \(liées à un domaine en particulier, il suit la SOP\). La capacité de stockage est plus grande que celle des cookies et ces données-ci ne sont pas envoyées au serveur.

Le _localStorage_ est persistant tandis que le _sessionStorage_ est vidé à la fermeture de la fenêtre. Les deux sont accessible en js \(via `setItem` et `getItem`, avec les risques que cela sous-entends.

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

## Sources

* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)
* Misc research

