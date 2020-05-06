# Autre Ajouts

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
