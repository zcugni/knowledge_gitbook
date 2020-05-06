---
description: Not complete & not tested
---

# XPATH

* Permet de naviguer un document XML \(en sélectionnant des nœuds particuliers\)
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

## Opérateurs

### Arithmétiques

| Symbole | Sens |
| :--- | :--- |
| + |  |
| - |  |
| \* |  |
| div |  |
| mod |  |

### De comparaison

| Symbole | Sens |
| :--- | :--- |
| = |  |
| != |  |
| &lt; |  |
| &lt;= |  |
| &gt; |  |
| &gt;= |  |

### Misc

| Sens | Symbole |
| :--- | :--- |
| and |  |
| or |  |
| \| | ? |

## Prédicat \(condition\)

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

## Wildcard

| Wildcard | Definition |
| :--- | :--- |
| \* | Any element node |
| @\* | Any attributes |
| node\(\) | Any node of any kind |
| text\(\) | Any text node |

{% hint style="info" %}
Je suis pas certaine de la diff entre `*` et `node()`
{% endhint %}

## Location Path

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

