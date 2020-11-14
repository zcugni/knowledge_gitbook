---
description: 'Disclaimer : This note is not complete & not up to current standard'
---

# Javascript

## Generalities

* ECMAScript is the real name of the language
* Use to make web pages interactive

## Insertion

* Written within `<script></script>` tags
  * The type can be specify `<script type="text/javascript">` but js is the default scripting language for html
* script tag can be placed within the `<head>` or `<body>` tag
* Putting scripts at the bottom of the body speed up the display because they are interpreted after the visual elements
* They can be loaded from an external file by specifying an `src` to a script tag : 
  * `<script src="external.js"></script>`
  * You still need to close the tag

## Syntax

* `// This is a single line comment`
* `/* This is a multi line comment */`
* `;` aren't mandatory
* String between `'` or `"`
* CamelCase is generally used

### Variables

* Name start by letters, `_` or `$`
* Can contains letters, digits, `_` & `$`
* Unassigned variables have the value of `undefined`

```javascript
var x = "value";

var x, y;
x = 10;
y = 11;

var x = "xValue", y = 10, z = "zValue"
```

### Operators

#### Numerical

| Symbol | Definition |
| :--- | :--- |
| + | Addition & Concatenation \(See note\) |
| - |  |
| \* |  |
| / |  |
| % | Modulo |
| \*\* | Exponent |

Note : 

```javascript
"5" + 3 + 1 = "531" 
2 + 3 + "5" = "55"
```

#### Comparison

| Symbol | Definition |
| :--- | :--- |
| `==` & `!=` | Equal & Diff in value |
| `===` & `!==` | Equal & Diff in type & value |
| `>` & `<` |  |
| `>=` & `<=` |  |
| ! | Not |

#### Misc

| Symbol | Definition |
| :--- | :--- |
| `?` | Ternary |
| `&&` / `||` |  |
| `++` / `--` |  |
| `+=` / `-=` / `*=` / `/=` |  |

## Misc

* `typeof` Returns the type of a variable
* `instanceof` Returns true if an object is an instance of an object type
* See the javascript bitwise operators part in [https://www.w3schools.com/js/js\_operators.asp](https://www.w3schools.com/js/js_operators.asp)

{% hint style="info" %}
I stopped [there](https://www.w3schools.com/js/js_arithmetic.asp)
{% endhint %}

## Source

* [W3C](https://www.w3schools.com/js/default.asp)

