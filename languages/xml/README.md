# XML

## Généralités

* Abréviation de eXtensible Markup Language
* Conçu pour le transport et le stockage des données
* Décrit ce que _sont_ les données \(tandis qu’HTML décrit ce à quoi elles ressemblent\)
* Certains caractères sont systématiquement interprété par le parser, il faut donc utiliser des _entity_ :

| Caractère | Entity |
| :--- | :--- |
| `<` | `&lt;` |
| `>` | `&gt;` |
| `&` | `&amp;` |
| `'` | `&apos;` |
| `"` | `&quot;` |

* `<!-- Ceci est un commentaire -->`
* `<!-- Ceci est un -- commentaire invalide -->`

## Structure

* Description du document optionnel \(obligatoirement en premier\) :

  `<?xml version="1.0" encoding="UTF-8"?>` 

* Un élément parent à tous les autres \(en dehors de la description\)
* Structure hiérarchique pour le reste

## Tags/éléments/nœuds

* Ces 3 termes sont synonymes
* `<elementName></elementName>`
* Aucun nom prédéfinis, mis à part pour le `<?xml>` \(et ces variantes type `<Xml>`\)
* Case-sensitive
* Commence par une lettre ou un \_
* Ne peut contenir des espaces
* Peut contenir des lettres/nombres/`_`/`.`
* Peuvent être vide `<element></element>` et se fermer tout seul `<element />`
* Un élément entouré de CDATA ne sera pas interprété `<![CDATA[content]]>`

## Attributs

* Possibilité d'ajouter des attributs aux tags :

  `<elementName attributeName="attribute value"></elementName>`

* Aucun prédéfinis 
* Toujours entre `“”` \(ou `‘’`\) même si ce sont des nombres/autres
* En général, les metadata \(par exemple un id\) devraient être des attributs, tandis que les données en elles-mêmes \(par exemple une date\) devraient être des tags.

## Exemple

```markup
<?xml version="1.0" encoding="UTF-8"?>
<bookstore>
  <book category="cooking">
    <title lang="en">Everyday Italian</title>
    <author>Giada De Laurentiis</author>
    <year>2005</year>
    <price>30.00</price>
  </book>
  <book category="children">
    <title lang="en">Harry Potter</title>
    <author>J K. Rowling</author>
    <year>2005</year>
    <price>29.99</price>
  </book>
  <book category="web">
    <title lang="en">Learning XML</title>
    <author>Erik T. Ray</author>
    <year>2003</year>
    <price>39.95</price>
  </book>
</bookstore>
```

## Namespace

Si on a 2 éléments avec un même nom mais ne représentant pas le même type d'informations, on peut préciser des namespace.

```markup
<table xmlns="http://www.w3.org/TR/html4/">
  <tr>
    <td>Apples</td>
    <td>Bananas</td>
  </tr>
</table>
```

On peut aussi préciser un prefix à utiliser `xmlns:prefix="URI"` \(le lien n'est pas suivis par le parser et est juste informatif\) :

```markup
<root>
<h:table xmlns:h="http://www.w3.org/TR/html4/">
  <h:tr>
    <h:td>Apples</h:td>
    <h:td>Bananas</h:td>
  </h:tr>
</h:table>

<f:table xmlns:f="https://www.w3schools.com/furniture">
  <f:name>African Coffee Table</f:name>
  <f:width>80</f:width>
  <f:length>120</f:length>
</f:table>
</root>
```

On peut aussi directement specifier les prefix dans le 1er élément du document :

```markup
<root xmlns:h="http://www.w3.org/TR/html4/"
xmlns:f="https://www.w3schools.com/furniture">
```

## Source

* [W3C](https://www.w3schools.com/xml/default.asp)

