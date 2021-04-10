# XML

## Generalities

* Stands for eXtensible Markup Language
* Used for **transporting and stocking data**
* Describe what are the data \(while HTML describe their structure\)
* Some characters are reserved so you need to use _**entities** :_

| Characters | Entity |
| :--- | :--- |
| `<` | `&lt;` |
| `>` | `&gt;` |
| `&` | `&amp;` |
| `'` | `&apos;` |
| `"` | `&quot;` |

* `<!-- This is a comment -->`
* `<!-- This is -- an invalid comment -->`

## Structure

* The description of the document is optional but needs to be first if there :`?xml version="1.0" encoding="UTF-8"?>` 
* It's hierarchical 
* There's a root element to all others \(beside the description\)

## Tags/elements/nodes

* Those 3 terms are synonyms
* `<elementName></elementName>`
* No predefined name, except for `<?xml>` \(and it's variant `<Xml>`\)
* Case-sensitive
* Begins with a letter or an `_`
* Can't contain spaces
* Can contain letters, numbers, `_`, `.`
* Element can be empty `<element></element` and closed by themselves `<element />`
* An element inside `CDATA` won't be interpreted `<![CDATA[content]]>`

## Attributes

* You can add them to tags : `<elementName attributeName="attribute value"></elementName>`
* None predefined
* Always between `"` or `'`
* Generally speaking, metadata \(like an id\)  should be attributes while data \(like a date\)  should be nodes

## Example

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

* Too differentiate elements with the same name but not representing the same thins, use namespaces :

```markup
<table xmlns="http://www.w3.org/TR/html4/">
  <tr>
    <td>Apples</td>
    <td>Bananas</td>
  </tr>
</table>
```

* You can set a prefix with `xmlns:prefix="URI"` :

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

* You can also specify prefix in the first element of the document :

```markup
<root xmlns:h="http://www.w3.org/TR/html4/"
xmlns:f="https://www.w3schools.com/furniture">
```

* The links aren't followed by the parser

## Source

* [W3C](https://www.w3schools.com/xml/default.asp)

