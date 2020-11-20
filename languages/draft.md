# Haskell

## Disclaimer

I started learning haskell before i got into the habit of doing those notes. A lot of things are missing. Moreover, it's my first functional language, so i'm not sure what is specific to it, and what is specific to functional programming.

## Functions

### Generalities

* Functions must follow those rules :
  * Have at least 1 parameter
  * Return something
  * Always return the same thing for a given value
* Functions can be parameters, and they can be returned
* In reality all functions only have one parameter, but Haskell simplifies the syntax from `func :: arg -> (arg -> return)` to `func :: agr -> agr -> return`
* A function dealing with every cases is **total**, others are **partial**
* A function without secondary effects is **pure**
* You can combine functions with `.` :

  ```haskell
  (.) :: (b -> c) -> (a -> b) -> a -> c
  ```

* Anonymous functions \(lambdas\) are written like that :

```haskell
(\x -> x + 1) (2)
-- (\param_name -> expression) (param)
```

### Order of operation

* `$` replace parentheses : 

  ```haskell
  func1 $ func2 param = func1 (func2 param)
  ```

* Functions are always evaluated before operators
* You can declare an operator before its arguments : `(+) 2 3`
* You can place a function between its parameters like this : ``10 `div` 5`` 

### Closure & Partial application

* Closure consist in wrapping a variable inside a lambda
* Haskell does it automatically for partial application
* "Anytime you write a closure in haskell, you order the parameter from most to least general"
* Example :

```haskell
myFunc f = (\x -> f (x + 1))
```

* `myFunc` will wait for the `x` argument when it will be used
* In this form, it's not very useful because we could do it like that :
  * It becomes interesting when there's multiple cases

```haskell
myFunc f x = f (x + 1)
```

* Example :
  * Given an `getUrlRequest` function which takes an host, an api key and a resource
  * You can prepare in advance functions with the right host & key that can be used more easily

```haskell
googleBuilder = getUrlRequest “[www.google.com](http://www.google.com)”
amazonBuilder = getUrlRequest “[www.amazon.com](http://www.google.com)”

-- For API keys :
bookCall = amazonBuilder “1337hAsk311_”
```

## Variables

```haskell
-- With where
sumSquareOrSquareSum x y = if sumSquare > SquareSum
                      then sumSquare
                      else squareSum
  where sumSquare = x^2 + y^2
     squareSum = (x+y)^2

-- With let in
sumSquareOrSquareSum x y = let sumSquare = (x^2 + y^2)
                                           squareSum = (x+y)^2
                                          in
                                           if sumSquare > squareSum
                                           then sumSquare
                                           else squareSum
```

## Type

### Variable signature

```haskell
x :: Int
x = 2

values :: [Int]
values = [1, 2, 3]

streetAddress :: (Int, String)
streetAddress = (123, “Happy St.”)
```

### Function signature

* `->` separate each parameter and the return
  * The last element is always the return

```haskell
-- Basic :
double :: Int -> Int
double n = n*2

-- Multiple parameters :
makeAddress :: Int -> String -> String -> (Int, String, String)
makeAddress number street town = (number, street, town)

-- Function as parameter :
ifEven :: (Int -> Int) -> Int -> Int
ifEven f n = if even n
             then f n
             else n
```

* You can use **placeholders** for the types, like `a` or `b` and use classes to apply **constraints** to them
  * Declare the constraints before the `=>`

```haskell
print :: Show a => a -> IO ()
```

### Declaration

```haskell
-- Enum :
data Country = Switzerland | France
data Maybe a = Just a | Nothing
data TPError = ParsingError string | ConnexionError ServantError

-- Record (dictionnary) :
data Address = { country :: String, zip :: Int}
-- With a variable test of type Address
-- You can retrieve the country with : country test

-- Alias
type Name = KnownType

-- New type :
newtype Name = Constructor 
-- or :
newtype Name = Constructor  { unwrapFunction :: KnownType }
-- or : 
newtype Name = Constructor  { unwrapFunction :: KnownType } deriving (TypeClass1, TypeClass2, ..)
```

