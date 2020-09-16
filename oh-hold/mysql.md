---
description: 'Disclaimer : Not complete'
---

# MySQL

## Command-line

From the console, you can use normal SQL request. Use `exit;` to exit it.

### Launch "console"

* `mysql`
  * `-u user`
  * `-D db_name`
  * `-p` \(Ask for the password through the tty\)

### Show strucutre

\(When in console, be mindful of the `;`\)

* `show tables;` Shows tables names
* `describe table_name;` Shows specific table rows

## Information Gathering

* Version :
  * `@@version`
  * `VERSION()`
* Database name : `DATABASE()`
* La vue INFORMATION\_SCHEMA nous donne ce genre d’info :

![](../.gitbook/assets/information_schema.png)

### Single Quote Escape

* If the db use the GBK charset \(simplified chinese\) and not the application, using `\xBF'` \(URL-encoded as `%bf%27`\) will prevent `addslashes` \(php function\) from correctly escaping the quote
  * Explained  [here](http://shiflett.org/blog/2006/addslashes-versus-mysql-real-escape-string)

### Read / Write

* We can write with `into outfile 'path'`
* You can read with `load_file(‘name’)`
* You can't bypass the `'` in this case so if they're escaped it won't work

### Misc

* You can't accumulate request with  `;`
* There's 2 types of users : `USER() &` `CURRENT_USER()`
* Comments : `--` \(The space is important\) ou `#`
* MariaDB runs with MySQL

