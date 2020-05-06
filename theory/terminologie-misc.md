---
description: To polish
---

# Terminologie / Misc

## Binaire

* Un octet \(ou byte\), est composé de 8 bits, plus on avance vers la gauche, plus le “poids” du bits est élevé, même si je suis pas encore sûre de comprendre la formule qui permet de passer de l’un à l’autre.
* Une des façons d’écrire un nombre négatif est de mettre le tout premier bit à 1.

## Listes et tableaux

* La raison pour laquelle on peut accéder à une case d’un tableau par son index, c’est qu’on lui a réservé un espace mémoire côte à côte, et que donc, quand on accède à une certaine case, on accède en fait à l’adresse mémoire de la 1ere + \(l’index \* la taille des choses stockées\).
* Définir le type du tableau permet donc d’indiquer de quel “pas” on doit avancer pour chaque élément.
* Une liste quant à elle n’a pas de zone réservée au préalable, donc on a pas besoin de réserver un espace dès le début, mais on ne peut pas non plus accéder à un index en particulier.

## Heap vs stack

* Initialement, “Heap” et “Stack” désignent simplement deux listes, une en type d’arbre, l’autre en LIFO.
* Mais ces noms ont ensuite été donné aux deux endroits de la mémoire vive utilisée par les programme, la heap et le stack. \(Plus que deux endroits, c’est deux façon de gérer une partie de la mémoire j’imagine\).
* Dû à leur nature, la stack est plus petite que la heap mais plus rapide d’accès.
* Les variables locales et les appels de fonctions sont conservés sur la stack tandis que toutes les variables dynamiquement allouées sont conservées sur la heap.
* La stack est plus rapide d’accès que la heap, mais plus petite.
* Les deux utilisent la RAM \(la mémoire vive de l’ordi, en opposition avec les disques durs, qu’on appelle apparemment aussi mémoire de masse\).

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

