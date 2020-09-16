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

## Misc

* MariaDb tourne sous mySQL

