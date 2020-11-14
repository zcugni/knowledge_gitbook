# Pandas

## Introduction

* Proposes 2 types :
  * `Series` : 1D arrays \(like vectors\)
  * `DataFrame` : Multiple dimensions \(like matrices\)

## Useful functions

* This a is list of useful functions :
  * `.isin([“col_name1”, “col_name2”])`
  * `.median()` & `.mean()`
  * `.unique()`
  * `.value_counts()`
  * `.idxmax()` Return the index where the value is the highest
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
  * `.at` Necessary to modify a precise value in a loop
  * `.isna()`
  * `df.sample(10)`
  * `df.head()`
  * `df.tail()`
* WARNING : Set `inplace=true` to drop, rename, fillna, etc.
* For both DataFrames & Series, if you apply a function to it, it will by default be applied to each entry
  * This does not work tests
  * Example :

```python
df[“col”] * 2
add_FR(df[“col”])
func_with_params(df[“col”], params)
df1 + df2
```

{% hint style="info" %}
Since we can do it directly like that, i'm not sure of the utility of `.apply()`
{% endhint %}

## Series declaration

* `pd.Series(val, index, [name])`
  * `val :`
    * Unique values \(will be the default one\)
    * A list
    * Optional if `index` is present
  * `index` :
    * List of strings or int
    * Optional if `val` is present
    * `range()` can be useful to generate a list of continuous numbers
  * `name` :
    * I think that if it's added to a DataFrame, it will be the name of the new column

## DataFrame

### Declaration

* `pd.DataFrame(<dict | array>, columns, [index])`
* `dict` : `{ “col” : [val_lst] }` 
* `array` : `[ [val_col_1], [val_col_2] ]` 
* `columns` : Only mandatory with an array 
* `index` : By default a list of continuous numbers

### Access

* You can do this :

```python
df[“col_name”]
df.col_name
df[“col_name”][index]
```

* But locs are more precise and allows slicing :
  * `.iloc[]` : Slice like the python standard : only numbers, upper limit exclusive
  * `.loc[]` : Slice like the pandas lib : Inclusive of both limits, allows selection of names & conditions
  * `.at[row, col_name]` : Allows access to a specific element
* Condition : 
  * `df[df.col_name == “something”]`
  * `df[ (test1) & (test2)]`

### Combine DataFrames & Series

* Series + Series : `new = s1.append(s2, ignore_index=true)`
* DataFrame + Series to the end of column : `new = df[“col”].append(s1)`
* DataFrame + Series as column : `df[“col”] = s1`
* DataFrame + DataFrame as new row : `new = df.append(df)`

### Grouping

* You can group data on a column
* For example, group data in function of the price
* You can use the usual functions on groups
* Example :

```python
best_rating_per_price = reviews.groupby('price')['points'].max().sort_index()
```

### Read & Write

```python
df = pd.read_csv("path", index_col = 0)
df.to_csv("/path/to/file.csv")
```

