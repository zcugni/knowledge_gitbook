---
description: 'Disclaimer : The vuln part isn''t complete / might not be right'
---

# Cryptography

## Disclaimer

* I haven't yet had the occasion to really research cryptography, the following is more akin to personal notes of stuff i stumble upon while researching other things
  * As such, it's neither complete nor fault-proof

{% hint style="info" %}
I should re-read the cryptography chapter of Hacking : The Art of Exploitation once i have a better understanding of this field
{% endhint %}

## Generalities

* A cryptographic system is considered to be **unconditionally secure** if it cannot be broken, even with infinite computational resources
  * One example is the one-time pad
  * A random blocks of data called a pad is created. It's at least as long as the plaintext message to encode
  * XOR each bit of the plaintext with its corresponding pad bit, the receiver will XOR them again to decode
  * If its used only one time, it's totally secure, except that the 2 sides needs the pads and it most be kept private 
* A cryptosystem is considered to be **computationally secure** if the best-known algorithm for breaking it requires an unreasonable amount of computational resources and time
* **Product ciphers** combine both confusion & diffusion :
  * **Confusion** refers to methods used to hide the relationships between the plaintext, the ciphertext and the key
  * **Diffusion** serves to spread the influence of the plaintext bits and the key bits over as much of the ciphertext as possible

## Checksum

* Used to verify the integrity of a file against **accidental modifications**
* Generate a **signature** from the file
* When you receive a file with it's signature, you can use the algorithm against the file to check that the generated signature corresponds
* It's quite easy to forge a file in a way that will give it the same signature for a different content, so it's not useful against malicious modifications

## Hash

* Used to verify the integrity of a file against **malicious modifications**
* Generate a **signature** from the file
* Since the signature is a string of a defined length, generally quite smaller than the source text, there will be **collisions** \(different file with the same signature\)
* Characteristic of a good hash :
  * Sufficiently fast to be practical, but not enough to make brute forcing easy
  * Collisions shouldn't be predictable
* **Message Authenticated Code** \(MAC\) uses hashes with the added functionality of identifying the sender
  * A shared key is used to create & validate the MAC
  * One of the most known is HMAC, it use any type of hash algorithm

### Crack

* There's different ways to crack an hash :
  * Brute-forcing every possible combination of chars, often impracticable because they're too big
  * Using a pre-made **dictionary** of plaintext to hashes
  * Using a **hash lookup table**
  * Using a **password probability matrix**
    * Uses a form of lossy compression, its similar to an hash lookup table except in that it's not complete, it will return multiple results, but is faster to create & use
  * Using a **rainbow table**
* However, the use of arbitrary **salt** values makes those way less efficient 

## Encryption

The goal of encryption is to protect sensible data, there's 2 main types : symmetrical & asymmetrical

### Symmetrical

* The same key is used both for encryption & decryption
* You need to pass the key to the other side, which can be risky, but it's faster than asymmetrical encryption

#### Stream

* Stream ciphers generate a stream of pseudo-random bits, usually either one bit or byte at a time
  * This is called the keystream, and it's XORed with the plaintext
* Most common are RC4 & LSFR

#### Block

* Encrypts data in block of a fixed size, usually 64 or 128 bits
* Encryption and decryption aren't the same
* Examples are AES \(Rijndael\), DES, 3DES, Blowfish
  * DES’s only real weakness is its key size of 56 bits which make an exhaustive brute-force attack possible in a few weeks on specialized hardware
    * Triple-DES fixes that by using 2 DES keys concatenated together for a total size of 112 bits
    * Encryption is done by :
      * Encrypting the plaintext block with the first key
      * Decrypting with the second key
      * Encrypting again with the first key
    * Decryption is done analogously, but with the encryption and decryption operations switched
  * Most industry-standard block ciphers are resistant to all known forms of cryptanalysis, and the key sizes are usually too big to attempt an exhaustive brute-force attack
* In **Electronic CodeBook** mode \(ECB\), each plaintext block is encrypted independently to produce a ciphertext block
  * Meaning that 2 identitcal ciphertext also have identical plaintext
