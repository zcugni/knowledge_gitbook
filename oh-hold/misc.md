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

### Jinga 2

* To get it to execute something, navigate the available class by iterating `{{.__class__.mro()[x]}}` until you stumble on one that might have multiple functions \(like _object_\)
* Check it's subclasses with `{{.__class__.mro()[x].__subclasses__()}}` and search for something useful within it something useful \(like `popen`\)
* Use it with `{{.__class__.mro()[x].__subclasses__()[y](COMMAND)}}`

## Serialization

* In php and other languages, we can serialize an object, which gives you a string describing it
  * For example`O:8:"siteuser":2:{s:8:"username";s:5:"admin";s:8:"password";s:3:"aaa"}` gives you the following info :
    * The name takes 8 chars
    * It has 2 attributes which are strings
    * etc
* Unserializing lets you create an object from such a string

## Misc

* `.jar` are archives files of java classes

