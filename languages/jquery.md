---
description: 'Disclaimer : This is an old note not up to current standard'
---

# Jquery

## Génétalités

* Les docs jquery & jquery UI sont très complète
* Pour rajouter Jquery à une page web, le mieux est d’utiliser Jquery CDN

## Syntaxe

* Variable : `var nomVar` \(voir `let` en en js\)
* Objet jquery : `$var` ou  `$(...)` Les parenthèses peuvent contenir ce genre d'éléments : 
  * `document`
  * Classes `.plop` ou plusieurs ainsi : `.plop, .plip`
  * Id `#plop`
  * `this`
  * Balises `p`
* Pour les méthodes qui s’appliquent à un objet jquery, on fait `$.methode_name`

```javascript
$(document).ready(function() {
    $('.vanish').click(function() {
        fadeOut();
    });
});
```

## Gestions des événements

Simplement `$.event_name` ou :

### $.on :

Le handler est trigger pour chaque événement de ce type survenant sur l'élément bindé et ces descendant. \(Aka, un click un élément d’une div trigger le .on\(click\) de la div.

* `$.on( events [, selector ] [, data ], handler )`
* `$` Représente l’objet Jquery sur lequel survient l’événement. Il doit déjà exister au moment où le `.on` est appelé \(voir le paramètre selector pour la déléguation\).
* `events` Le\(s\) nom\(s\) de\(s\) l’événement\(s\) concerné\(s\) \(exemple : “click”\)
* `handler` Le nom de la fonction à appeler suite à cet événement ou une fonction anonyme.
* `selector` L’enfant du `$` sur lequel l’événement est réellement appliqué. Ceci permet de déléguer un événement
* `data` Données à transmettre accessible ensuite depuis `event.data`
* Exemple avec nom de fonction :

```javascript
$(“dot”).on("mouseenter", selectDot);
```

* Exemple avec fonction anonyme :

```javascript
$(“dot”).on(“mouseenter”, function(event){
    event.preventDefault();
    alert(“Hello world”);
});
```

* Exemple avec selector:

```javascript
$(“main”).on("mouseenter", ".dot", selectDot);
```

* Exemple avec data:

```javascript
$(“main”).on("mouseenter", ".dot", “nb : 12”, selectDot);
// Dans selectDot :
alert(event.data.nb);
```

### Performances

Déléguer peut parfois aider au performance, je vais juste mettre un screen de la doc qui l’explique très bien :

Mais par contre, si on délègue à un niveau trop haut, comme par exemple le body, l’événement sera trigger en continu et ça risque de couter en performance. Il vaut donc mieux déléguer très près de la cible si on doit le faire.

## Appel ajax

* Ajax sert à envoyer des requêtes de façon asynchrone et à insérer le résultat dans la page sans la recharger.
* En js il faut instancier une classe XmlHttpRequest
* Jquery fournit des fonctions plus simple
  * `$.ajax()`
  * `$.get()` Raccourci de la première
  * `$.post()` Same

```javascript
$("#formulaire").submit(function(e){ 
    e.preventDefault(); 
    $.ajax({
       url : 'url_page_traitant_info',
       type : 'POST',
       data : $(this).serialize();
       dataType : 'html',
       success : function(donnéesReçuesParLajax, statut){
            //code
       },
       error : function(resultat, statut, erreur){
            //code
       },
       complete : function(resultat, statut){
            //code
       }
    });
});
```

* `data` contient les données à envoyer en tant que string. Facultatif pour get. `serialize()` permet d’automatiquement concaténer les données d'un formulaire.
* `type` Type de requête \(`GET` par défaut\)
* `dataType` Type de retour \(html, xml, json, text\)
* Pour `error` et `complete`, le résultat est un objet XHR

