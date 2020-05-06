## Norme

Je vais me baser sur les normes PEP qui sont les plus utilisées, en prenant à chaque fois une des méthodes proposées.

### Général

* Variable = fonction\_avec\_paramètre

```python
foo = long_function_name(var_one, var_two,
                         var_three, var_four)
```

* Déclaration de fonction avec paramètres

```python
def long_function_name(
        var_one, var_two, var_three,
        var_four):
    print(var_one)
```

* Condition

```python
if (this_is_one_thing
        and that_is_another_thing):
    do_something()
```

* Déclaration de liste

```python
my_list = [
    1, 2, 3,
    4, 5, 6,
]
```

* Emplacement des opérateurs

```python
income = (gross_wages
          + taxable_interest
          + (dividends - qualified_dividends)
          - ira_deduction
          - student_loan_interest)
```

### Espaces

* Autour des opérateurs “:” des slices quand ils séparent autre choses que des simples nombres.

```python
ham[1:9], ham[1:9:3], ham[:9:3], ham[1::3], ham[1:9:]
ham[lower + offset : upper + offset]
```

* Autour des opérateurs d’assignement et de comparaisons
* Pour les opérateurs de calculs, on en mets autour de ceux qui ont le moins de priorités

```python
x = x*2 - 1
c = (a+b) * (a-b)
```

* On ne les mets PAS dans les valeur par défauts/les return :

```python
def complex(real, imag=0.0):
    return magic(r=real, i=imag)
```

* Après le “\#” de début de commentaire

### Ligne vide

* Surround top-level function and class definitions with two blank lines.
* Method definitions inside a class are surrounded by a single blank line.
* Extra blank lines may be used \(sparingly\) to separate groups of related functions. Blank lines may be omitted between a bunch of related one-liners \(e.g. a set of dummy implementations\).
* Use blank lines in functions, sparingly, to indicate logical sections

### Import

* Au début du document
* Dans cet ordre : standard, third party, our
* Sur plusieurs ligne pour les “import” avec des virgules pour les “from.. import”

### Docstring

* Placées après la ligne de définition.
* Pour tout ce qui est publique. Pour les fonctions privées, un commentaire peut suffire 
* Je suis les guidelines de Google, donc ce format-ci

```python
""" description

        Args:
            var_name (var_type): description
            var_name (var_type): description

        Raises:
            RuntimeError: description

        Returns:
            var_name : description
"""
```

### Naming convention

* module : short lower-case name underscore
* class : CamelCase
* function/variable : lowercase with underscore
* Si le nom qu’on veut donner est un mot réservé rajouter \_ à la fin
* constant : uppercase with underscore

### Remarques diverses

* Ligne de 79 caractères, docstring de 72
* Espace/tab de 4 \(ils conseillent espaces mais je préfère tab je pense\)
* Use string methods instead of string module \(more efficient\)
* Use `.startswith()` and `.endswith()` instead of string slicing to check for prefixes or suffixes \(cleaner and less error prone\)

```python
Yes: if foo.startswith('bar'):
No:  if foo[:3] == 'bar':
```

* Object type comparisons should always use isinstance\(\) instead of comparing types directly

```python
Yes: if isinstance(obj, int):
No:  if type(obj) is type(1):
```
