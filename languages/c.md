---
description: 'Disclaimer : This is an old and not up to current standard note'
---

# C

## Free

La mémoire n’est pas bzero au lancement du programme pour des questions de perfs et elle ne l’est pas non plus après un free. \(Je sais pas si c'est spécifique au C ou le cas tout le temps\)

## String

On peut pas modifier le contenu d’un `char tmp[4] = “test”` car c’est un type `const`. Ce qui n’est pas le cas d’un `char *tmp`.

## `Void *` et déplacement dans la mémoire

Si on veut accéder à un offset après un pointeur il vaut mieux d’abord le caster en `void *`, car par défaut le C ajoutera x fois la taille de la donnée et non juste x octets.

Exemple :

```c
int *nb_ptr;
void *new_add1;
void *new_add2;
new_add1 = nb_ptr + 2; // Offset de 2 * 4
new_add2 = (void *)nb_ptr + 2; // Offset de 2
```

De la même façon, si on a un `void *`, ce en quoi on va le caster définira le nombre de byte récupéré \(1 pour un char, 4 pour un int, etc\).

## GCC

* Lets you compile c file
* `gcc <file_name> [-o <output_name]`
  * The default output name is `a.out`
* To compile in 32 bits, use this option : `-m32`
  * You might need to install this first : `libc6-dev-i386`

## Article

Check [this article](https://matt.sh/howto-c), but it differs a lot from what we did in school without always really explaining why, so do as you wish.

