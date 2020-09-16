# BDD Language differencies

### Erreurs

> You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ‘\’’ at line 1 MySql
>
> ORA-00933: SQL command not properly ended Oracle
>
> Microsoft SQL Native Client error ‘80040e14’ Unclosed quotation mark after the character string MS SQL Server
>
> Query failed: ERROR: syntax error at or near “’” at character 56 in /www/site/test.php on line 121. PostGre SQL

### Technique de concaténation

| DB Name | Contatenation |  |  |
| :--- | :--- | :--- | :--- |
| MySql | `‘test’ + ‘ing’` |  |  |
| SQL Server | `‘test’ ‘ing’` |  |  |
| Oracle | \`‘test’ |  | ’ing’\` |
| PostgreSQL | \`‘test’ |  | ’ing’\` |

### Commentaires

MySQL propose des commentaires qui sont ignorés par les autres DBMS : `/*! and 1=0 */`. S’il réagit c’est donc que c’est un serveur MySQL.

