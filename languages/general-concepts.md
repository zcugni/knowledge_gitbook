# General Concepts

## Introduction

Some concepts are corner stone of programming and will appear in every language in one form or another. Generally, syntax and level of abstraction will change for each, but i'll detail the base ideas here.

This concerned **procedural** programming, not **functional**.

**Disclaimer** : there might be false generalization here, i wrote that from my experience with different languages but i'm not an expert on any of them. I also didn't touch on object oriented programming, i'll do that one day.

## Variables & types

* Data needs to be stored, a **variable** is a space of memory that is reserved for some kind of information that you named
  * As the name says, the data in a variable can change over time
    * For some languages, it's type too
  * **constant** contain values that won't change over time
* It can be numbers, words, sentences, etc
* To reserve the right space in memory, the compiler needs to know the **type** \(and therefore the size\) of the data you want to save
  * Some languages, like python, will guess this type from the content, while others, like C, will ask you to define it
  * Common types are :
    * `int` Decimal numbers
    * `float` / `double` Floating points number, like 2.6
    * `unsigned ..` Number types that are only positive
    * `char` One character, like 'A'
    * `strings` Multiple characters, like a sentence or a word
      * Strings are arrays of characters under the hood
    * `boolean` True/False 
    * `pointer` Contains memory address, will be detailed later
    * `array` / `list` Multiple data, the differences will be explained later
      * Some languages accepts arrays &  list containing different types of data
    * `dictionnary` Multiple key-value pair
* Languages that ask you to define the type have some particularities :
  * They lets you momentarily change the type to another one by **typecasting** it
    * For  example, casting an int to a string to be able to write it to the screen
  * A division using at least one non-floating point number will give a decimal result rounded down \(for example 3 / 2 will give you 1\)
  * They have a `void` type, a type that is explicitly not define. Used for function that don't return anything and for pointers
* When you name a variable, you **declare** it
* When you give it a value, you **assign** 

### Pointers

* Contains a **memory address**, let's you access another variable data without copying it's content
  * For example, instead of copying a whole string into a new variable, you just store it's address, and when the program comes to the pointer, it will "follow" it, and retrieve the data stored at this address
* Accessing the value that is at the address pointed by the pointer is called **dereferencing**
  * It's usually done with `*`
  * Likewise, a ptr is usually declared like this : `<type> *<ptr_name>`
* The **address-of** operator \(`&`\) lets you retrieve the address of a variable
  * For example `ptr = &<var_name>`
* To retrieve the value, the programs needs to know what is the **type** of the pointed variable in order to know how many bits to read
  * Because of that, **void** pointers must always be typecasted before being derefenced
* When using **arithmetic** with pointers, the program will also use the type of the pointed variable
  * `ptr + 1` Will for example increment the address by 1 bit if it's a char, and by 4 if it's an int
* Pointers are always used under the hood, but high-level languages hide \(and simplify\) a lot of the details

### List vs Array

* Arrays & list contains multiple value \(of the same type or not\)
* Arrays are of a define fixed size, so they are given an allocated space where each value can be next to each other
  * This is why you can access their content through indexes \(which are actually offset added to the start address of the array\)
* Their indexes start at 0
* List aren't of fix size, and don't get a pre-allocated space, which means that each element must at least keep a pointer to the next one
* Array are easier and faster to traverse, but you can't add or delete element, only change their values
* List are more complex to traverse, but you can add & remove elements all you want

## Control structures

* There's 2 types : conditional and loop
* **Conditional** ones let you do or not do stuff on specific condition, common ones are :
  * `if .. (then) ... else if (then) ... else ...`
    * The then are generally omited
    * Only some part might be use, like only the first `if`, or only `if .. else ..`
  * `Switch`  Lets you list cases in a different syntax
* **Loops** lets you do something for multiple times, common one are :
  * `whille ... (do) ...`
  *  `for ...` For are a different syntax that lets you define an iterator and add or subtract from it each loop
* For concrete examples, look at the languages files

### Operators

* Example syntax might change between languages
* **Arithmetic** : Lets you do math
  * There's the basic : `+`, `-`, `/`, `*`
  * There's also the **modulo** \(usually `%`\) which gives you the remainder of a division
* **Comparison** : Used in condition
  *  `==` / `!=` Equal &  not equal
  * `>` / `<` / `>=` / `<=` Greater than, lesser than, greater or equal, lesser or equal
* **Combination**  : Used in condition, lets you combine multiple ones
  * `||` Or
  * `&&` And

## Function

* Code that is repeated can be placed into a function
* When you **call** it, the program will continue into the function before returning to where it was before
* This makes coding way more efficient
* They can takes **parameters** \(or **arguments**\), aka variable that can be use within the function
  * This makes the functions usable with different values
* A function can **return** a value \(meaning that you can assign a call to a variable to store the result of the function\)
* Some languages \(usually object oriented ones\) lets you **overload** a function, aka declare multiple times a function of the same name
  * What separate these functions are their **signature**, aka the type & order of their parameters
* A function **prototype** is the combination of it's name, parameters &  return types. It tells the compiler what the function expects & returns so that it can compile code using it
  * It will retrieve the actual content of the function when it needs it

## Scope

* Variables can be declared inside a function, meaning they'll exist and will be accessible only from this function. Those are called **local variables**
  * They're on the stack
  * They're declared again each time the function is called
* **Static** variables are local to their function, but they persist between each function call \(aka, they will remember the value they had at the end of the last function called\)
  * Because of that, they're not on the stack but in the bss or data segment of the memory
* **Global** variables are accessible from everywhere in the code
  * They're also in the bss or data segment
* If there's a global and local variable of the same name, the local one will be used

## Format strings

* The majority of languages have function that makes it easier to create a string
* They use **format specifiers** to define how the data should look, and use **placeholders** and **parameters**
* A typical example is `printf`, which might be use like this `printf("This is a number : %d, this is a string %s", 10, string_var);` 
* Check the languages files for their specific syntax

## Randomness

* Computers can't create truly random number, but they can give the impression of it
* Generally, algorithm are used that will create a list of numbers from a given seed
  * The same list will be given if the same seed is used
  * But as long as it's not the case, the numbers will appear random
* Because of that, we usually use the epoch time as the seed

