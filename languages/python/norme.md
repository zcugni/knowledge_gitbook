# Style guide

* This is based on the most popular PEP guidelines

## General

* Assignation of a function with parameters to a variable :

```python
foo = long_function_name(var_one, var_two,
                         var_three, var_four)
```

* Declaration of a function with parameters :

```python
def long_function_name(
        var_one, var_two, var_three,
        var_four):
    print(var_one)
```

* Condition :

```python
if (this_is_one_thing
        and that_is_another_thing):
    do_something()
```

* List declaration :

```python
my_list = [
    1, 2, 3,
    4, 5, 6,
]
```

* Operator location :

```python
income = (gross_wages
          + taxable_interest
          + (dividends - qualified_dividends)
          - ira_deduction
          - student_loan_interest)
```

## White spaces

* Around the `:` of slices when they separate other things than numbers :

```python
ham[1:9], ham[1:9:3], ham[:9:3], ham[1::3], ham[1:9:]
ham[lower + offset : upper + offset]
```

* Around assignement & comparison operators
* For numerical operators, around the ones with less priority :

```python
x = x*2 - 1
c = (a+b) * (a-b)
```

* NOT before default value/return :

```python
def complex(real, imag=0.0):
    return magic(r=real, i=imag)
```

* After the `#` of a comment

## Empty lines

* Surround top-level function and class definitions with 2 blank lines
* Method definitions inside a class are surrounded by a single blank line
* Extra blank lines may be used \(sparingly\) to separate groups of related functions
* Blank lines may be omitted between a bunch of related one-liners \(e.g. a set of dummy implementations\)
* Use blank lines in functions, sparingly, to indicate logical sections

## Import

* At the start of the document
* Order : standard, third party, ours

{% hint style="info" %}
I wrote that but i have no idea what i meant : "Sur plusieurs ligne pour les “import” avec des virgules pour les “from.. import” "
{% endhint %}

## Docstring

* Document a public function
  * For private ones, use comment
* Placed after the line of the declaration 
* Those are the Google's guidelines :

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

## Naming convention

* Module : Short lower-case name underscore
* Class : CamelCase
* Function / Variable : lowercase with underscore
* Constant : Uppercase with underscore
* If the name we want to give is reserved, add `_` at the end

## Misc

* 79 chars by lines
  * 72 for Docstrings
* Spaces/tabs of 4
* Use string methods instead of string module \(more efficient\)
* Use `.startswith()` & `.endswith()` instead of string slicing to check for prefixes or suffixes \(cleaner and less error prone\) :

```python
Yes : if foo.startswith('bar'):
No :  if foo[:3] == 'bar':
```

* Object type comparisons should always use `isinstance()` instead of comparing types directly :

```python
Yes: if isinstance(obj, int):
No:  if type(obj) is type(1):
```

