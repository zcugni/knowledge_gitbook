# Syntaxe

## Générale

* Déclaration de variable : sans type \(ex : `index = 0`, ou `str = "Test"`\)
* `# Ceci est un commentaire.` Il n'y en a pas de multi-lignes, à part les Docstrings qui sont utilisée pour la documentation

## Opérateurs

### Numériques

| Symbole | Sens |
| :--- | :--- |
| + | Addition / Concaténation |
| \* | Multiplication \(nombre & strings\) |
| - |  |
| / |  |
| // | Floor division \( 5 // 2 = 2\) |
| % | Modulo |
| \*\* | Exposant |

### De comparaison

* `==` && `!=`
* `<` && `>`
* `<=` && `>=`

### Autre

* `and`
* `or`
* `not` / `is not`
* `in` Check si un élément est dans une liste, est aussi utilisé dans les boucle for

## Conditions

Le switch n’existe pas.

### if - elif - else

```python
if test:
    #code
elif test:
    #code
else:
    #code
```

#### Utilisation du not

```python
if "." not in str:
    # code

if var is not np.nan:
    # code

if not (elem1 or elem2 or elem 3):
    # code
```

### Ternaire

```python
val = "something" if test else "something else

# On peut mettre du ternaire dans un printf :
print("Splitting", total_candies, "candy" if total_candies == 1 else "candies")
```

## Boucles

### for

```python
# De base
for var_name in iterable_object:
    # code

# Avec range (on peut omettre start s'il veut 0)
for var_name in range(start, end):
    # code

# Avec index
for index, var_name in enumerate(iterable_object):
    #code
```

### while

```python
i = 0
while test:
    # code
```

## String

* Les strings peuvent être entourées "" ou de ''
* On peut comparer des string entre elles
* On ne peut pas modifier les string

### Fonctions utiles

* `.upper()` && `.lower()`
* `.index(“substring”)`
* `.split()`
* `.join()`
* `len(str)`

### Concaténation

* Avec `+`
* Avec format : `"asdqwr {0} wrew {1} {1}"".format(var0, var1)`. On peut aussi ne pas mettre d’index et juste {}, auquel cas les params doivent être dans l’ordre.
* Pour concaténer avec d’autres type , il faut d’abord caster au moyen de `str(var)`.

## Fonction

### Déclaration

```python
def name(param1, param2, param3 = valeur_defaut):

    """Documentation

    [blablabla]

    >>> exemple

    """
    #ligne1
    #ligne2
    return val
```

* Les lignes en dessous d’une déclaration doivent être indentées.  
* Les paramètres peuvent être optionnels si on leur fournit une valeur par défaut. Ceux-ci doivent être placés à la fin de la liste.
* On peut passer une autre fonction en paramètre simplement en donnant son nom \(sans les  parenthèses\).
* Ce n’est pas possible de passer par référence

### Retour

* Pas obligatoire \(ce sera `None` par défaut\) 
* Parenthèse non obligatoires
* On peut retourner plusieurs valeurs aux moyens des tuples

```python
def exemple(param):
    #code func
    return (nb1, nb2)

plop, plip = example(3)
```

### Appel

`name(param1, param2, param3)`

* Valeur par défaut \(pour param3\) `name(param1, param2)`
* Ordre non important `name(param2, param3 = val, param1 = val,)`
* “Unpack” une liste/tuple \(python récupére 1 à 1 les éléments comme paramètres\) `name(param1, *list_ou_tuple)`

### Fonction anonyme

Une fonction anonyme est une fonction qu’on a pas déclarée, à laquelle on a pas assigné de nom. On l’utilise donc en général qu’une seule fois en l’a passant en paramètre.

Par exemple, ceci pourrait être passé à un `.apply()` de panda :

```python
lambda x : ast.literal_eval(x) if not pd.isna(x) else []
```

## Classe

### Généralité

* Tout en python est un objet. 
* On peut appeler une fonction de la classe parente en utilisant `super()`.
* Pour accéder à nos propres fonctions/méthodes, on utilise le mot clé `self`.

### Syntaxe

```python
class Nom:
    variable_classe

    __init__(self, param, ..):
        self.variable_instance
        #code

    def func1(self, ...):
        #code

    @staticmethod
    def func2(....):
        #code
```

### Variable & Fonctions

* Les variables \(attributs\) et fonctions \(méthodes\) sont accessible avec un `.`
* Les variables de classes déclarées directement à la racine de celle-ci sont partagées entre chaque instance
* Les variables d’instance sont indépendantes d’une instance à l’autre et s’accède au moyen de `self.var_name`
* Les fonctions d’une même classe sont aussi accessible par `self.func_name(...)`
* Le premier paramètre par défaut d’une méthode est `self`, présent dans sa définition mais pas quand on l’appelle
* Si on n’a pas besoin de self, on peut écrire `@staticmethod` au-dessus

