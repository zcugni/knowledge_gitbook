# Assembly

## Introduction & Disclaimer

This is my introduction to assembly. I used the [microcorruption.com](https://microcorruption.com/) website, which ask you to reverse engineer basic code on a MSP430 MCU. I'll explain instructions, register and whatnot for this MCU, i do not yet know how it generalize to other ones, but i guess the basic principles are the same.

I might not always use the right terms.

## Terminology

* Register : Internal memory of the processor, these are way more rapid to access than "normal" memory
* Offset address : Number added to a base address to get an absolute one
* `pc` \(Program Counter\) : The offset address of the next instruction to execute
  * Also named the `ip` \(Instruction Pointer\)
* `sp` \(Stack Pointer\) : The offset address of the start of the stack 

## Architecture

* 16-bit architecture
* Little-endian
* Composed of 15 registers, 4 of which are reserved :
  * R0 for `pc`
  * R1 for `sp`
  * R2 for `sr` \(Status Register\) - Flags used by conditional jumps
    * Z \(zero\) \(It's set when it's value is 0003\)
    * N \(negative\)
    * C \(carry\)
  * R3 for `cg` \(Constant generation\)

## Instructions

* I won't detail all instructions, only the most useful to me

### Value

* There's multiple way to specify or access a value, i'll detail them here :
  * Replace `y` by a number 
  * `ry` The value stored in `ry` \(the name `sp` or `pc` can also be used\)
  * `@ry` The value in memory pointed by `ry`
  * `@ry+` The value in memory pointed by `ry`, also increment `ry`
  * `#y` The actual value `y`
  * `y` The value in memory stored at offset `r0+y`
  * `y(ry)` Add the offset `y` to the address of `ry`
  * `$` The current value of `pc` \(you can do `$+y`\)
* There's also suffix :
  * `.b` Byte operation
    * For example a `cmp.b` on registers will only use the two lower bytes for it, not the whole value
  * `.w` Word operation

### Single operand

* Some instruction are shortcuts for other longer combination \(which i call "actual instructions"\)

| Instruction | Actual instruction | Verbal Explanation |
| :--- | :--- | :--- |
| `clr arg1` | `mov #0, arg1` | Clears arg1 |
| `tst arg1` | `cmp #0, arg1` | Compares arg1 to 0 |
| `push arg1` | / | Pushes arg1 to the stack, so decrement `sp` and writes arg1 at `sp` |
| `pop arg1` | `mov @sp+, agr1` | Pops the stack to arg1, so set arg1 to `sp`and increment `sp` |
| `call arg1` | / | Calls a function, for that it pushes the current value of  `pc` to the stack and then changes `pc` to arg1 |
|  `ret` | `mov @sp+, pc` | Return \(aka exit\) a function, so pops the stack to `pc` |
| `inc arg1` | `add #1,arg1` | Increment |
| `incd arg1` | `add #`2`,arg1` | Double Increment |
| `dec arg1` | `sub #1,arg1` | Decrement |
| `decd arg1` | `sub #2,arg1` | Double Decrement |
| `swpb` | / | Swap bytes \(for example, swap 007d to 7d00\) |

### Double operand

* General syntax `opcode source,destination`

| Instruction | Explanation |
| :--- | :--- |
| `mov arg1,arg2` |  Copy the value of arg1 in arg2 |
| `add arg1,arg2` | Add arg1 to arg2  |
| `sub arg1,arg2` | Subtract arg1 from arg2 |
| `cpm arg1,arg2` | Compares the arguments by subtracting arg2 from arg1 and set the flags \(for example the Zero Flag is `sp` in consequence\) |

### Conditional

* `and`
* `bis`

### Jump

* Basic : `jmp arg1` Jump to arg1 \(aka changes `pc` to arg1\)
* There's also a bunch of conditional jumps that look at the flags in the `sr` \(will jump if true\) :
  * `jz` Zero
  * `jnz` Not zero
  * `jlo` Lower
  * `jhs` Same or higher
  * `jn` Negative
  * `jge` Greater or equal
  * `jl` Less

## Interrupts

* There's a bunch of special interrupts from the exercises of microcorruption.com
* These are special-purpose functions of the device
* To use them, call the `INT` function, but first, push the interrupt code to the stack \(for example 0x7d\)
* An explanation of all interrupts is given by the game manual

## Disassemble

* Some functions/instructions might not be written into memory when you launch a program \(they can be written from a loop afterwards for example\)
* In this case, they won't be initially disassemble by your debugger, you need to do it explicitly afterwards \(with an online tool for example\)

## Sources

* [microcorruption.com](https://microcorruption.com/)
* [razvioverflow](https://razvioverflow.github.io/microcorruption/)
* Wikipedia's [page ](https://en.wikipedia.org/wiki/TI_MSP430)about the TI MSP430
* [jaimelightfoot.com](https://jaimelightfoot.com/blog/)


