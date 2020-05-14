# URL & CO + URL Encoding

## URI, URL & URN

* Uniform Ressource Identifier \(_URI_\) : Permet d'identifier une ressource \(irl, correspond à un nom\).
* Uniform Ressource Locator \(_URL_\) : Permet de localiser une ressource \(irl, correspond à une adresse\).
* Uniform Ressource Name \(_URN_\) : Permet d'identifier une ressource dans l'espace et le temps de façon unique \(irl, un ISBN d'un livre\).
* Les URL & URN sont des URI, mais l'opposé n'est pas systématique.

## Anatomie d'une URL

![](../.gitbook/assets/url_anatomy.png)

* Le scheme peut aussi être `file://` auquel cas c'est le chemin vers une ressource locale
* Il peut y avoir plusieurs paramètres dans la _Query String_ séparés par des `&`
* Le terme _Query String_ semble couramment utilisé mais n'est, je crois, pas officiel
* On peut aussi spécifier l'utilisateur et le mot de passe avant le domaine : `https://username:password@example.com`
* Le _Fragment Identifier_ est en fait une ancre dans la page
* Elle peut être plus complexe que cela, mais c'est l'utilisation générale qu'on en fait

## URL Encoding

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

## Sources

* [W3C](https://www.w3schools.com/tags/ref_urlencode.ASP)
* Misc Research