* An enum type is a "sum type" \(OR\), while a record is a "product type" \(AND\)
* To retrieve a member of a record, use its name : `country example` will return the country 
* Aliases are just another name for a type, its for example `String` for `[Char]`
  * If a function has this signature : `func -> alias1 -> alias2 -> type` and the aliases are from the same type, the compiler won't be able to detect it if we inverted them
* `newtype` Creates a "real" new type, meaning that in the previous example, the compiler will be able to throw an error
  * `unwrapFunction` \(which is a record of only one element\), enables you to retrieve the base type

### Casting

```haskell
half :: Int -> Double
half n = (fromIntergral n) / 2
```

* `show` & `read` for strings

### Classes

* You can group types into classes that define the function to implement
* Functions can be optional if they can be deduced from others
  * For example, with`Eq`, you must define either `==` or `/=`
  * Hackage or Hoogle can give you this information
* Syntax :

```haskell
class TypeClassName a where
    funcName1 :: a -> a
    funcName2 :: a -> String
    funcName3 :: a -> a -> Bool
```

* Main classes :
  * `Num` Generalize the idea of a number
  * `Eq` Generalize the idea of equality
  * `Ord` Generalize the idea of comparison/order
  * `Bounded` A type of this class has minimal & maximal limits \(for example the boundaries of an `int`\)
  * `Show` A type of this class can be printed
  * `Read` A type of this class can be read
  * `Lazy` Will be evaluated at the last possible moment \(in opposition to anything `Strict`\)
    * With this, we can have infinite lists as long as we don't try to evaluate them entirely
  * Some classes are detailed at the end of this summary
* The `deriving` seen before lets you use functions from another class
  * You can overload function with `instance`
  * There's also `DerivingStrategies` but i don't understand it
  * Syntax :

```haskell
instance TypeClass OurType where
    functionToImpletement param = implementation
```

### Kinds

* The kind describe the number of types a type takes as parameter 
  * This is the _arity_ of a function 
* Example :
  * `Either :: * -> * -> *`
  * `Maybe ::  * -> *`
  * `Maybe Int :: *` This one is the final \(**concrete**\) type
* Types using other types are said to give a **context** to those
  * For security reasons, you can't definitively leave a context, but there are temporary workarounds

## Data Structure

### List & Array

* List are declared between `[ ]`
* To add an element at the start of the list, use a _cons_  `:` 
  * `newVar : existingList` Add the element at the start of the list
  * `newVar : [ ]` Create a list
* The `List` type is used for linked list
* The `Vector` type is used for array
* You can generate a range like that : `[1 .. 10]`
  * With a step : `[1,3 .. 10]`
  * To decrement, you must specify the step : `[1,0 .. -10]`
* Useful functions :

```haskell
head :: [a] -> a
tail :: [a] -> [a]
(!!) :: [a] -> Int -> a -- Return the element at this index
length
reverse
elem :: (Eq a, Foldable t) => a -> t a -> Bool -- Verify the existence of the element
take :: Int -> [a] -> [a]
drop :: Int -> [a] -> [a]
zip :: [a] -> [b] -> [(a, b)]

-- Function to traverse them :
map :: (a -> b) -> [a] -> [b]
filter :: (a -> Bool) -> [a] -> [a]
foldl :: Foldable t => (b -> a -> b) -> b -> t a -> b
-- foldl reduce a list to one value by applying a function to each element
-- For example :
foldl (+) 0 [1, 2, 3, 4] -- = 10
```

{% hint style="info" %}
Speak of List Comprehension
{% endhint %}

### Tuple

* Tuples are declared between `()`
* Useful functions :

```haskell
(,) :: a -> b -> (a, b)
fst :: (a, b) -> a
snd :: (a, b) -> b
```

