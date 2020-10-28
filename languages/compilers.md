# Compilers

## Generalities

* Compilation refers to 2 things :
  * Converting a source code to another language
  * Generating an executable from a source code \(by using a compiler, but this takes more steps than just this one\)
* A **decompiler** does the opposite
* There's different sorts :
  * **Cross-compilers** can generates executable able to run on a different OS than it
  * **Bootstrap compilers** are written in the language they compile
  * A compiler between high-level languages is a _Source-to-Source compiler_ or **transcompiler**

## Source code to bin

* This will depend on the language, but the general idea stays the same :
  * The source code is pre-processed, for example to interpret includes &  defines and to remove comments
  * The source code is converted to object code \(binary\)
  * A **linker** combines the object and library files into an executable

### Linker

* Combine all the object and library files into one executable
* he major task of a linker is to search and locate referenced module/routines in a program and to determine the memory location where these codes will be loaded, making the program instruction to have absolute references.

### Libraries



## Source code to language

* This will detail the process of converting source code to assembly, as is often done by compilers \(before an assembler convert the assembly to machine code\)
* Compilers have 3 goal :
  * Translate the code
  * Sanity-check it \(and return errors\)
  * Optimize it
* Compilation is divided in 2 steps detailed below :
  * Analysis
  * Synthesis

### Analysis

* The "front-end" of the compiler
* Generates an intermediate representation of the source code, it's needed because :
  * Optimization are easier to do on it than on the source code
  * This allows the analysis to stay the same for any machine, while the synthesis phase needs to adapt to the assembly of the target machine
  * The intermediate code can be language-specific or language-independent
* Fills a symbol table
* Check for lexical, syntactic & semantics errors

#### Symbol table

* The symbol table stores all the identifiers with their :
  * Values
  * Types
* There's 2 types of table for different scopes :
  * Global : Accessible by every functions
  * Scope : Created for each functions
* It's usually implemented either as :
  * An hash table \(most current\)
  * A binary search tree
  * A linear tree

#### Lexical analysis

{% hint style="info" %}
To be honest i find different explanations of this and i'm not sure, read the Purple Dragon book
{% endhint %}

* Extract lexemes from the code
  * A lexeme is is a sequence of characters that matches the pattern of a token
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
  * Type mismatch
  * Undeclared variable
  * Reserved identifier misuse
  * Scope resolution
  * Array-bound check
  * etc
* Keeps track of :
  * The identifiers, their type & expression
  * When they're declared
  * etc
* Produce an annotated syntax tree that is used to generate an intermediate source code

### Synthesis

* The "back-end" of the compiler
* Takes the intermediate source code & the symbol table as input
* Optimize the code
* Generate the machine code

## Sources

* Compilers : [tutorialspoint](https://www.tutorialspoint.com/compiler_design/index.htm)
* Lexemes & Tokens : [stackoverflow](https://stackoverflow.com/questions/14954721/what-is-the-difference-between-a-token-and-a-lexeme)

