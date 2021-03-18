# Layer 5 - Application

## Generalities

* The OSI model divides this layer in 3 \(session, presentation & application\) but in reality they're often mixed, like in the TCP/IP model
* Some tech of this layer are : HTTP, LDAP, SMTP, etc.

## HTTP - Hypertext Transfer Protocol

* Used to fetch resources, such as HTML documents
  * A document is reconstructed from the different element fetched \(text, css, media, scripts, etc\)
* Essential for any data exchange on the Web
* Characteristic :
  * Client-Server : the communication is initiated by the client \(usually a browser\)
    * They communicate via messages, called **request \(**from the client\) & **response** \(from the server\)
  * [Stateless](https://zcugni.gitbook.io/notes/theory/terminologie-misc#types-of-communication-protocols) \(The usage of cookies let us use it as a stateful protocol\)
  * Human readable \(before HTTP/2\)
  * Used over TCP \(however in reality it only needs its transport protocol to be reliable, not necessarily connection-oriented\)
    * Experiments are made to develop a transport protocol better suited for HTTP
  * Extensible
* There's 3 versions of this protocol \(HTTP/1.0, HTTP/1.1 & HTTP/2\)
  * The main difference between HTTP/1.0 & HTTP/1.1 is that the new version can use a connection for more than one request/response pair
  *  HTTP messages, as defined in HTTP/1.1 and earlier, are human-readable. In HTTP/2, these messages are embedded into a binary structure, a _frame_, allowing optimizations like compression of headers and multiplexing. Even if only part of the original HTTP message is sent in this version of HTTP, the semantics of each message is unchanged and the client reconstitutes \(virtually\) the original HTTP/1.1 request. It is therefore useful to comprehend HTTP/2 messages in the HTTP/1.1 format.

### Request

Format :

```text
<VERB | NOUN> <path> <version>
[Optional Headers ...]

[Optionnal body]
```

Example :

```text
GET /api/stats/overview HTTP/1.1
Host: www.hackthebox.eu
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:73.0) Gecko/20100101 Firefox/73.0
Accept: application/json, text/javascript, */*; q=0.01
Accept-Language: fr,fr-FR;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
X-Requested-With: XMLHttpRequest
Connection: close
Referer: https://www.hackthebox.eu/
Cookie:  hackthebox_session=eyJpdiI6IkJBTlJ0bzNRVGowUDVrOUZNejl2K3c9PSIsInZhbHVlIjoiNlNUOUlUTlkwXC9GWEhpWXhpQzhmSUtHczFvWDlLYTYyMk5Tc0RNTDBFcm8rdjA2YmJwdDF5blwvSkl5OFB6emxVIiwibWFjIjoiMTY1NDNjMDI0NzhkOTI4ZDVlNzE2Y2MwOTYwZGE5NzZhOTIxNzQ3NTQ3ODk3YjNkNmJmMjA2ZWZhMjdmZjQ5ZCJ9
```

* HTTP Verbs & Nouns :
  * `GET` Ask for a resource by adding `key=value` parameters after an `?` in a link
  * `POST` Submit form data \(they're in the message body\)
  * `HEAD` Ask for the the headers that would be given if the request was a GET
  * `PUT` Upload a file to the server
  * `DELETE` Remove a file from the server
  * `OPTIONS` Query the server for accepted Verbs
  * `TRACE`
* Path
* Protocol Version
* `Host` Domain of the requested ressource
  * The behavior of the serveur might change depending on that field even if it points to the same ressource \(for example, using the ip address instead of the hostname\)
* `User-Agent` Client software issuing the request
* `Accept` Expected format of the response
* `Accept-Language` Expected \(human\) language of the response
* `Accept-Encoding` Expected encoding \(aka compression method\)
* `Connection` What to do with it, with `keep-alive` it will be re-use next time \(i think\)
* `Referer` Last page visited

### Response

```text
HTTP/1.1 200 OK
Date: Mon, 23 Mar 2020 18:35:27 GMT
Content-Type: application/json
Connection: close
Vary: Accept-Encoding
Cache-Control: no-cache, private
X-Frame-Options: SAMEORIGIN
X-XSS-Protection: 1; mode=block
X-Content-Type-Options: nosniff
CF-Cache-Status: DYNAMIC
Expect-CT: max-age=604800, report-uri="https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct"
Strict-Transport-Security: max-age=0; includeSubDomains
Server: cloudflare
CF-RAY: 578a4096ab09c795-AMS
Content-Length: 48

{"users":"286k","machines":154,"challenges":111}
```

* Protocol version
* Status
  * 1xx : Information
  * 2xx : Successful
    * 200 Ok
  * 3xx : Redirection
    * 301 Moved Permanently
    * 302 Found \(temporarily moved\)
  * 4xx : Client Error
    * 400 Bad Request
    * 401 Unauthorized
    * 403 Forbidden
    * 404 Not Found
    * 405 Method Not Allowed
    * 408 Request Time-Out
  * 5xx : Server Error
    * 500 Internal Server Error
    * 501 Method Not Implemented
* `Date`
* `Cache-Control` Cache policy
* `Content-type` Describe the format
* `Content-Encoding` Describe the encoding \(the compression method\)
* `Server` \(Optional\) Header of the server generating the response
* `Content-Length` Length of the message body in bytes

### HTTPS

* Stands for HTTP Secure, it's runs over SSL/TLS

## Cookies

* Set by the server via the `Set-Cookie` header in the response :

  `Set-Cookie: name=value; expires=Mon, 23-Mar-2020 20:35:24 GMT; Max-Age=7200; path=/; secure`

* Available attributes :
  * `Secure` Will only be transmitted with https request
  * `HttpOnly` Can't be retrieved in js \(not supported by every browser\)
  * `Domain` Define the domain for which the cookies will be sent
    * By default, it's the host name of the server generating the cookie
    * Transmitted for the domain and it's sub-domains
  * `Path` Define the precise path for which the cookies is sent
  * `expires` By default, cookies are only valid for the current session of the browser and are deleted when it closes \(i think\)
    * With this flag, you can define an expiration date

## Session

* Session keeps the state & information of the client server-side
* This allows you to avoids transmitting all data through cookies
* When you login \(for example\), your're only given a session id cookie, which is subsequently used to retrieve your info \(instead of having a cookie for each info\)
* Session id can also be sent via GET parameters

## Local Storage

* Also called **Web Storage** or **Offline Storage**
* Let you store data as key-value pair on the client
  * Linked to a particular domain, it follows SOP
* The storage capacity is bigger than that of cookies, and data aren't sent to the server
* The `localStorage` is persistent, while the `sessionStorage` is emptied when you close a window
  * Both are accessible in js via `setItem` & `getItem`

## URL

### Terminology

* **Uniform Resource Identifier** _\(URI\)_ Identifies a resource \(like a name\)
* **Uniform Resource Locator** _\(URL\)_ Locate a resource \(like an address\)
* **Uniform Resource Name** _\(URN\)_ Identifies a resource uniquely' \(like the ISBN of a book\)
* URL & URN are URIs, but the opposite isn't always true

### Anatomy

![](../../.gitbook/assets/url_anatomy.png)

* If the scheme is `file://` it reference a local resource
* There can be multiple parameters in the **Query String**, separated by `&`
* You can specify the username & password before the domain :`https://username:password@example.com`
* The **Fragment Identifier** is a page anchor

### Encoding

* Some chars have special meaning and needs to be encoded to be use normally 

| Char | Encoding | Signification |
| :--- | :--- | :--- |
|  | `%20` or `+` | Space |
| `!` | `%21` |  |
| `#` | `%23` | Delimits the fragment identifier |
| `$` | `%24` |  |
| `%` | `%25` | Encode string |
| `&` | `%26` | Delimits query string's parameters |
| `'` | `%27` |  |
| `(` | `%28` |  |
| `)` | `%29` |  |
| `*` | `%2A` |  |
| `+` | `%2B` | Use to encode a space |
| `,` | `%2C` | Alternative to `;` |
| `/` | `%2F` | Delimits path segment |
| `:` | `%3A` | Delimits the scheme from the host and the host from the port |
| `;` | `%3B` |  |
| `=` | `%3D` | Delimits key-value pairs in the query string |
| `?` | `%3F` | Delimits the start of the query string |
| `@` | `%40` | Delimits user info from the host |
| `[` | `%5B` |  |
| `]` | `%5D` |  |

## SOP - Same Origin Policy

* The SOP restrict how an origin can access or interact with a resource from another origin
* It's goal is to help protect against CSRF
* 2 pages have the same **origin** if they share :
  * The same **protocol** \(http vs https\)
  * The same **host**
  * The same **port**
* Type of exchanges :
  * Link, redirection and form submissions are generally authorized
    * 
  * Authorized only in part :
    * `<script src=”..”></script>` Error message are visible only to the same origin
    * `<link rel=”stylesheet” href=”...”>` The `content-type` header must be present and there's restrictions depending on the browser
    * `<img>`, `<video>`, `<audio>`
    * Plugins with `<object>`, `<embed>`, `<applet>`
    * `@font-face` Depending of the browsers
    * `<frame>` & `<iframe>` Can be forbidden by an header
    * Not authorized : Read \(you won't see the result of a GET response\)

## CORS - Cross-Origin Resource Sharing

Can authorize some exchanges that don't follow SOP restriction by adding headers in the response

### Type of headers \(in the response\)

* `Access-Control-Allow-Origin : * | <origin>` Authorize cross-origin request from one specific domain or any domain
* `Access-Control-Allow-Credentials : true` ****
  * False if not specified
  * Allows request with cookies
  * `Access-Control-Allow-Origin` Must specifies a precise origin
* All the ones in a preflight response, see below

### Normal request

Request following these conditions won't trigger a preflight :

* No event listeners are registered on any _XMLHttpRequestUpload_ object used in the request
  * These are accessed using the `XMLHttpRequest.upload property`
* No `ReadableStream` object is used in the request
* Some additional restrictions by browsers
* Accepted methods :
  * GET
  * HEAD
  * POST
* Header that can be specified manually :
  * Accept
  * Accept-Language
  * Content-Language
  * Content-Type
  * DPR
  * Downlink
  * Save-Data
  * Viewport-Width
  * Width
* Accepted values of `Content-Type` :
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

### Preflight

* Other request are judged risky and will trigger a preflight
* For that, an `OPTION` request is first made to the server describing the final one that we want to do
  * The server send back a response with headers describing what it accepts
* Header of an OPTION request : 
  * `Origin`
  * `Access-Control-Request-Method` POST, GET, etc
  * `Access-Control-Request-Headers` Name of the used headers, custom or not
* Header of a response to an OPTION :
  * `Access-Control-Allow-Origin`
  * `Access-Control-Allow-Methods` POST, GET, etc
  * `Access-Control-Allow-Headers` Name of the headers, custom or not
  * `Access-Control-Max-Age` Lifetime of the option request, a preflight will need to be trigger again if it’s exceeded

![](../../.gitbook/assets/headers.png)

## Sources

* MDN Web Docs
  * [On SOP](%20https://developer.mozilla.org/fr/docs/Web/Security/Same_origin_policy_for_JavaScript)
  * [On CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)
* Url encoding : [W3C](https://www.w3schools.com/tags/ref_urlencode.ASP)

