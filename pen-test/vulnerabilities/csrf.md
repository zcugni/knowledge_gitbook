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

## Mitigation

* For GET requests, they're safe as long as **they are not used to perform action, only retrieve data**
  * The reason behind this is that browsers **won't share the response** to requests made from another origin \(unless explicitly authorized too\) thanks to the [SOP & CORS](https://zcugni.gitbook.io/notes/networking-and-web/protocols/sop-and-cors)
  * However, **the request are still made**
* For POST request **CSRF tokens** & **CORS preflight requests** are used as protection
  * CSRF tokens are a **secret & unique value identifying the user added to every form** of the website
  * Any request that does not contain the token is **rejected**
  * As long as the attacker can't leak the token, this is a good defense
  * Those tokens might be named `X-CSRF-TOKEN`, `lia-token`, `form_id`, etc
* The `Referer` or `Origin` HTTP header can also be used because they're set by browser and can't be modified by attackers

## Tips

* If the HTTP header `Content-type` as any of these values, a [CORS ](https://zcugni.gitbook.io/notes/networking-and-web/protocols/sop-and-cors)**preflight won't be triggered** \(so the request will be directly sent\) :
  * `application/x-www-form-urlencoded` 
  * `multipart/form-data`
  * `text/plain`
  * This can be abused to perform POST requests that wouldn't be accepted otherwise

## Sources

* LiveOverflow's [video](https://www.youtube.com/watch?v=KaEj_qZgiKY)
* PwnFunction's [video](https://www.youtube.com/watch?v=eWEgUcHPle0)
* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)
* Misc research
* _Real-World Bug Hunting: A Field Guide to Web Hacking_ by Peter Yaworski

