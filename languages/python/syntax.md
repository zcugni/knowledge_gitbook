# Syntax

## General

* Declare variables without defining a type
* `# This is a comment`
  * There's no multi-line, except Docstrings, but they shouldn't be used for that

## Operators

### Numerical

| Symbol | Definition |
| :--- | :--- |
| + | Addition / Concatenation |
| \* | Multiplication \(number & strings\) |
| - |  |
| / |  |
| // | Floor division \( 5 // 2 = 2\) |
| % | Modulo |
| \*\* | Exponent |

### Comparison

* `==` && `!=`
* `<` && `>`
* `<=` && `>=`

### Condition

* `and`
* `or`
* `not` / `is not`
* `in` Check if an element is in the list, also used if for loop

## Conditions

* There's no switch

### if - elif - else

```python
if test:
    #code
elif test:
    #code
else:
    #code
```

#### Uses of not

```python
if "." not in str:
    # code

if var is not np.nan:
    # code

if not (elem1 or elem2 or elem 3):
    # code
```

### Tertiary

```python
var = "something" if test else "something else"

# You can put tertiary into printf :
print("Splitting", total_candies, "candy" if total_candies == 1 else "candies")
```

## Loop

### for

```python
# Basic
for var_name in iterable_object:
    # code

# With a range (the start is 0 by default)
for var_name in range(start, end):
    # code

# With an index
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

* In between `"` or `'`
* You can't modify them

### Useful functions

* `.upper()` && `.lower()`
* `.index(“substring”)`
* `.split()`
* `.join()`
* `len(str)`

### Concatenation

* With `+`
* With `format` : `"asdqwr {0} wrew {1} {1}"".format(var0, var1)`
  * The index is optional if the arguments are in the right order
* To concatenate with other types, you must first cast : `str(var)`

## Function

### Declaration

```python
def example_name(arg1, arg2, arg3 = default_value):

    """Documentation

    [....]

    >>> exemple

    """
    # code
    return val
```

* Lines under a declaration must be indented  
* You can create optional parameters by giving them a default value
  * They must be at the end of the list
* You can pass a function as argument by giving its name without \(\)
* It isn't possible to pass by reference

### Call

* `example_name(arg1, arg2, arg3)`
* `example_name(arg1, arg2)` Default value for arg3
* `example_name(agr2, arg3 = val, arg1 = val)` The order of the arguments is unimportant
* To unpack \(get the element one by one of\) a list/tuple : `func(arg1, *list_or_tuple)`

### Return

* `None` by default, not mandatory
* parenthesis optional
* You can return multiple values with tuples

```python
def exemple(param):
    #code func
    return (nb1, nb2)

plop, plip = example(3)
```

### Anonymous function

* An anonymous function doesn't have a name and therefor is used only once as a parameter
* For example, an `.apply()` of the panda lib : 

```python
lambda x : ast.literal_eval(x) if not pd.isna(x) else []
```

## Class

### Generalities

* Everything in python is an object
* You can call a function of the parent class with `super()`
* To access its function, use `self`

### Syntax

```python
class Name:
    class_variable

    __init__(self, param, ..):
        self.instance_variable
        #code

    def func1(self, ...):
        #code

    @staticmethod
    def func2(....):
        #code
```

### Variable & Functions

* Variables & functions are accessed via `.`
* Class variable are declared at the root of the classe and are shared with each instance
* Instance variable exist only for their instance and are access with `self.<var_name>`
* Function of their own class are accessed with `self.<func_name>(...)`
* The first parameter of a function is `self`, present is the declaration but not when we call it
  * If you don't need `self`, write `@staticmethod` over it

### Module & package

* Modules are files in which are defined functions/variables, etc
  * Their name depends on the name of the file
  * Those are the ones you can `import`
* Packages are directories and sub directories containing those modules
* Example :

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

## Data structure

### List

* Declaration : `list = [element1, element2, element3]`
* The element can be of any type and of mixed type
* Access a list element like you would an array
  * To access element at the end, use negative index
* There's 2 syntax : 

```python
hands = [
    ['J', 'Q', 'K'],
    ['2', '2', '2'],
    ['6', 'A', 'K'], # (Comma after the last element is optional)
]

hands = [['J', 'Q', 'K'], ['2', '2', '2'], ['6', 'A', 'K']]
```

#### Slicing

* Basic syntax :`[start_rows:end_rows, start:col:end_col]` 
* You can't use negative index
* You don't have to write a starting `0`
  * Nor an ending `:` if you take everything
* To select multiple indexes that aren't continuous :

```python
df[[in1, ind4, ind7], [col2, col4, col9]]

# Or 
cols = [col2, col4, col9]
rows = [ind1, ind4, ind7]
df[rows, cols]
```

#### List comprehension

* You can directly apply a condition or a loop to a list : 

```python
loud_short_planets = [planet.upper() + '!' for planet in planets if len(planet) < 6]
```

> `['VENUS!', 'EARTH!', 'MARS!']`

#### Useful functions

* `len()`
* `sorted()` It sort them
* `sum()`
* `max()` && `min()`
* `.append()`
* `.pop(index)` Remove & return the element
* `.index(elem_name)` Give the index of an element
* `.remove(value)` Delete the first instance of a value

### Tuples

* Tuples are like list except for 2 things :
  * Declare them in parentheses :  `t = (1,2,3)`
  * They can't be modified
* Assign the value of a tuple to variables : `var1, var2 = tuple`

### Dictionaries

* Declared between `{}`

```python
nb = {"one" : 1, "two" : 2, "three" : 3}
```

* To add an element, give its key & value :

```python
nb["eleven"] = 11
```

* `.values()` Get the values
* `.keys()` Get the keys
* `.items()` Get both

#### Dictionary comprehension

* Similar to list

```python
planet_initial = {planet: planet[0] for planet in planets}
```

> {'Mercury': 'M', 'Venus': 'V', 'Earth': 'E', 'Mars': 'M'}

#### Loop on directory

* A loop on a directory uses its keys as index :

```python
for k in numbers:
    print("{} = {}".format(k, numbers[k]), end = ' ')
```

> one = 1 two = 2 three = 3 eleven = 11

* With `.items()`, you can get the keys and the index :

```python
for key, val in dict.items():
    #code
```

## Time Conversion

* You can transform date using this format : `Wed, 22 Apr 2020 16:02:51 GMT` to a `struct_time` :

```python
# [...]
# Get the date of the server in the correct format
serverTime = response.headers["Date"]
format = "%a, %d %b %Y %H:%M:%S %Z"
time_tuple = time.strptime(serverTime, format)
```

* You can also transform a timestamp epoch : `calendar.timegm(time_tuple)`

## Misc

* You can explore an object with`dir(obj)` to get the list of its variables

