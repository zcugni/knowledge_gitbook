# Layer 5 - Application

## Généralités

Le modèle OSI sépare cette couche en 3 \(session, présentation & application\), mais dans les faits ils sont souvent mélangés, comme dans le modèle TCP/IP.

Cela peut être HTTP, LDAP, SMTP, etc.

## HTTP

### Request

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

* HTTP Verb :
  * `GET` Demande une ressource, prends des paramètres avec ?key=value après le lien
  * `POST` Submit form data \(celle-ci sont dans le message body\)
  * `HEAD` Demande uniquement les en-têtes d'une réponse et non la réponse au complet
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
* `Referer` Last page visited \(i think\)

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
  * 200 Ok
  * 301 Moved Permanently
  * 302 Found \(temporarily moved\)
  * 400 Bad Request
  * 401 Unauthorized
  * 403 Forbidden
  * 404 Not Found
  * 405 Method Not Allowed
  * 408 Request Time-Out
  * 500 Internal Server Error
  * 501 Method Not Implemented
* `Date`
* `Cache-Control` Cache policy
* `Content-type` Describe the format
* `Content-Encoding` Describe the encoding \(the compression method\)
* `Server` \(Optionnal\) Header of the server generating the response
* `Content-Length` Lenght of the message body in bytes

### HTTPS

* Stands for HTTP Secure, it's run through over SSL/TLS

## Cookies

* Set par le serveur via une en-tête `Set-Cookie` dans la réponse :

  `Set-Cookie: name=value; expires=Mon, 23-Mar-2020 20:35:24 GMT; Max-Age=7200; path=/; secure`

* Ils peuvent avoir tout ces attributs :
  * `Secure` Ne sera transmis que pour des requêtes https
  * `HttpOnly` Ne peut pas être récupéré par du js \(pas supporté par tous les navigateurs\)
  * `Domain` Définit le domaine pour lequel le cookie sera envoyé. Par défaut le _host name_ du serveur générant le cookie. Il est transmis pour le domaine et ces sous-domaines \(ex: s’il est set à app.mydomain.com, il sera aussi transmis pour plop.app.mydomain.com, mais pas pour tut.mydomain.com\).
  * `Path` Définit le chemin plus précis \(dans le serveur web\) pour lequel le cookie sera envoyé \(ex : /myapp/\).
  * `expires` Par défaut, les cookies ne sont valide que pour la session active du navigateur et sont supprimé à la fin de celle-ci \(j’imagine quand on ferme le navigateur ?\). Avec ce flag, on peut définir une date précise d’expiration pour les rendre plus persistant.
* Les cookies ne sont envoyés qu'au domain/chemin correspondant quand ils ne sont pas expirés et que la situation corresponds à leur flag

## Session

* Session keeps the state & information of the client server-side.
* Ca permet d'éviter tout les échanges de données dû au cookies.
* When you logging \(for exemple\), your're only given a session id cookie, which is subsequently used to retrieve your info \(instead of having a cookie for each info\).
* \(Session id can also be sent via GET parameters\)

## URL

### Terminologie

