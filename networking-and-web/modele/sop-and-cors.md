# SOP & CORS

## SOP - Same Origin Policy

* The SOP **restrict a script from one origin to access resources of another origin**
  * It is enforced by **browsers**, with each their own implementation, but the idea stays the same
  * **The requests are still made**, the browsers just doesn't share the response
    * This is needed because there can be special CORS headers in the http response, that indicates to the browser what cross-origin exchange are permitted 
  * Obviously, attackers can still craft & send requests outside of a browser, but then they would not have access to the victim cookies \(that a browser might send along with a request\)
* 2 web pages have the same origin if they share the same **protocol** \(http vs https\), **host** & **port**
* **Only scripts are concerned**, so those are permitted :
  * **Embedded tags** with an `src` attribute \(or equivalent\) pointing to another origin
    * Embedded tags example : `<img>`, `<iframe>`, `<link>`, `<script>`, `<object>`
  * **Links**, **redirection** & **form submission** to another origin
  * SOP doesn't apply to them because it would restrict the web too much, however it makes them vulnerable to [CSRF](https://zcugni.gitbook.io/notes/pen-test/vulnerabilities/csrf)
  * There might be additional protections/restrictions depending on browsers
* For some websites features, the SOP is too restrictive, so the CORS was created
* Example situation :
  * The attacker creates a malicious site with a script sending a GET request to `https://reddit.com/message/inbox.json`
  * A user logged in to reddit visit the malicious site
  * The scripts send the request to reddit with the victim cookies attached
  * Without SOP, the browser would share the response to the GET request, giving the full list of messages to the attacker

## CORS - Cross-Origin Resource Sharing

The CORS are added **headers** in HTTP request & response that makes it possible to get around the SOP

### Preflight

* If a request is judged risky, it will trigger a _**preflight**_
  * An `OPTION` request describing the final request is sent to the server
  * Headers in the response indicates if the final request will be accepted or not
  * If it is, it is sent
* Requests following these conditions won't trigger a preflight :
  * No event listeners are registered on any `XMLHttpRequestUpload` object used in the request
    * These are accessed using the `XMLHttpRequest.upload property`
  * No `ReadableStream` object is used in the request
  * Some additional restrictions by browsers
  * Accepted methods : `GET`, `HEAD`, `POST`
  * Header that can be specified manually :
    * `Accept`
    * `Accept-Language`
    * `Content-Language`
    * `Content-Type`
    * `DPR`
    * `Downlink`
    * `Save-Data`
    * `Viewport-Width`
    * `Width`
  * Accepted values of `Content-Type` :
    * `application/x-www-form-urlencoded`
    * `multipart/form-data`
    * `text/plain`

### Headers

* Request :
  * `Origin`
  * `Access-Control-Request-Method` Used in preflight request to describe the method \(POST, GET, etc\) of the final request
  * `Access-Control-Request-Headers` Used in preflight request to list the name of the headers \(custom or not\) of the final request
* Response :
  * `Access-Control-Allow-Origin : * | <origin>` Authorize cross-origin request from one specific domain or any domain
  * `Access-Control-Allow-Credentials : true` ****
    * False if not specified
    * Allows request with cookies
    * `Access-Control-Allow-Origin` Must specifies a precise origin if set to `true`
  * `Access-Control-Allow-Headers`Used in response to a preflight request to indicate which HTTP headers can be used 
  * `Access-Control-Expose-Headers`Indicates which headers can be exposed in the response
  * `Access-Control-Max-Age`Indicates how long the results of a preflight request can be cached

## Sources

* MDN Web Docs
  * [On SOP](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)
  * [On CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)
* [Hackedu](https://www.hackedu.com/blog/same-origin-policy-and-cross-origin-resource-sharing-cors#:~:text=The%20same%2Dorigin%20policy%20is,when%20it%20was%20first%20created.)
* [Wikipedia](https://en.wikipedia.org/wiki/Same-origin_policy#:~:text=The%20concept%20of%20same%2Dorigin,Document%20Object%20Model%20%28DOM%29.)
* [LiveOverflow](https://www.youtube.com/watch?v=KaEj_qZgiKY)
* Misc research