* In **Cipher-Block Chaining** \(CBC\) mode, each plaintext block is XORed with the ciphertext of the previous block before encryption \(opposite for decryption\)
  * The first block is XORed with the **Initialization Vector**
* Padding, if needed, is usually done with **PKCS\#7**

### Asymmetrical

* One public key encrypt, and a private one decrypt
* You pass the public key with anyone with whom you want to communicate
  * They'll encrypt their message with your key
* Your private key should be strictly secret
* RSA is the most known, it uses a mathematical proporty : when you compute the product of 2 prime numbers, it's very complicated to retrieve them from the result
  * The bigger the number, the harder
  * I won't explain how it work exactly, i just know that it uses these prime numbers
* It's more secure but slower than symmetrical encryption, so it's not use to encrypt a lot of data
  * Hybrid cipher use an asymmetric key to encrypt the symmetric key used to encrypt the data
  * Hybrid ciphers are used by most modern cryptographic applications, such as SSL, SSH, and PGP

### One time pad

* A one time pad take a text of length N, generate N or plus random chars and XOR the 2
* As long as it used only one time, if the pad is not compromised in in transit, it's completely secure

## PGP - Pretty Good Privacy

* Lets you encrypt data by mixing symmetric & asymmetrical encryption
* Process :
  * The sender :
    * Encrypt it's message with it's symmetric key
    * Encrypt the key with the receiver public key
    * Sends the encrypted message & key
  * The receiver :
    * Decrypt the key with it's private key
    * Decrypt the message with the decrypted key

## Vulnerabilities

### Stream Cipher Reuse

* I'm not exact sure why, but if you encrypt 2 blobs of data with the same key, anc XOR their ciphertext together, you obtain the XOR of the plaintext
* eSTREAM does'nt have this problem because it uses a nonce \(a salt i guess ?\)
  * If you don't re-use the same key-nonce pair, its safe
* RC4 has this problem, so generally dev XOR the key in advance with a nonce

### ECB Block Reordering

* ECB blocks are independent, you can reorder them however you want
* For example :
  * A cookie encrypt this with DES :`admin=0;username=daeken1`
  * It uses 8 bits blocks, so the string is divided in 3 blocks : 
    * admin=0;
    * username
    * daeken1
  * If you control the username, you can give as a payload : `paddingadmin=1;`, which will give the blocks :
    * admin=0;
    * username
    * =padding
    * admin=1;
  * You can reorder them as you want

### Pad Oracle

* For this to work, data must be encrypted with CBC, and padded with PKCS\#7
  * And the server needs to respond differently if they're padded or not
* Since CBC blocks are independent, if you flip a bit in one, it will flip the same bit in the next one
* You can use this to decrypt with a paddle oracle
  * The goal is to determine which bit of the N-1 block will change the plaintext of block N in `0x01` after being XORed together
  * Once we know this, we know that the plaintext of that bit in block N is cipher XOR 0x1
  * Test all possible char, the only one that won't return an error will be the one to flip the bit to `0x01` \(since the padding will be correct\)
  * Continue doing that and changing the padding to `0x02`, then `0x03`, etc

### Contermeasure

* The pad oracle and the ECB re-ordering can be counter if the dev uses a MAC _after_ having encrypted the data
* But sometimes it's not possible for the pad for compatibility reasons, in which case you need to at least not react differently on padding error

## Others

* A Feistel network is often used in block ciphers to ensure that the algorithm is invertible
  * Each block is divided into two halves : left \(L\) & right \(R\)
  * I'll show the process on each round `i` :
  * `-1` Means the previous one, note minus 1
  * `Ki` is the subkey for this round, normally each round has one, calculated earlier
  * `Li = Ri-1`
  * `Ri = Li-1 XOR function(Ri-1, ki)`
* Tools : 
  * RainbowCrack 

## Sources

* [Hacker101](https://www.hacker101.com/sessions/crypto_crash_course)
* PGP Encryption : [varonis.com](https://www.varonis.com/blog/pgp-encryption/)
* _Hacking : The Art of Exploitation_ by John Erickson
* Misc Research

