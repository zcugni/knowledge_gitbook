---
description: 'Disclaimer : The vuln part isn''t complete / might not be right'
---

# Cryptographie & Hash

## Checksum

* Utilisé pour vérifier l'intégrité d'un fichier contre des modifications accidentelles
* Permet de créer une "signature" représentant le fichier. Si on nous envoit un fichier, on peut donc comparer le checksum du notre avec l'original
* C'est assez facile de forger un document correspondant à un checksum donné, donc ça ne protège pas des modifications malicieuses

## Hash

* Utilisé  pour vérifier l'intégrité d'un fichier contre des modifications malicieuse
* Permet de créer une sorte de signature du fichier en ramenant celui-ci à une chaine donnée
* La chaîne final ayant une taille précise, il y aura forcément des collisions \(aka, 2 chaines différentes aboutissant au même résultat\)
* On ne peut pas extrapoler le fichier original depuis le hash
* Caractéristique d'un bon algo de hash :
  * Rapide mais pas trop, afin de rendre l'utilisation agréable, mais le brute force compliqué
  * Les collisions ne doivent pas être prévisible/facilement générale. Si c'est le cas, on peut facilement forger un faux document correspondant au checksum
* Pour cracker un hash, hors le brute force on utilise des dicos qui font correspondre pleins de string à leur hash correspondant
  * Il y a aussi les rainbow tables, qui sont des dico un peu particulier, go se renseigner 

## _Message Authentication Code_ \(MAC\)

* Basé sur les hash mais permet d'identifier l'expéditeur en plus de véirifer l'intégrité du message
* Une clé partagée est utilisée pour la construction et la validation du MAC
* Le plus connu est HMAC et utilise n'importe quel hash:

  HMAC\(key, message\) = hash\(key + hash\(key + message\)\) \(the keys are padded separately in each run, that's a simplification\)

## Chiffrement

Le but d'un chiffrement est de protéger du contenu et de le rendre récupérable par la suite. Pour cela, on différencie 2 types.

### Symétrique

Une même clé pour le chiffrement et le déchiffrement, ce qui la rends moins sécu.

#### Stream

* Encrypts data byte by byte
* Most common is _RC4_ used in SSl
* Random number generator seeded with the key generating bytes to XOR with the text

#### Block

* Encrypts data block by block
* Examples are AES \(Rijndael\), DES, 3DES, Twofish
* Encryption and decryption aren't the same
* In _Electronic CodeBook_ mode \(ECB\), each plaintext block is encrypted independently to produce a ciphertext block, meaning that two identitcal ciphertext also have identical plaintext
* In _Cipher-Block Chaining_ \(CBC\) mode, each plaintext block is XORed with the ciphertext of the previous block before encryption \(opposite for decryption\). The first block is XORed with the _Initialization Vector_
* All blocks are of the same length, so padding may be necessary. It's usually done with PKCS\#7.
  * Si on a besoin d'un bit de pad, on ajoute un bit 0x1, si on en a besoin de deux, on ajoute deux bits 0x2, etc.

### Asymétrique

* Une clé publique qui chiffre, que l'on fournit à toutes personnes avec qui l'on veut communiquer. Elles l'utiliseront pour chiffrer leur message.
* Ainsi qu'une clé privée qui déchiffre, que l'on doit absolument garder pour nous.
* RSA est le plus connu, il utilise la propriété mathématique suivante : quand l'on fait le produit de 2 nombres premiers, il est très compliqué de les retrouver à partir du résultat \(surtout s'ils sont grands\)
* Rarely used for encrypting data due to performance & complexity
* Use to securely transmit a symmetric key

### One time pad

* N bits of random data
* N bits of text
* XOR each other

Used only one time it's perfect, but you need to have the initial key, which is why we use more complex solution.

## PGP - Pretty Good Privacy

Logiciel permettant de chiffrer des messages en mélangeant chiffrement symétrique et asymétrique \(car l'asymétrique est bien plus lent\)

L'expéditeur :

1. Chiffre le message avec clé symétrique 
2. Chiffre celle-ci avec la clé publique du destinataire
3. Envoies le message et la clé chiffré au destinataire

Le destinataire :

1. Déchiffre la clé symétrique avec sa clé privé
2. Déchiffre le message avec la clé symétrique

Linux utilise gpg pour gérer ces clés, regarder ... pour plus de détails

## Entrainement

-&gt; [https://cryptopals.com](https://cryptopals.com/)

## Vulnerabilities

### Stream Cipher Reuse

Bon je suis pas 100% de la raison, mais si on a encrypté 2 blobs de données avec la même clé, et qu'on XOR ces deux ciphertext ensemble, on va obtenir le XOR de leur plaintext.

eSTREAM n'a pas ce problème car il utilise un "nonce" avec la clé. \(Un salt j'imagine ?\). Donc si on ne re-utilise pas la même paire clé-nonce, c'est safe.

Mais RC4 ne fait pas ça, donc les dev XOR généralement sa clé à l'avance avec un nonce.

### ECB Block Reordering

Les blocks d'ECB étant indépendant, on peut les réordonner comme on veut.

Exemple : Un cookie encrypte cela grâce au DES `admin=0;username=daeken1`. Celui-ci utilise des block de 8 bits, cette string sera donc divisée en 3 blocks : "admin=0;", "username" et "daeken1".

Si on contrôle le username, on peut donner en input "paddingadmin=1;", ce qui nous donnera ces blocks "admin=0;", "username", "=padding", "admin=1;", qu'on peut ensuite réorganiser comme nécessaire.

### Pad Oracle

Possible quand les données on été encrypté en CBC et paddé avec PKCS\#7 et que le serveur réagit différemment si elles sont bien paddées ou non.

Vu que les blocks CBC sont interdépendant, si on flip un bit dans l'un, ça va flip le même bit dans le prochain. Ceci nous permet de les décrypter avec un padding oracle.

On commence par le dernier byte de l'avant dernier block car ceci va affecter le dernier bit du dernier block.

Notre but est de déterminer quel bit du block N-1 va changer le plaintext du block N en 0x1 quand ils seront XOR.

Once we know this, we know that the plaintext of that bit in block N is cipher XOR 0x1.

On test les 255 caractères possibles et le seul qui ne nous retournera pas une erreur sera cela qui flippera le bit à 0x1 \(vu que ce sera un padding correct\).

Une fois ça trouvé, on avance petit à petit en passant à 0x2 pour du pad, puis 0x3, etc et on peut récupérer la valeur plaintext de chaque bit.

C'est résumé et très mal expliqué, mais c'est l'idée globale.

\(check the explanation in owasp maybe\).

### Contre mesure

Le pad oracle et l'ECB re-odoring peuvent être contré si les dev utilisent un MAC _après_ avoir encrypté les données pour vérifier qu'elles n'ont pas été modifiées.

Mais des fois pour des raisons de comptabilité ce n'est pas possible \(du moins pour le pad\) auquel cas il faut au moins ne pas réagir différemment en cas d'erreur.

### Hash Extension

J'ai rien compris et ça marche pas avec HMAC apparemment.

## Sources

* [Hacker101](https://www.hacker101.com/sessions/crypto_crash_course)
* Misc Research

