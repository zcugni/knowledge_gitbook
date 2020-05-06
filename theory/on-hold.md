# On hold



## 

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

