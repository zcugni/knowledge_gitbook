# Good Practices

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

