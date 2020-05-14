---
description: 'Disclaimer : This is an old and not up to current standard note'
---

# PHP

## Variable

* Commence par un $ puis une lettre. 
* Ensuite lettre et chiffre, mais pas de caractères spéciaux.
* Le type est deviné et peut changer en cours d’exécution.
* Constante : `DEFINE("NOM_CONST", "valeur");`
  * On l'écrit en majuscule. Il n’y a pas de $, même quand on l'appelle.

## String

* Même si on peut faire `$string[0]`, les strings ne sont pas des tableaux de lettres -&gt; `count()` inutile.
* Si on entoure une chaîne par `" "` ce qui est dedans est interprété, au contraire de `' '`.
* Concaténation avec `.`
* Ecrire `$_SESSION["bla"]` entre des " " : `${_SESSION["bla"]}`

## Array

* Les tableaux php sont des listes
* Déclaration

  ```php
  $test[0] = ‘blabla’; $test[1] = ‘bla’;

  // ou
  $test = array(‘donnee1’, ‘donnee2’, ‘etc’);

  // associatif
  $test = array(
        ‘var1’ => $donne1,
        ‘var2’ => $donne2,
        // ...
  );
  ```

* Ajouter une valeur à la fin du tableau : `$nomTableau[] = valeur;`

## Foreach

* `Foreach (array_expression as $value)`
* `Foreach(array_expression as $key => $value)`

## Fonction

```php
function functionName($argument) {
    //code
    //return $variable; if needed
}
```

## Sessions

* Écrire au début de chaque page : `session_start();`
* Définir des variables de sessions : `$_SESSION[‘nomVariable’] = ‘test’;`
* Fermer une session : `session_destroy();`

## Documentation

On peut générer une documentation automatiquement au moyen du [phpDocumentator](https://www.phpdoc.org/) si on utilise les mots clé de type `@param`, `@return`, etc.

## PDO

### Connexion

```php
function myPDO(){
    static $pdo = null;
    try{
        if($pdo == null){
            $db = DB_NAME;
            $host = HOST;
            $us = USER;
            $pw = PW;
            $pdo = new PDO('mysql:dbname=' . DB_NAME . ';dbhost=' .         HOST, USER, PW, array(
                    PDO::MYSQL_ATTR_INIT_COMMAND => "SET NAMES utf8",
                    PDO::ATTR_PERSISTENT => true
            ));
        }
    }catch(Exception $e){
        return $e;
        exit;
    }
    return $pdo;
}
```

* Le début de la ligne `mysql` est je crois ce qu’on appelle de DSN et varie en fonction du type de base de donnée. Aller [ici](http://php.net/manual/en/pdo.drivers.php) pour toutes les bases de données.
* PDO ::ATTR\_PERSISTENT permet de rendre la connexion persistante pour ne pas en re-créer une à chaque requête.

### Requête SQL avec input de User

```php
<?php
$sql = 'SELECT name, color, calories
        FROM fruit
        WHERE calories < :calories AND color = :color';
$sql = $pdo->prepare($sql);
$sql->bindParam(‘:calories’, ‘150’, PDO::PARAM_INT);
$sql->bindParam(‘:color’, ‘red’);
$sql->execute();
return $sql->fetchAll(PDO::FETCH_ASSOC);
?>
```

* `prepare` est une optimisation
* Placeholder : `:nom` ou `?`
* On ne peut pas mettre de placeholder après le FROM car il doit préparer la requête et pour cela connaitre la table concernée.
* Pour `bindparam` utiliser le numéro du paramètre si on a mis des `?`
* `bindParam` Passe une référence de la variable
* `bindValue` Passe sa valeur
* `fetch()` Sort le prochain résultat
* `fetchAll()` Sort un tableau de tous les résultats

#### Fetch Style

* `PDO::FETCH_BOTH (default)` Retourne un array associatif et indexé
* `PDO::FETCH_ASSOC` Retourne un array associatif
* `PDO::FETCH_NUM` Retourne un array indexé

## Autre

* `header("Location : page.php");` -&gt; redirige, doit être suivis de exit
* `exit([message])` : output le message s’il y en a un et quitte le script
* `include(‘file_path’)`
* `require(‘file_path’)` oblige l’inclusion du fichier et enverra une erreur si cela échoue.
* `include_once(‘file_path’)` et `require_once(‘file_path’)`.
* `===` existe
* `empty()` test si une variable est vide \(chaine vide, 0, null\) tandis que `isset()` test si elel est définie.
* Sécu : `htmlspecialshars()`, `filter_input()`, ...

## Source

* PHP's [doc](https://www.php.net/docs.php)

