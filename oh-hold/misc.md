# Misc

## RIA Cross Domain Policy

Rich Internet Applications \(RIA\) use a `crossdomain.xml` or `clientaccesspolicy.xml` file to specifies the permissions that a web client such as Java, Adobe Flash, Adobe Reader, etc. uses to access data across different domains. There’s a master file at the root and maybe sub files in the subdirectory. Too permissive files will enable _CSRF_.

## Server-Side Include

* Allows servers to have some dynamic code in html pages without using a full blown language
* The server parse the instruction before giving the final page
* Example : 
  * `<!--#echo var=”DATE_LOCAL”-->` Print the hour
  * `<!--#include virtual=”path”-->` Include the content of a page
  * `<!--#exec cmd=”ls”-->` Execute the cmd \(if the config allows it\)
* The `.shtml` extension indicates that Server Side Include are used but it's optional

## Tools

*  [https://webhook.site/](https://webhook.site/) permet d'enregistrer les requêtes faite sur une adresse qu'il nous donne.

## Web Messaging

En plus des CORS, il y a un autre moyen permettant à 2 domaines de communiquer : _Cross Domain Messaging_.

La messaging API introduit une fonction `postMessage()` contenant le message et le domaine de destination. De même, un event existe pour détecter la réception d'un message.

Exemple :

```javascript
// Send message :

iframe1.contentWindow.postMessage(“Hello world”,”http://
www.example.com”);

// Receive message :
window.addEventListener(“message”, handler, true);
function handler(event) {
    if(event.origin === ‘chat.example.com’) {
     /* process message (event.data) */
    } else {
     /* ignore messages from untrusted domains */
    }
}
```

Si l'origine est mal vérifiée ou si les dev sont trop confiant de la sécurité des données transmises, des failles peuvent survenir.

