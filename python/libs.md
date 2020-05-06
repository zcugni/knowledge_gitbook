# Libs

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