### Module & package

Les modules sont en fait les fichiers dans lesquels sont définis les fonctions/variables/etc. Leur nom dépend du nom du fichier. C’est ce qu’on `import`.

Les packages sont en fait les dossiers et sous dossiers contenant ces modules. Un packages gérant le son aura par exemple cette structure-là:

```text
/sound  # package name

    __init__.py

    formats/ # subpackage
            __init__.py
            wavread.py  #module
            wavwrite.py
            …

    effects/
            __init__.py
            echo.py
            surround.py
            …

    .../
```

## Structure de données

### List

* On les déclare ainsi : `list = [element1, element2, element3]`
* Les éléments peuvent être de n’importe quel type
* On peut avoir plusieurs type par liste
* On peut accéder à un élément d’une liste comme pour un tableau.
* L’index commence à 0
* On peut commencer par la fin en utilisant des index négatifs
* On peut modifier le contenu d’une liste sur le tas

On peut aussi écrire des listes de listes de 2 façons :

```python
hands = [
    ['J', 'Q', 'K'],
    ['2', '2', '2'],
    ['6', 'A', 'K'], # (Comma after the last element is optional)
]

hands = [['J', 'Q', 'K'], ['2', '2', '2'], ['6', 'A', 'K']]
```

#### Slicing

Syntaxe de base : `[start_rows:end_rows, start:col:end_col]` pour du 2d, avec une liste à un seul niveau on n’a qu’une sélection autour de `:`.

* On peut utiliser des index négatifs
* Le 0 n’est pas obligatoire
* Le `:` non plus si on prends tout

Sélectionner plusieurs index qui ne sont pas continus :

```python
df[[in1, ind4, ind7], [col2, col4, col9]]

# Ou 
cols = [col2, col4, col9]
rows = [ind1, ind4, ind7]
df[rows, cols]
```

#### List comprehension

On peut directement appliquer une condition ou une boucle à une liste :

```python
loud_short_planets = [planet.upper() + '!' for planet in planets if len(planet) < 6]
```

> `['VENUS!', 'EARTH!', 'MARS!']`

#### Fonctions & méthodes utiles

* `len()`
* `sorted()` Ca les trie
* `sum()`
* `max()` && `min()`
* `.append()`
* `.pop(index)` Enlève et renvoit l’élément
* `.index(elem_name)` Donne l’index de l’élément
* `.remove(value)` Supprime la première instance de la valeur

### Tuples

Les tuples sont comme des listes, sauf pour 2 choses:

* Ils se déclarent avec parenthèses `t = (1, 2, 3)`
* Ils ne peuvent pas être modifié
* Assigner des variables au résultat d’un tuple : `var1, var2 = tuple`

### Dictionnaires

Se déclare entre {}

```python
nb = {"one" : 1, "two" : 2, "three" : 3}
```

Pour ajouter un élément, on donne juste sa clé et sa valeur

```python
nb["eleven"] = 11
```

* `.values()` Récupère les valeurs
* `.keys()` Récupère les clés
* `.items()` Récupère les deux

#### Dictionary comprehension

Similaire à celle des listes.

```python
planet_initial = {planet: planet[0] for planet in planets}
```

> {'Mercury': 'M', 'Venus': 'V', 'Earth': 'E', 'Mars': 'M'}

#### Loop sur les dict

Un loop sur un dictionnaire va prendre les clés en index.

```python
for k in numbers:
    print("{} = {}".format(k, numbers[k]), end = ' ')
```

> one = 1 two = 2 three = 3 eleven = 11

Avec `.items()` on récupère les clés et les index.

```python
for key, val in dict.items():
    #code
```

## Time Conversion

* Si on a une date sous ce format : "Wed, 22 Apr 2020 16:02:51 GMT" on peut la transformer en `struct_time` \(time tuple\), ainsi :

```python
# [...]
# Récupère la date du serveur dans ce format "Wed, 22 Apr 2020 16:02:51 GMT"
serverTime = response.headers["Date"]
format = "%a, %d %b %Y %H:%M:%S %Z"
time_tuple = time.strptime(serverTime, format)`
```

* On peut transformer un time tuple en **timespamp epoch** ainsi : `calendar.timegm(time_tuple)`

## Explorer des variables

Avec `dir(obj)`, on peut récupérer la liste des attributs de l'objet