* Uniform Ressource Identifier \(_URI_\) : Permet d'identifier une ressource \(irl, correspond à un nom\).
* Uniform Ressource Locator \(_URL_\) : Permet de localiser une ressource \(irl, correspond à une adresse\).
* Uniform Ressource Name \(_URN_\) : Permet d'identifier une ressource dans l'espace et le temps de façon unique \(irl, un ISBN d'un livre\).
* Les URL & URN sont des URI, mais l'opposé n'est pas systématique.

### Anatomie

![](../../.gitbook/assets/url_anatomy.png)

* Le scheme peut aussi être `file://` auquel cas c'est le chemin vers une ressource locale
* Il peut y avoir plusieurs paramètres dans la _Query String_ séparés par des `&`
* Le terme _Query String_ semble couramment utilisé mais n'est, je crois, pas officiel
* On peut aussi spécifier l'utilisateur et le mot de passe avant le domaine : `https://username:password@example.com`
* Le _Fragment Identifier_ est en fait une ancre dans la page
* Elle peut être plus complexe que cela, mais c'est l'utilisation générale qu'on en fait

### Encoding

Etant donné que certains caractères ont une signification spéciale pour les URL, ils sont réservés et si l'on veut les utiliser hors de ce contexte il faut les encoder. De plus, l'espace n'est pas permis et est remplacé soit par un `+` soit par `%25`.

Je ne suis pas sûre de la raison dernière chaque caractère réservé.

| Caractère | Encodage | Signification |
| :--- | :--- | :--- |
|  | %20 | Espace |
| ! | %21 | ? |
| \# | %23 | Delimits the fragment identifier |
| $ | %24 | ? |
| % | %25 | Encode string |
| & | %26 | Delimits query string's parameters |
| ' | %27 | ? |
| \( | %28 | ? |
| \) | %29 | ? |
| \* | %2A | ? |
| + | %2B | Utilisé pour encoder un espace |
| , | %2C | Alternative à ; |
| / | %2F | Delimits path segment |
| : | %3A | Delimits the scheme from the host and the host from the port |
| ; | %3B | I'm not sure |
| = | %3D | Delimits name/value pairs in the query string |
| ? | %3F | Delimits the start of the query string |
| @ | %40 | Delimits user info from the host |
| \[ | %5B | ? |
| \] | %5D | ? |

## SOP - Same Origin Policy

> La same-origin policy restreint la manière dont un document ou un script chargé depuis une origine peut interagir avec une autre ressource chargée depuis une autre origine.

2 pages ont la même origines si elles utilisent le même protocole \(http vs https\), le même hote et le même port.

Types d’échanges :

* Ecriture - globalement autorisée
  * Lien
  * Redirection
  * Formulaire
* Embarqués - en partie autorisé
  * `<script src=”..”></script>` \(les messages d’erreurs de syntaxe ne sont dispo que pour une même origine par contre\)
  * `<link rel=”stylesheet” href=”...”>` \(mais ils doivent avoir un header `content-type`. Restrictions en fonction des navigateurs\)
  * `<img>`, `<video>`, `<audio>`
  * Plugins avec `<object>`, `<embed>`, `<applet>`
  * `@font-face` en fonction des navigateurs
  * `<frame>` & `<iframe>` \(peut être interdit avec un header\)

## CORS - Cross-Origin Resource Sharing

Permet au serveur d’autoriser certains échanges ne suivant pas la SOP grâce à des headers qu’il rajoute dans sa réponse.

### Type d’headers \(dans la réponse du serveur\)

* `Access-Control-Allow-Origin : * | <origin>` : autorise des requêtes cross-origine venant de n’importe quel domaine ou d’un domaine spécifique.
* `Access-Control-Allow-Credentials : true (false if not specified)` : permet de faire des requêtes avec des cookies.
  * Si ce header n’est pas présent dans la réponse du serveur, une requête avec cookie sera rejetée par le navigateur. 
  * Pour que ce header soit prit en compte, `Access-Control-Allow-Origin` doit obligatoirement spécifié une origine précise.
* Plus toutes celles de réponse à un preflight, voir plus bas.

### Requêtes “normales”

Les requêtes respectant ces conditions ne triggereront pas de preflight.

* No event listeners are registered on any _XMLHttpRequestUpload_ object used in the request. These are accessed using the `XMLHttpRequest.upload property`.
* No `ReadableStream` object is used in the request.
* Some additional restrictions by browsers.

Méthodes acceptées :

* GET
* HEAD
* POST

Headers pouvant être spécifiés manuellement : 

* Accept
* Accept-Language
* Content-Language
* Content-Type
* DPR
* Downlink
* Save-Data
* Viewport-Width
* Width

Valeurs de Content-Type autorisées :

* application/x-www-form-urlencoded
* multipart/form-data
* text/plain

### Preflight

Les autres requêtes sont jugées risquées et donc trigger un “preflight”. Cela consiste à d’abord envoyer une requête `OPTION` au serveur décrivant la requête finale que l’on compte faire pour vérifier qu’il l’accepte et si c’est le cas, la faire réellement.

header d’une requête `OPTION` :

* `Origin`
* `Access-Control-Request-Method` POST, GET, etc
* `Access-Control-Request-Headers` Name of the used headers, custom or not

header d’une réponse `OPTION` :

* `Access-Control-Allow-Origin`
* `Access-Control-Allow-Methods` POST, GET, etc
* `Access-Control-Allow-Headers` Name of the headers, custom or not
* `Access-Control-Max-Age` Lifetime of the option request, a preflight will need to be trigger again if it’s exceeded

Ces headers sont présents dans la requête du client pour décrire sa futur requête et dans la réponse du serveur pour décrire ce qu’il accepte.

![](../../.gitbook/assets/headers.png)

## Sources

En plus de celle données [ici](https://zcugni.gitbook.io/notes/networking-and-web/modele#sources) :

* MDN Web Docs
  * [On SOP](%20https://developer.mozilla.org/fr/docs/Web/Security/Same_origin_policy_for_JavaScript)
  * [On CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)
* [W3C](https://www.w3schools.com/tags/ref_urlencode.ASP)

