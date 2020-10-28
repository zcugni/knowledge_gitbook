# Things

## Compilation process

* Compiling is the act of **converting source code to another language**, be it a high/low-level one or machine code \(binary\)
  * A **decompiler** does the opposite
* There's different sorts :
  * **Cross-compilers** can generates executable able to run on a different OS than it
  * **Bootstrap compilers** are written in the language they compile
  * A compiler between high-level languages is a _Source-to-Source compiler_ or **transcompiler**
* The whole compilation takes multiples steps and uses different tools :
  * Preprocessor
  * Compiler
  * Assembler
  * Linker

## Assembler

* Transform assembly code into machine code \(aka binary\), creating object files

## Compiler

* The compiler **transform source code** to another language, be it a high-level one or, more generally, **object code** \(aka binary\)
  * A **decompiler** does the reverse
* There's different sorts :
  * **Cross-compilers** can generates executable able to run on a different OS than it
  * **Bootstrap compilers** are written in the language they compile
  * A compiler between high-level languages is called a _Source-to-Source compiler_ or **transcompiler**
* In addition to translating source code, compilers also **sanity-check** the code \(and returns errors\) and **optimizes** it
* Compilation is divided in 2 steps detailed below :
  * Analysis
  * Synthesis

### Analysis

* The "front-end" of the compiler
* Generates an intermediate representation of the source code, it's needed because :
  * Optimization are easier to do on it than on the source code
  * This allows the analysis to stay the same for any machine, while the synthesis phase needs to adapt to the assembly of the target machine
  * The intermediate code can be language-specific or language independent
* Fills a symbol table
* Check for lexical, syntactic & semantics errors

#### Symbol table

* The symbol table stores all the identifiers with their :
  * Values
  * Types
  * Scopes
  * If they're declared
* There's 2 types of table :
  * **Global** : Accessible by every functions
  * **Scope** : Created for each functions
* It's usually implemented either as :
  * An hash table \(most current\)
  * A binary search tree
  * A linear tree

#### Lexical analysis

{% hint style="info" %}
To be honest i find different explanation of this and i'm not sure, read the Purple Dragon book
{% endhint %}

* Extract lexeme from the code \(a lexeme is is a sequence of characters that matches the pattern of a token\)
* Token are pairs of `<token_category, lexeme>`
* In the example `int x = 10;`
  * The tokens are :
    * &lt;KEYWORD, int&gt;
    * &lt;IDENTIFIER, x&gt;
    * &lt;OPERATOR, =&gt;
    * &lt;CONSTANT, 10&gt;
    * &lt;SYMBOL, ;&gt;
  * The lexemes are :
    * int
    * x
    * =
    * 10
    * ;

#### Syntax Analysis

* Also called parsing
* Takes the tokens of the precedent stage and generate a parse tree \(or better yet, an abstract syntax tree\)
* Check the order of the tokens

#### Semantic Analysis

* Check that the parse tree follows the rules of the language, for example :
  * Assignment of value between compatible types
  * Adding a string to an integer
* Keeps track of :
  * The identifiers, their type & expression
  * When they're declared
  * etc
* Produce an annotated syntax tree that is used to generate an intermediate source code





The following tasks should be performed in semantic analysis:

* Scope resolution
* Type checking
* Array-bound checking





We have mentioned some of the semantics errors that the semantic analyzer is expected to recognize:

* Type mismatch
* Undeclared variable
* Reserved identifier misuse.
* Multiple declaration of variable in a scope.
* Accessing an out of scope variable.
* Actual and formal parameter mismatch.

### Synthesis

* The "back-end" of the compiler
* Takes the intermediate representation & the symbol table as input
* Optimize the code
* Generate the machine code





* A compiler may traverse the source code multiple times before the end of the process



## Linker

* Combine all the object and library files into one executable
* he major task of a linker is to search and locate referenced module/routines in a program and to determine the memory location where these codes will be loaded, making the program instruction to have absolute references.

## Loader

* Loader is a part of operating system and is responsible for loading executable files into memory and execute them. It calculates the size of a program \(instructions and data\) and creates memory space for it. It initializes various registers to initiate execution.

## Interpreter

* An interpreter, like a compiler, translates high-level language into low-level machine language.
* Instead of reading the whole source code at once like a compiler, an interpreter reads one line at a time \(and execute it\)
  * It stops as soon as an error occurs and report it
* 
## Libraries

## Makefile

{% hint style="info" %}
Add
{% endhint %}

## Sources

* Compilers : [tutorialspoint](https://www.tutorialspoint.com/compiler_design/index.htm)
* Lexemes & Tokens : [stackoverflow](https://stackoverflow.com/questions/14954721/what-is-the-difference-between-a-token-and-a-lexeme)



