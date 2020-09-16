# Vulnerabilities

## Injection

* Nearly all attacks are an injection of a form or another
* The goal is to get a vicious string interpreted by whatever technology is used
* To get out of the original string/instruction in which the input is inserted, you can :
  * Add comment symbol
  * Close the original statement \(with `;` for example\) and add yours afterwards
  * Use concatenation

## Types of attacks

* **Inband** : The result is directly visible \(sent or displayed\)
* **Out-of-band** : We must force the app to send the result from another way : 
  * By adding it as a cookie or parameter to a request made to our server
  * By pipping the result to netcat to send it back to us
  * etc
* **Blind \(oracle\)** : The result isn't retrievable, but the application reacts differently in function of it
  * With that, you can get an mdp char by char for example
  * Look for differences in : 
    * Wording
    * Response status code
    * Response time
* **Error based** : Extract the data via error messages

## Bypassing sanitizers

* **Encode** :
  * Url
  * Unicode or double unicode \(`%2e` or `%c0%af`for `/` for example\)
  * Base64
  * Hexadecimal
    * `printf "\x41"`
    * `password LIKE 0x4125`
  * Either it will be understood directly, or inside the payload you can add code to decode \(for example if the website verify your input but the server behind it doesn't check afterwards\)
* See if the check is **case sensitiv**e or not
* **Fragment** or mix the input, or try to double some elements
  * If the sanitizers aren't recursive or are straightforward, they'll remove only a part of your input
* If some characters are removed, see if you can **create the string from numbers with functions**, for example :
  * tostring a number with a base : `17795081..toString(36)`
  * `eval(String.fromCharCode(97, 108, 101, 114, 116, 40, 49, 41))`
  * `password LIKE CHAR(65,37)`
* More generally, try to find **alternative functions** to create your input
  * For example, if the `"` are sanitized you can eval a regex : `eval(/alert(1337)/.source())`
* Add a null byte `%00` to terminate a string earlier
* **Retrieve your char from** **constant** or variable, for example :
  * To get `/` you can `echo $HOME` and retrieve the start
  * `echo ${<env_var>:13:1}` The first number is the position of the char, the second the number of chars to retrieve
* The following examples are specific to some technologies/languages, but see if you can adapt it to your situation : 
  * **Break out of html commen**t : 
    * If `<?php>` \(or `<?>`, it's shortcut\) are in the html response, browser will transform it to `<!--php-->` because server code shouldn't be on the client
    * However, nested comment aren't possible in html, so if we do that inside of an existing comment, it terminate it earlier
  * If there's a verification that the address start with `/`, use : `//google.com`
  * The whole **127.0.0.1/8 subnet** is localhost, so if 127.0.0.1 is filtered, try that or localhost
  * **JSFuck** is a valid javascritp syntax using only 6 characters : `[`, `]`, `(`, `)`, `+`, `!` which you can use to bypass filters

## Misc tips

* Try to get warnings instead of errors, they might not stop the execution flow
* Check the source code of the language/functions in the documentation if it's available

## Sources

* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)
* PwnFunction's [video](https://www.youtube.com/watch?v=jkJWA_CWrQs)
* Misc research

