# CSRF - Cross-Site Request Forgery

For CSRF, the victim must go to the _attacker_ website which has a malicious image or form that will send a request in the name of the victim \(so with it's cookies\) to the target website

## GET attack

* Because of the SOP, if a server receive a get request from another origin, it won't show the response
* When using GET request correctly  \(to get data, not do action\), SOP & CORS are a good protection
  * If you send one from another origin, the server will receive it, but no show the result
* However, if the get request is used to delete/modify/etc data, this will still be executed by the server \(since it does receive the request\)
* For exemple, if this link [https://example.com/profile/delete](https://example.com/profile/delete) is embeded in an image, any user visiting our website will send a request to the target server with the cookies of the user, effectively deleting their account

## POST attack

* With POST, you need an auto-submit form \(through js\) with the target website as the action link
* To defend against that, we use **CSRF tokens** : an hidden input added to every form of the website
  * It's value is secret, unique and identifies the user
  * Any request that does not contain the token is rejected
  * As long as the attacker can't leak the token, this is a good defense

## Sources

* LiveOverflow's [video](https://www.youtube.com/watch?v=KaEj_qZgiKY)
* PwnFunction's [video](https://www.youtube.com/watch?v=eWEgUcHPle0)
* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)
* Misc research

