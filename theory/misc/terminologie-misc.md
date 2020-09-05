---
description: To polish
---

# Terminologie

## List vs Array

* Since array get a pre-allocated space of a defined size, each data of the array is next to each other in memory
  * This is why we can use the base address + index \(aka array\[x\]\) to get to the desired part
    * The index is multiplied by the size of the data's type
  * This is also why you can't just extend them
* List don't get pre-allocated, so the different data can be at different places in memory
  * Which is way we need a pointer to the next one, and why we need to run through each node
  * This is also why we can remove or add node whenever we want

## Heap vs stack

* Heap & stack are data structures, the first one is a tree and the other a LIFO list
* These names also reference 2 areas of the memory \(RAM\)
* Stack
  * Faster
  * Smaller
  * We push function, parameters & local variable to it
* Heap
  * Slower
  * Bigger
  * Used for dynamic allocation \(with for example `malloc`\)
* Return address, parameters and local variables are stored in the stack

## RAM vs Hard Drive

* The RAM \(Random Access Memory\) is used by programs &  co
  * It's fast
  * It's volatile \(meaning its content disappear on power off\)
  * It's called "Random" because any part of the memory can be accessed regardless of where it's written
* Hard drive & co are the storage unit \(also called "mémoire de masse" in french\)
  * It's slower
  * It's persistent \(doesn't disappear on power off\)

## Race Condition & Mutex

> “A race condition occurs when two or more threads can access shared data and they try to change it at the same time. Because the thread scheduling algorithm can swap between threads at any time, you don't know the order in which the threads will attempt to access the shared data. Therefore, the result of the change in data is dependent on the thread scheduling algorithm, i.e. both threads are "racing" to access/change the data.
>
> “A Mutex is a lock that we set before using a shared resource and release after using it. When the lock is set, no other thread can access the locked region of code. ”

## Types of malware

### Virus

Piece of code that spreads from computer to computer without any direct action or authorization by the owners of the infected machines.

They usually copy themselves inside legitimate programs or documents and so run every time an infected program or file is opened.

### Trojan horse

A malware that comes embedded in a seemingly harmless file.

The most common trojan are backdoors.

### Backdoor

* Composed of 2 components : a server and a client.
* The former runs on the victim machine listening for connections and the client usually run on the attacker machine, to connect to the server.
* Examples are _NetBus_ & _SubSeven_.
* Connect-back backdoors : To escape firewalls protection/detection, on peut inverser le mécanisme et faire en sorte que la victime soit le client et qu'elle se connecte via un port typiquement autorisé sur le serveur tournant sur la machine de l'attaquant.

### Rootkit

* A malware designed to hide itself from users and antivirus program.
* Bootkit : They're rootkits which circumvent OS protection mechanisms by running during the bootstrap phase.

### Adware

Software that shows advertisements to the users.

### Spyware

Software used to collect information about the users's activity such as :

* OS installed
* Visited Website
* Passwords
* etc..

### Greyware

General term used for malware which does not fall under a specific category.

### Dialer

Software that tries to dial numbers on dial-up connections in order to collect money from the victim's phone bill. Nowadays, they target smartphones.

### Keylogger

Software which records every keystroke on the remote victim machine. The log are then send to the attacker machine, using the same technique as backdoors to bypass firewall \(sending traffic via a known authorized port\).

#### Hardware keyloggers

Small devices you can installbetween a keyboard and a computer. The log are storred on the device, so the attacker needs to retrieve it.

#### Rootkit keyloggers

Stealthier than software keyyloggers, they work by hijacking the OS API via an interrupt kernel command

### Bots

Small pieces of software installed on millions of internet connected machines to perform DDOS or other attacks necessitating lots of power.

They're controlled by a Command & Control server.

### Ransomware

Encrypts a computer or smartphone content with a secret key and ask for a ransom to give it back.

### Worms

They spread over the network by exploiting os & software vulnerabilities or by using default credentials or configurations.

They're usually part of another malware and they offer an entry point into the target system.

## XOR

Bitwise operator : exclusive or.

| A | B | Result |
| :--- | :--- | :--- |
| 0 | 0 | 0 |
| 1 | 1 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |

Reversable : If `C = A xor B` then `A = C xor B` \(and `B = C xor A`\)

## Sources

* Misc Research
* [blackblaze.com](https://www.backblaze.com/blog/whats-diff-ram-vs-storage/)
* [Pentesterlab](https://pentesterlab.com/)

