# Other addons

## Disclaimer

This is by no mean complete, and there might be some errors. I'll update it one day

## XSL & XSLT

* Disclaimer : i'm not sure i understood correctly
* XSL stands for _eXtensible Stylesheet Language_
* XSLT stands for _XSL Transformation_
* XSLT lets you transform an XML document into another one, or for example an XHTML or HTML one§

### Usage

* In the xml file to transform, link the xsl file before the first element : 

```markup
<?xml-stylesheet type="text/xsl" href="file.xsl"?>
```

* The first element of an xsl file must :
  * Declare the XSLT namespace
  * Be either of type :
    *  `<xsl:stylesheet>`
    *  `<xsl:transform>` 
    * They're synonyms

```markup
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
```

* There's some special element that introduce logic like :
  * `xsl:for-each>`
  * `<xsl:value-of>`
  * `<xsl:if>`
  * etc
  * This is a [complete list](https://www.w3schools.com/xml/xsl_elementref.asp)

## Others 

* XQuery
  * It's to XML what SQL is to databases
  * It was designed to query XML data
* XSD - _XML Schema Definition_
  * A more complete alternative to DTDs
  * Directly written with the XML syntax
* XML DOM

