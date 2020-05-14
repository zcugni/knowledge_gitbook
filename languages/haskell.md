---
description: >-
  Disclaimer : Cette note n'est pas complète et contient peut-être des
  informations erronées. Je me suis arrêtée en cours d'apprentissage de
  l'haskell car je n'en avais plus besoin
---

# Haskell

## Fonctions

### Généralités

* Elles doivent suivre ces règles :
  * Prendre au moins un argument
  * Retourner quelque chose
  * Toujours retourner la même chose pour une valeur donnée
* Les fonctions lambdas \(anonymes\) sont définis ainsi : 

  ```haskell
  (\x -> x + 1) (2)
  -- (\param_name -> expression) (param)
  ```

* On peut recevoir et retourner des fonctions.

### Argument

A vrai dire, elles ne prennent pas “au moins un argument”, mais précisément un seul.

`func :: agr -> agr -> return` corresponds en fait à `arg -> (arg -> return)`. Haskell simplifie juste la syntaxe et l’utilisation.

### Ordre d’opération

* Le $ remplace les parenthèses : 

  ```haskell
  func1 $ func2 param = func1 (func2 param)
  ```

* Les fonctions sont toujours évaluées avant les opérateurs
* Un opérateur peut être utilisé avant ces arguments ainsi : `(+) 2 3`
* Une fonction peut être utilisée entre ces arguments ainsi : `10 'div' 5` \(les ' ' sont en fait des \`\`\)

### Closure & Partial application

La closure consiste à wrapper une variable dans un lambda \(example 1\), mais haskell le fait aussi automatiquement pour de l’application partielle \(exemple 2\).

```haskell
myFunc f = (\x -> f (x + 1))
```

myFunc va donc attendre l’argument x quand elle sera utilisée. Tel quel, ce n’est pas très intéressant, car on pourrait directement faire

```haskell
myFunc f x = f (x + 1)
```

Ca devient utile quand on veut préparer plusieurs cas.

Exemple :

On a une fonction getUrlRequest qui prends un host, une api key et une ressource. On peut à l’avance préparer des fonctions ayant déjà les bons host \(vu qu’ils reviennent souvent\) et même les bonnes clé d’api. On peut ensuite les utiliser rapidement.

Ainsi, on peut déclarer :

```haskell
googleBuilder = getUrlRequest “[www.google.com](http://www.google.com)”
amazonBuilder = getUrlRequest “[www.amazon.com](http://www.google.com)”

-- Puis des clés d’api :
bookCall = amazonBuilder “1337hAsk311_”
```

Qu’on pourrait directement utiliser.

C’est d’ailleurs pour cette utilisation que

> Anytime you write a closure in haskell, you order the parameter from most to least general.

## Variables

```haskell
-- Avec where
sumSquareOrSquareSum x y = if sumSquare > SquareSum
                      then sumSquare
                      else squareSum
  where sumSquare = x^2 + y^2
     squareSum = (x+y)^2

-- Avec let in
sumSquareOrSquareSum x y = let sumSquare = (x^2 + y^2)
                                           squareSum = (x+y)^2
                                          in
                                           if sumSquare > squareSum
                                           then sumSquare
                                           else squareSum
```

## Type

### Signature de variables

```haskell
x :: Int
x = 2

values :: [Int]
values = [1, 2, 3]

streetAddress :: (Int, String)
streetAddress = (123, “Happy St.”)
```

### Signature de fonctions

* `->` séparent les paramètres et le retour. Le dernier élément est toujours le retour.
* On peut utiliser des placeholder quand on ne veut pas être trop spécifique sur les types. Pour cela on utilise une lettre unique : “a”, “b”, etc.

```haskell
-- Base
double :: Int -> Int
double n = n*2

-- Plusieurs arguments
makeAddress :: Int -> String -> String -> (Int, String, String)
makeAddress number street town = (number, street, town)

-- Fonction en argument
ifEven :: (Int -> Int) -> Int -> Int
ifEven f n = if even n
             then f n
             else n
```

#### Contrainte sur les types

Quand on veut utiliser un placeholder, mais quand même restreindre un peu le scope de celui-ci, on applique une contrainte au type \(celles-ci sont des classes de types\). Les contraintes sont écrites avant le `=>`.

```haskell
print :: Show a => a -> IO ()
```

### Déclaration

#### Alias

```haskell
type Nom = TypeConnu
```

Crée un type qui fonctionne comme le TypeConnu. C’est par exemple `String` pour `[Char]`.

Si une fonction a pour signature: `func -> alias1 -> alias2 -> type` et que les alias se basent sur le même type, le compilateur ne pourra pas soulever une erreur si on les inverse.

#### New Type

```haskell
newtype Nom = Constructeur { unwrapFunction :: TypeConnu } deriving (TypeClasse, TypeClasse, ..)
```

Permet de créer un “vrai” nouveau type, dans le même exemple qu’avant, le compilateur soulèvera une erreur.

Seule la partie `newtype Nom = Constructeur` est obligatoire. Le unwrapFunction \(qui est un record à un seul élément\), permet de récupérer le type sous-jacent à partir du nouveau.

Le deriving .. est optionnel et sera détaillé plus loin.

#### Enum

Le data permet de créer des types composés de plusieurs.

```haskell
data Country = Suisse | France
data Maybe a = Just a | Nothing
data TPError = ParsingError string | ConnexionError ServantError
```

#### Record

Cette syntaxe permet de créer un dictionnaire.

```haskell
data Address = { country :: String, zip :: Int}
```

Avec une variable de ce type nommé “test”, `country test` permet de récupérer ce champ

### Casting

```haskell
half :: Int -> Double
half n = (fromIntergral n) / 2
```

`show` & `read` pour les strings

### Classe de type

On peut regrouper plusieurs types sous une seule classe, qui définit toutes les fonctions à implémenter. Certaines peuvent être optionnelles s’il est possible de les déduire depuis d’autre \(par exemple, pour `Eq` on ne doit spécifier que `==` ou `/=`\). Cette information, et bien d’autre, peuvent être trouvées sur _hackage_. \(Pour une recherche plus facile, _Hoogle_\).

#### Syntaxe

```haskell
class TypeClassName a where
    funcName1 :: a -> a
    funcName2 :: a -> String
    funcName3 :: a -> a -> Bool
```

#### Classe de type principales

* `Num` Généralise l’idée d’un nombre
* `Eq` Généralise l’idée de l’égalité
* `Ord` Généralise l’idée de la comparaison/l’ordre
* `Bounded` Permet de définir des limites maximal et minimal pour le type \(par exemple les limites des int\).
* `Show` Permet d’afficher la variable
* `Read` Permet de lire la variable

#### Instance & dérivation

Le deriving vu dans le chapitre “Déclaration” des “Types” permet d’utiliser les fonctions d’une autre classe pour la sienne.

Si leur fonctionnements ne sont pas adapté mais que l’on veut quand même les implémenter, on peut en faire une instance \(aka, la surcharger\).

Syntaxe :

```haskell
instance TypeClass OurType where
    functionToImpletement param = implementation
```

Exemple :

```haskell
instance FromField SupportedCountry where
  parseField f
    | f == "France" = pure $ SupportedCountry France
    | otherwise = fail "unsupported country"
```

Il y a aussi tout un truc avec les `DerivingStrategies` mais j’y comprends pas grand chose.

### Kinds of type

Les `kinds` décrivent le nombre de type qu’un type prends en paramètre. On appelle cela _l’arity_ d’une fonction \(en anglais\).

* `Either :: * -> * -> *`
* `Maybe ::  * -> *`
* `Maybe Int :: *` Car c’est le type final. On appelle ça un **type concret**

On dit des types qui prennent d’autres types en paramètres qu’ils donnent un **contexte** à ceux-ci. `Maybe` par exemple sous-entend que la variable peut être manquante. Tandis que `IO` sous-entends qu’il peut y avoir tous les problèmes liés à des interactions extérieurs.

**Pour des questions de sécurité, on ne peut pas sortir d’un contexte définitivement**, mais un certains nombre de fonctions permettent de naviguer autour de cette règle.

## Test

### If..else

Le else est obligatoire

```haskell
Syntaxe : if test then code else code
```

### Switch

```haskell
func x y = 
    case (x > y) of
        False -> y
        True -> x + 10
```

### Guards

\[todo\]

## List

* Les listes sont déclarées entre \[ \]. 
* Pour rajouter un élément au début d’une liste,  utilisez “:” \(appelé “cons”\)  
  * `newVar : existingList` Ratoute l’élément au début
  * `newVar : [ ]` Crée une une liste.
* Le type `Vector` est favorisé par rapport aux `List` car c’est un tableau plutôt qu’une liste chainée \(donc généralement plus efficace\).

### Fonctions utile pour les traiter

```haskell
head :: [a] -> a
tail :: [a] -> [a]
(!!) :: [a] -> Int -> a -- Ressort l’élément à l’index demandé
length
reverse
elem :: (Eq a, Foldable t) => a -> t a -> Bool -- Vérifie l’existence de l’élément
take :: Int -> [a] -> [a]
drop :: Int -> [a] -> [a]
zip :: [a] -> [b] -> [(a, b)]
```

### Fonction utile pour les parcourir

```haskell
map :: (a -> b) -> [a] -> [b]
filter :: (a -> Bool) -> [a] -> [a]
foldl :: Foldable t => (b -> a -> b) -> b -> t a -> b
```

foldl réduit une liste à une seule valeur en appliquant une fonction `foldl (+) 0 [1, 2, 3, 4]` = 10

### List Comprehension

\[todo\]

### Générer des range

* `[1 .. 10]`
* En définissant le step : `[1,3 .. 10]`.
* Pour décrémenter, il faut préciser le step car il vaut 1 par défaut : `[1,0 .. -10]`

## Pattern matching

Cette méthode permet de définir le comportement d’une fonction par rapport à ces arguments plus simplement qu’avec un if..else

### Cas 1 : Enum

\[todo\]

### Cas 2 : Liste

Pour représenter une liste reçue en paramètre, on utilise la syntaxe `(x:xs)`, x représentant la tête de la liste, et xs le corps.

```haskell
func [] = []
func (x:xs) = doSomething
```

### Cas 3 : Tuple

```haskell
func (elem1, _, _) = elem1
func (_, elem2, _) = elem2
func (_, _, elem3) = elem3
```

Ceci permet d’extraire les éléments d’un tuple. “\_” est une wildcard.

## Pragma

Les pragma sont rajoutées au début du fichier et permettent de modifier le comportement du compilateur, ou plutôt ce qu’il peut accepter.

Voici une liste non exhaustive de celle qui reviennent souvent :

```haskell
{-# LANGUAGE OverloadedStrings #-}
```

Grâce à celle-ci, ghc comprends que ce qui est entre “ “ peut être converties en String, mais aussi en `Text`, `ByteString`, et tout autre type du genre.

```haskell
{-# LANGUAGE TypeApplications  #-}
```

Permet d’expliciter le type que doit avoir certaines variables sans rajouter toute la signature avec un `@type`.

Dans ghci elle permet aussi de faire ça :

\*Main&gt; :type \(&gt;&gt;=\) @Maybe @String \(&gt;&gt;=\) @Maybe @String :: Maybe String -&gt; \(String -&gt; Maybe b\) -&gt; Maybe b

## Concept généraux

* Vu que ce qui concerne IO enfreint souvent les règles des fonctions \(aka, retourner quelques chose, pas de side effect, etc\), on parle d’_IO Action_ et non de fonction.
* Une fonction qui gère tout les cas est appelée total, tandis qu’une qui n’en gère qu’une partie est partielle.
* Une fonction sans effet secondaire est appelée pure.
* Un type enum est un “sum type” tandis qu’un record est un “product type”. \(Le premier est un OR, le second un AND\).

## GHCI Options

* `:l file` Charge le fichier dans ghci
* `:t anything` Informe du type de la chose \(fonction ou variable\)
* `:info typeClass/Type` Donne des infos sur une classe de type/ un type
* `:q` Ferme ghci
* `:r` Reload
* `:k/:kind` Donne le kind d’une fonction

## Divers

### String & Text

En général, on préfère le type `Text` au `String` car c’est un tableau plutôt qu’une liste chainée. Il y a aussi des fonctions permettant de gérer l’unicode. Voir les pragma

### Tuple

Les tuples sont déclarés entre \(\).

Fonctions utiles :

```haskell
(,) :: a -> b -> (a, b)
fst :: (a, b) -> a
snd :: (a, b) -> b
```

### Lazy evaluation

Tout ce qui est `Lazy` \(fonctions ou types\) sera, comparé à tout ce qui est `Strict`, évaluer uniquement au dernier moment. Cela permet par exemple d’avoir une liste infinie \(du moment qu’on ne cherche pas à l’évaluer entièrement\).

### Combiner des fonctions

On peut le faire avec la fonction “.”

```haskell
(.) :: (b -> c) -> (a -> b) -> a -> c
```

## Cheat Sheet

### Semigroup

Permet de combiner un type avec lui-même au moyen de l’opérateur `<>`

Exemple d’implémentation pour un int :

```haskell
instance Semigroup Integer where
(<>) x y = x + y
```

Les dérivés de `Semigroup` doivent être associatifs \(on obtient toujours le même résultat, peut importe l’ordre dans lequel on combine les éléments\).

### Monoid

* Les Monoid sont simplement des Semigroup avec un “identity element” appelé `mempty`.
* `<>` est remplacé par `mappend`.
* Ils ont aussi `mconcat` dont l’implémentation est la suivante : `mconcat = foldr mappend mempty`

### Functor & Applicative

#### Implémentation

**Functor**

```haskell
fmap (<$>) : Functor f => (a -> b) -> f a -> f b
```

**Applicatives**

```haskell
<*> :: Applicative f => f (a -> b) -> f a -> f b
pure :: a -> f a
```

#### Utilisation

Imaginons un record Name qui prends 2 String \(prenom, nomFamille\), son constructeur sera de la forme : `String -> String -> Name`

Si j’ai une `Maybe String` que je veux utiliser pour créer un Name, je peux faire ceci :

```haskell
Name <$> Maybe String
```

Vu que chaque fonction ne prends en vrai qu’un seul argument, le **Name** équivaut à

```haskell
(String -> (String -> Name))
```

On se retrouve donc à la fin avec un `Maybe (String -> Name)`. L’applicative nous permet de gérer cet autre argument.

### Monad

#### Implémentation

```haskell
(>>=) :: Monad m => m a -> (a -> m b) -> m b -- Aussi appelé bind
(=<<) :: Monad m => (a -> m b) -> m a -> m b) -- Son jumeau
return :: Monad m => a -> m a - L’équivalent de pure
(>>) :: Monad m :: m a -> m b -> m b - Permet de se débarrasser d’un argument)
```

#### Do - notation

```haskell
func :: m a -> m b
func ma_var = do
  justA <- ma_var
  func justA
```

Le do est plus ou moins un bind plus facile d’utilisation : `<-` permet de sortir du contexte.

En occurrence, on aurait juste pu écrire `func =<< ma_var`. Mais pour des cas plus compliqué, c’est vite pratique.

### Either

Il y a différentes façon de gérer `Either`.

* Essayer d’en sortir rapidement
* Utiliser les `bifunctor`
* Utiliser `either`

## Sources

* Haskell Programming from first principles by Christopher Allen & Julie Moronuki
* [https://kodimensional.dev/recordwildcards](https://kodimensional.dev/recordwildcards)
* [https://artyom.me/aeson](https://artyom.me/aeson)
* Misc research

