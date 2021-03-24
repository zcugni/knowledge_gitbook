# CSRF - Cross-Site Request Forgery

## Concept

* Cross-Site Request Forgery is an attack where you **execute action in the name of an authenticated user**
* For example :
  * A user is authenticated on application X
  * The user visit a malicious website
  * This website sends requests \(with the user cookies attached\) to the application X server without the user knowing
* To trigger the requests, the malicious website could use :
  * `<img>` or other embedded tags `src`
  * Auto-submitting forms
  * Scripts
* Because of the SOP & CORS, the browser **won't share the response** to requests made from another origin \(unless explicitly authorized too\)
  * However, **the request are still made**, so **GET requests** that trigger some action instead of only retrieving data are vulnerable 
  * To protect **POST request**, servers use **CSRF tokens** : an hidden input added to every form of the website
    * It's value is secret, unique & identifies the user
    * Any request that does not contain the token is rejected
    * As long as the attacker can't leak the token, this is a good defense

## Sources

* LiveOverflow's [video](https://www.youtube.com/watch?v=KaEj_qZgiKY)
* PwnFunction's [video](https://www.youtube.com/watch?v=eWEgUcHPle0)
* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)
* Misc research

