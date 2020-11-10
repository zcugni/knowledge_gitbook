# ELF Linking

### Static Relocation

{% hint style="info" %}
* Read :
  * [https://eli.thegreenplace.net/2012/08/13/how-statically-linked-programs-run-on-linux](https://eli.thegreenplace.net/2012/08/13/how-statically-linked-programs-run-on-linux) 
  * [https://eli.thegreenplace.net/2013/07/09/library-order-in-static-linking](https://eli.thegreenplace.net/2013/07/09/library-order-in-static-linking)
{% endhint %}

### Dynamic relocation

* During compilation of the object file, the linker can resolve all **internal references** to symbols but at runtime it needs to resolve the **external** ones
* To do that, there's 2 solutions :
  * Load-time relocation
    * Quite simple : the linker simply puts a placeholder where the symbol was, and add a relocation entry for it that will be resolved at load time
    * The different types of relocation enables it to deal with multiple situations, but i won't detail them because there's too many
  * **Position-Independent Code**
    * The most popular now

### Position-independent code

#### Introduction

* Position independent code doesn't care about its final address. Since all reference are relative, it will work not matter where it is loaded
* Compared to load-time relocation, this technique handle the relocation from the data segment instead of the text one, this has 2 advantages :
  * The **text segment** can remain **read-only**, and as such be shareable to other processes
  * **The relocation is done per symbol** instead of per symbol reference
* However, there's also disadvantages :
  * More instructions are needed
  * A register is often use to reference the `.got` rapidly, so there's one less for general purpose
* To do that, it uses the **Global Offset Table** \(`.got`\) and the **Procedure Linkage Table** \(`.plt`\)
  * The `.got` is an array of pointers which can be 2 things :
    * Absolute addresses of symbols
    * Addresses within `.plt`
  * The `.plt` is an array of little functions
* To explain how it works, i must first explain some things :
  * The offset of an instruction within the `.text` section is what `eip` would be at this instruction
  * The linker knows the address of the sections, so if it can retrieve `eip` at a specific instruction, it can deduce the offset between this instruction and `.got`
  * However, there's no instruction for retrieving `eip`, but a small trick does it :
    * `call` a function which has only one instruction : `pop <reg>`
    * This work because when a call occurs, the next instruction to be executed is pushed to the stack
* The following explanation is for the x86 architecture, it will differ on others

{% hint style="info" %}
Read that one day : [PIC on x64](https://eli.thegreenplace.net/2011/11/11/position-independent-code-pic-in-shared-libraries-on-x64)
{% endhint %}

#### Global variable

* This is the process to resolve a symbol reference to a global variable :
  * Instead of writing the absolute address of the symbol in its `st_value` :
    * Write the offset of an entry within the `.got` in its `st_value`
    * Write the index of the `.got` within the section header table in its `st_hndx`
    * Fill the `.got` entry with the absolute address of the symbol
* In the corresponding assembly code, instead of having a `mov ..`, these instruction will be used :
  * `call` to a function that only has 1 instruction `pop <reg>`
  * `add`  to `eip` a computed offset to the start of the `.got` 
  * `add` to the start of `.got` the offset of the corresponding entry of the symbol
  * Copy the value pointed by the entry at the place of the symbol
* Then, in the corresponding machine code, instead of simply doing a `mov ..` with the absolute address, these steps must be taken :

#### Functions

* With functions, the process isn't as straightforward
* The linker follows the principle of Lazy Relocation : it relocates only at the last moment, to avoid relocating all the functions when just a few might be used
* It is done through the `.plt` :
  * Instead of doing a `mov ..` from the `.got` value, a `call` is made to `<func_name>@plt`
  * In `.plt`, this function will be a `jmp` to an address pointed by a `.got` entry
  * This entry will be one of 2 things, depending of if it's the first time the function is called
  * The first time, the entry will point to the next instruction within `.plt` :
    * Those will `push` some arguments and then `call` the 1st function of the `.plt` :  a resolver
    * It uses the arguments to replace the value in the `.got` entry by the address of the function being called
  * The following times, the dereference `jmp` in `.plt` will directly point to the function
* The `.plt` is sometimes referred to as a trampoline

## Sources 

* eli.thegreenplace.net  :
  * [Load-time](https://eli.thegreenplace.net/2011/08/25/load-time-relocation-of-shared-libraries)
  * [PIC](https://eli.thegreenplace.net/2011/11/03/position-independent-code-pic-in-shared-libraries)
* elf's man page

