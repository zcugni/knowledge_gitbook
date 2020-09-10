# Buffer Overflow & Shellcode

## Prerequisite

To explain how a buffer overflow works, I first need to describe what the memory of a process running looks like and how the registers are use.

### Registers

These three interest us :

* `ip` The Instruction pointer register
  * The address of the next instruction to be executed
  * After every instruction execution its value is incremented
* `sp` The ****Stack pointer register
  * It stores the address of the last element of the stack
* `bp` The Base pointer register
  * Usually set to `sp` at the start of the function to save while we use it for over things

### Process memory

This is how the registers and the stack are modified when you call a function :

* At the start, there's :
  * a func
  * the stack filled with whatever was needed until now
  * **ip** points to the `call func` instruction
  * **sp** points to the **old** **sp** \(the content of whatever was needed previously\)
  * **bp** points to the **old** **bp** \(the content of whatever was needed previously\)
* When the function is called, these steps are taken :
  * Push the **arguments** to the stack
    * Push them in reverse order \(the last one first\), so the lowest address of the stack is the first argument
    * **sp** is changed
* The next steps are :
  * Push the address of the **next instruction** \(also called the _**return address**_\) on the stack, **sp** is changed 
  * Get the address of `func`, and set **ip** to it
  * Push **bp** to the stack \(so **old** **bp**\), **sp** is changed
  * Set **bp** to **sp** \(so the **return address**\)
  * Push **local variables** to the stack, **sp** is changed
* Just before the function returns, this is the state
  * The stack is filled by the **arguments**, the **return address** and the **old bp** address
  * **ip** Points to the `ret` call 
  * **sp** Points to the **old bp**
  * **bp** Points to the **return address**
* These are the steps taken to return the function :
  * Set **sp** to **bp** \(so the **return address**\)
  * Set **bp** by popping the **old bp** from the stack
  * Set **ip** by popping the **return address** from the stack

## Explanation

* Buffer Overflows work by overflowing the local variable so that they write over the **return address**
* This enables us to decide which instruction will be read next
* Remark : The local variables are at lower addresses than the return address. Since array grow towards higher addresses, it's possible to override the return address.

## Shellcode

* Once you redirected the `ip` to your malicious input, you can do whatever you want with it, like call other instructions.
* Don't forget that if some type of bits are sanitized, you can for example use registers to store values and do arithmetic operations. 
  * For example, if you need to push a certain value as parameter but it's gonna be sanitized, you can move another value to a register, add or subtract from it so that it equals what you want, and push the register instead of directly the value.

## Sources

* [dhavalkapil.com](https://dhavalkapil.com/blogs/Buffer-Overflow-Exploit/)
* [Stack overflow](https://security.stackexchange.com/questions/135786/if-the-stack-grows-downwards-how-can-a-buffer-overflow-overwrite-content-above)


