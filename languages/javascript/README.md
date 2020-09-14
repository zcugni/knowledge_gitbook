---
description: 'Disclaimer : This note is not complete & not up to current standard'
---

# Javascript

## Généralités

* ECMAScript is the real name of the language
* Utilisé pour rendre les pages interactive

## Insertion

* Written within `<script></script>` tags, the type can be specify `<script type="text/javascript"` but as js is the default scripting language for html, it's optionnal.
* script tag can be placed within the `<head>` or `<body>` tag
* Putting scripts at the bottom of the body speed up the display because they are interpreted after the visual elements.
* Can also be loaded from an external file by specifying an src to a script tag : `<script src="external.js"></script>` \(you still need to close the tag\).

## Syntaxe

* `// This is a single line comment`
* `/* This is a multi line comment */`
* `;` aren't mandatory
* String betwen `'` or `"`
* CamelCase is generally used

### Variables

* Name start by letters, \_ or $
* Can contains letters, digits, \_ and $
* Unassigned variables have the value of \`undefined\`

```javascript
var x = "value";

var x, y;
x = 10;
y = 11;

var x = "xValue", y = 10, z = "zValue"
```

### Operators

#### Arithmétique

| Symbole | Sens |
| :--- | :--- |
| + | Addition & Concaténation \(See note\) |
| - |  |
| \* |  |
| / |  |
| % | Modulo |
| \*\* | Exponentiation |

Note : 

```javascript
"5" + 3 + 1 = "531" 
2 + 3 + "5" = "55"
```

#### De comparaison

| Symbole | Sens |
| :--- | :--- |
| `==` & `!=` | Equal & Diff in value |
| `===` & `!==` | Equal & Diff in type & value |
| `>` & `<` |  |
| `>=` & `<=` |  |
| ! | Not |

#### Autre

| Symbole | Sens |
| :--- | :--- |
| `?` | Ternary |
| `&&` / `||` |  |
| `++` / `--` |  |
| `+=` / `-=` / `*=` / ... |  |

## Others

* `typeof` Returns the type of a variable
* `instanceof` Returns true if an object is an instance of an object type
* See the javascript bitwise operators part in [https://www.w3schools.com/js/js\_operators.asp](https://www.w3schools.com/js/js_operators.asp)
* I stopped there : [https://www.w3schools.com/js/js\_arithmetic.asp](https://www.w3schools.com/js/js_arithmetic.asp)

## Source

* [W3C](https://www.w3schools.com/js/default.asp)

