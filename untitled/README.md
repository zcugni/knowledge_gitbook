# Python

## Syntaxe

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

## Pandas

Pandas propose 2 types, les `Series` qui sont des tableaux 1D \(comme des vecteurs quoi\) et les `DataFrame` qui sont des tableaux multi-dimensionnel \(comme des matrices quoi\).

### Fonctions utiles

* `.isin([“col_name1”, “col_name2”])`
* `.median()` & `.mean()`
* `.unique()`
* `.value_counts()`
* `.idxmax()` Return l’index à l’endroit où la valeur est la plus élevée
* `.min()` & `.max()`
* `.agg()`
* `.sort_values()`
* `.describe()`
* `pd.isnull()`
* `.fillna`
* `pd.concat()`
* `.corr()` Calculate correlation
* `.resample()`
* `.append()` Doesn’t work “in place” so need to `=` it
* `.rename`
* `.drop`
* `.at` Nécessaire pour modifier une valeur précise dans une boucle
* `.isna()`
* `df.sample(10)`
* `df.head()`
* `df.tail()`

ATTENTION : mettre `inplace=true` pour drop, rename, fillna, etc.

Que ce soit les Series ou les DataFrame, si on leur applique une fonction/une opération, elle sera par défaut appliquée à chacune des entrées. Les seuls cas pour lesquels ça ne marche pas sont les tests.

Example :

```python
df[“col”] * 2
add_FR(df[“col”])
func_with_params(df[“col”], params)
df1 + df2
```

Etant donné qu’on peut le faire directement ainsi, je ne comprends pas vraiment l’utilité de .apply.

### Déclarer une nouvelle série

`pd.Series(val, index, name)`

#### val

* Une valeur unique \(qui sera prise comme valeur par défaut\)
* Une liste.

#### index

* Liste de string ou de int \(continue ou non\). 
* Si une liste de valeur est donnée, peut être omis 
* Dans le cas contraire il est nécessaire pour imposer une taille à la Series. 
* `Range()` est pratique pour générer une liste de nombre continu.

#### name

Je crois que ça peut devenir une colonne de ce nom-là si on l’ajoute ainsi à un dataframe. La majorité du temps je l’omet.

### DataFrame

#### Déclaration

`pd.DataFrame(dict ou tableau, columns, index)`

dict : `{ “col” : [val_lst] }` tableau : `[ [val_col_1], [val_col_2] ]` columns : Uniquement obligatoire si un tableau est fourni index : optionnel, vaudra une suite continue de nombre s’il n’est pas précisé.

#### Accès

On peut utiliser un index directement après :

```python
df[“col_name”]
df.col_name
df[“col_name”][index]
```

mais les locs sont plus précis \(et permettent du slicing\):

* `.iloc[]` : performe le slice comme le python standard - juste des nombre, en excluant la limite supérieur
* `.loc[]` : slice version pandas - inclut les deux limites et permet une sélection basée sur les noms et des conditions
* `.at[row, col]` : permet d’accéder à une cellule précise, on doit donner les nom de la colonne et non son index

On peut mettre des conditions dans les sélections, par exemple :

`df[df.col_name == “something”]`

Pour plusieurs conditions ça donne ça : `df[ (test1) & (test2)]`

### Combiner DataFrame et Series

Series + Series : `new = s1.append(s2, ignore_index=true)`

DataFrame + Series to the end of column : `new = df[“col”].append(s1)`

DataFrame + Series as column : `df[“col”] = s1`

DataFrame + DataFrame as new row : `new = df.append(df)`

DataFrame + Series as row :

DataFrame + DataFrame as new columns :

### Grouping

On peut grouper les données en fonction d’une colonne. Par exemple, grouper toutes les données en fonction de leur prix. ça va nous diviser et ordonner nos lignes par les groupes formés. On peut ensuite utiliser les même fonction de d’habitude \(par exemple applique un `.max()` sur la colonne note quand les données sont regroupée par prix\).

```python
best_rating_per_price = reviews.groupby('price')['points'].max().sort_index()
```

### Read & Write

```python
df = pd.read_csv("path", index_col = 0)
df.to_csv("cows_and_goats.csv")
```

## Visualisation

### Pandas

### Seaborn

### FacetGrid

Permet de faire un “grid” avec plusieur plot les uns à côté des autres.

Pour ça, on lui passe un dataframe en précisant la colonne ou la ligne \(ou les deux\) sur lesquelles on veut travailler puis on map une fonction de visualisation.

```python
g = sns.FacetGrid(df, col="Position", col_wrap = 6)
g.map(sns.kdeplot, "Overall")
```

### Plotly

### Plotnine

C’est une librairie qui à l’air de beaucoup simplifier la création de graphe, etc. Ca peut valoir la peine d’approfondir.

## A apprendre un jour

* Clarifier les `init`
* Apprendre à utiliser les “inner functions”
* Apprendre à utiliser `pd.merge` et autre outils puissant

