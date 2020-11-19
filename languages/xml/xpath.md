# XPATH

## Disclaimer

Not at all complete

## Introduction

* Lets you navigate an XML document by selecting specific nodes
* XPath expression are also used in other languages
* It works like the path of a file

| Expression | Definition |
| :--- | :--- |
| `nodename` | Selects all nodes with the name "nodename" |
| `/` | Selects any direct children matching |
| `//` | Selects any descendant matching |
| `.` | Selects the current node |
| `..` | Selects direct parent |
| `@` | Selects attributes |

## Operators

* Numerical :
  * `+`, `-`, `*`
  * `div`
  * `mod`
* Comparison :
  * `=, !=`
  * `>`, `<`
  * `>=`, `<=`
* Combination : `and`, `or`

## Condition

* Written inside `[ ]` : `/bookstore/book[1]`
* Some examples :
  * `/bookstore/book[1]`
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
| `*` | Any element node |
| `@*` | Any attributes |
| `node()` | Any node of any kind |
| `text()` | Any text node |

## Location Path

* You can define path explicitly with très redondant avec l'autre méthode donc je comprends moyen\) ainsi : `axisname::nodetest[predicate]`

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

