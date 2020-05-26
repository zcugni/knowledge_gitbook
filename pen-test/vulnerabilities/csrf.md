# CSRF - Cross-Site Request Forgery

Pour ces 2 attaques, l’utilisateur doit passer sur _notre_  site, ayant soit une image ou un formulaire vicieux \(expliqué plus bas\) qui le feront charger des informations du site victime avec les cookies actuels de l’utilisateur.

## Attaque avec GET

Avec la SOP et les CORS, on peut envoyer depuis une autre origine une requête GET avec des cookies, et si le serveur ne l’accepte pas, nous ne verrons pas la réponse. Ce qui est une défense parfaite quand les requêtes GET sont utilisées de la bonne façon, c’est à dire uniquement pour lire/demander des données.

Si par contre, elles sont utilisées pour exécuter des actions \(aka delete, update, etc\), vu que la requête est réellement envoyée avec des cookies, l’action sera exécutée comme si la demande était légitime.

Par exemple :

Avec une url “[https://example.com/profile/delete](https://example.com/profile/delete)”, si on embed ça dans une image, tout utilisateur visitant le site \(et donc chargeant l’image et son lien\) visitera automatiquement cette page avec ces cookies \(car ils sont envoyés automatiquement avec la requête\) et supprimera donc accidentellement son compte.

## Attaque avec POST

Pour une attaque en passant par POST, il faut simplement créer un formulaire ayant comme action le lien cible et au moment de submit les cookies sont aussi envoyés avec la requête. On peut autosubmit la form avec du js.

La défense contre cela consiste à utiliser des CSRF tokens. Concrètement, cela consiste en un input caché ajouté à tous les formulaires du site contenant une valeur secrète et unique à cet utilisateur. Toute requête arrivant sans cette valeur ne sera pas prise en compte. Vu qu’avec la SOP les autres origines ne peuvent pas accéder aux données du site, ce token ne peut pas être récupéré par l’attaquant.

Par contre, si l’attaqueur trouve un moyen de le leak, c’est game over.

## Sources

* LiveOverflow's [video](https://www.youtube.com/watch?v=KaEj_qZgiKY)
* PwnFunction's [video](https://www.youtube.com/watch?v=eWEgUcHPle0)
* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)
* Misc research