## Condition

```haskell
-- If...else
if test then code else code

-- Switch :
func x y = 
    case (x > y) of
        False -> y
        True -> x + 10
        
        
-- Pattern matching :

-- List : 

-- An empty list is given :
func [] = []
-- A list is given
-- x is the head of the list
-- xs its body
func (x:xs) = doSomething


-- Tuples :

-- This is how you can extract its element,"_" is a wildcard :
func (elem1, _, _) = elem1
func (_, elem2, _) = elem2
func (_, _, elem3) = elem3

-- Enum :
-- To Do
```

{% hint style="info" %}
* Add  :
  * guards
  * pattern matching on enum
{% endhint %}

## GHCI Options

* `:l <file>` Load the file
* `:t <function | variable>` Gives the type
* `:info <type_class | type>`
* `:q` Quit
* `:r` Reload
* `:k/:kind` Give the kind of a function

### Pragma

* Pragmas are added at the start of the file to modify the behavior of the compiler 
* `{-# LANGUAGE OverloadedStrings #-}` With this, ghc will understand that the content of `" "` can be converted to `Text`, `ByteString`, etc in addition to `String`
* `{-# LANGUAGE TypeApplications  #-}` Lets you define the type that some variable must have without having to give the full signature
  * With that, in ghci you can do this :

```haskell
*Main> :type (>>=) @Maybe @String (>>=) @Maybe @String :: Maybe String -> (String -> Maybe b) -> Maybe b
```

## Misc

* Since most things linked to IO violate the rules about the function, we speak of **IO action** instead of function
* `Text` is preferred to `String` because its an array instead of a linked list
* There's some functions to deal with unicode, check pragmas

## Important classes

### Semigroup

* Lets you combine a type with itself with the `<>` operator 
* Types deriving `Semigroup` must be associative, meaning that we obtain the same result no matter the order in which we combine the elements
* For example this is the implementation for an int :

```haskell
instance Semigroup Integer where
(<>) x y = x + y
```

### Monoid

* `Monoid` are `Semigroup` with an identity element, called `mempty`
* `<>` is replaced by `mappend`
* `mconcat` is added, its implementation is :  `mconcat = foldr mappend mempty`

### Functor & Applicative

#### Implementation

```haskell
-- Functor :
fmap (<$>) : Functor f => (a -> b) -> f a -> f b

-- Applicative :
<*> :: Applicative f => f (a -> b) -> f a -> f b
pure :: a -> f a
```

#### Usage

```haskell
-- Given this record :
data Name = { forname :: String, surname :: String }

-- If i want to create a Name from a Maybe String, I can do this :
Name <$> Maybe String
-- But since Name constructor is actually that :
(String -> (String -> Name))
-- We will end up with this and lose a parameter :
Maybe (String -> Name)

-- Applicatives lets us deal with this parameter
```

### Monad

#### Implementation

```haskell
(>>=) :: Monad m => m a -> (a -> m b) -> m b -- Also called bind
(=<<) :: Monad m => (a -> m b) -> m a -> m b)
return :: Monad m => a -> m a -- Like pure
(>>) :: Monad m :: m a -> m b -> m b -- Lets you get rid of a parameter
```

#### Do - notation

```haskell
func :: m a -> m b
func myVar = do
  justA <- myVar
  func justA
```

* The `do` is a simplification of the `bind`
  * `<-` Lets you exit the context
* In the above example, we could have simple done `func =<< my_var`, but for more complicated cases, its useful

### Either

* There's 3 way to deal with `Either` :
  * Get out of it quickly
  * Use `bifunctor`
  * Use `either`

## Sources

* _Haskell Programming from first principles_ by Christopher Allen & Julie Moronuki
* [https://kodimensional.dev/recordwildcards](https://kodimensional.dev/recordwildcards)
* [https://artyom.me/aeson](https://artyom.me/aeson)

