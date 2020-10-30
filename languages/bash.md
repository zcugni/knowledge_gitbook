# Bash

## Terminologie

### Bash, shell, terminal, etc

Bash \(_Bourne-Again-Shell_\) est le shell \(command line interpreter\) de l'os GNU. Il est compatible avec `sh` et intègre des fonctionnalités du _Korn Shell_ `ksh` et du _C Shell_ `csh`.

Shell est un _macro processor that executes commands_. Un shell unix est à la fois un _command interpreter_ et un langage de programmation.

> Terminal, shell & command line are often used interchangeably to indicate a text based system for navigating your OS. Command line is very windows centric terminology, terminal is very mac centric.

### Interactive, login & pseudo shell

**Interactive** shell are simply shells. We call them that for cmd that generate them. For exemple, sudo can either send a cmd to a shell and just give back the result, or return an interactive shell to use.

**Login** shell are interactive shell that simulate the state you get at login. So it clears env var, read login files like `.profile`, etc

**Pseudo**-shell \(or pseudo terminal ?\) are independent from the process/session that spawns them, so there's less security issue. The spawner act as a proxy \(i guess\). \(It's kind of like a sandbox ?\)

## Shebang

* Little com at the start of the script that indicates to the machine the path to the interpreter to use \(if it hasn't been specified, like when you launch in that way :`./script`\)
* Generally, use `#!/bin/bash`

## Variable

* Assignment : `var="value"` \(Won't work with spaces because if will search for the cmd name\)
* Call :

```bash
echo "str is $var ." # On peut pas coller de caractères au nom de la var
echo "str is ${var}s." # On peut
```

* environment variable / constant in CAPS
* To put a command in a var do `x=(printf "test");` \(test avec un $ devant la paranthèse si ça marche pas\)
* `$?` Return value of last cmd
* `$#` Number of args in the script
* `$0` Name of the script
* `$1..$n` Script arguments
* `$@` Liste des paramètres

## Quoting

```bash
echo 'won t be interpreted'
echo "\$,\`,\\ will be interpreted"
echo "this is a `cmd_to_be_interpreted`."
```

## Fonctions

```bash
function_name(){
    # code
}

function function_name(){
    # code
}
```

* On peut l'utiliser avec ou sans le keyword `function`
* A l'intérieur d'une fonction `$1` représente le premier paramètre de celle-ci et non du script
* On les appelles directement sans \(\) : `echo "test"`.
* La valeur de retour est celle de la dernière commande.

## Loops

```bash
until test; do cmd; done

while test; do cmd; done

for var in list; do cmd; done

# if no list is specified, it will iterate the parameters
for parameter; do cmd; done 

# ; can be replace by a newline
```

## Condition - If

### Généralités

* Il y a plusieur syntaxe permettant d'écrire un if, plus ou moins vielles et plus ou moins complètes.
* Tout les interpreter \(bash, sh, zsh, etc\), ne réagissent pas tous de la même façon. Les explications ci-dessous fonctionne pour **bash.**
* J'ai fait un [script de test](https://github.com/zcugni/misc_script/blob/master/test.sh) pour vérifier le comportement. Use it with `bash test.sh`, `sh test.sh`, etc to see the difference
* Généralement, utiliser cela :

```bash
if [[ condition ]]; then
    # cmd...
elif [[ condition ]]; then
    # cmd...
else
    # cmd...
fi
```

* `elif` & `else` optionnels
* `;` peut être remplacé par un retour à la ligne

### Type de test

* Attention, si on utilise un symbole non géré, il n'y aura pas forcément d'erreur, mais le résultat pourra être erroné.
* `if [[ condition ]]`
  * Les espaces après le `[` et avant le `]` sont nécessaires
  * Opérateurs pour les strings : `=`, `==`, `>`, `<`
  * Opérateurs pour les nombres : `-eq`, `-ne`, `-gt`, `-lt`, `-ge`, `-le`, `=`, `==`, `!=`, `>`, `<`
  * `&&`, `||`
  * Not POSIX compliant but supported by bash
  * Support regular expression
* `if (( condition ))`
  * Utilisé uniquement pour les comparaisons numériques \(y'aura pas forcément d'erreur pour les strings, cf le warning d'en dessus\)
  * `==, !=, >, <, >=, <=`
  * `&&, ||`
* `if [ condition ]`
  * Old \[\[ \]\] version, alias to `test`
  * Les espaces après le `[` et avant le `]` sont nécessaires
  * Opérateurs pour les strings  : `=`, `==`
  * Opérateurs pour les nombres : `-eq`, `-ne`, `-gt`, `-lt`, `-ge`, `-le, =, ==, !=`
  * `-o`, `-a`
  * If one part of the equation evaluate to an empty string, it won't work
  * Si une variable contient une chaine avec des espaces, il faut l'entourer de `"` :
  * ```bash
    file="file name"
    [ -f "$file" ] && echo "$file is a regular file"
    ```
* Il existe aussi des symboles particulier permettant de travailler avec des fichiers, par exemple `-e` ou `-nt` mais je ne les ai pas encore utilisé

### Test avec commande

* Il existe aussi des syntaxes mélangeant `if` avec une commande, mais ce n'est pas tout à fait la même chose.
* `if (command)`
  * Runs the command in a subshell. When it completes, it sets an exit code and the if statement acts accordingly.
  * Used to limits side-effects for commands that need special var or other changes to the shell's environment. Those disappear with the subshell
* `if command` The command is executed and the if statement acts accordingly to its exit code.

## Condition - Case

```bash
case $var in
  pattern1 | pattern2 | patter3) cmd;;
  pattern4 | pattern5 ) cmd;;
  *) cdm;;
esac
```

* Chaque pattern est séparé d'un `|`
* Une liste de pattern est terminée par `)`
* `*` est le cas par défaut
* `;;` si ça match, on arrête après avoir exécuter les commandes de cette clause
* `;&` si ça match, on exécute en plus les commandes de la prochaine clause
* `;;&` si ça match, tester la prochaine clause après avoir exécutée celle-ci
* Si aucun pattern n'a match, return 0, sinon retourne le status de la commande

## Condition - Select

Commande un peu spéciale permettant de créer des menus :

```bash
select brand in Samsung Sony iphone symphony Walton
do
echo "You have chosen $brand"
done
```

## Redirection

Each process being run has 3 channels

| Name | Complete Name | File Descriptor | Default |
| :--- | :--- | :--- | :--- |
| `STDOUT` | Standard output | 0 | The shell |
| `STDIN` | Standard input | 1 | The keyboard |
| `STDERR` | Standard error | 2 | The shell |

On peut rediriger avec `>` et `<` :

* `cmd1 > cmd2` ou `cmd1 1> cmd2` Output de cmd1 en input de cmd2
* `cdm1 2> cmd2` STDERR de cmd1 en input de cmd2
* `cmd1 < cmd2` Output de cmd2 en input de cmd1
* `cmd1 >> file` Append to the file instead of replacing it
  * You can also use it with  `2>>`
* `cm1 &> file` Redirects both STDOUT & STDERR \(équivalent à `>&` et `2>&1`\)

Et on peut chaîner avec `|` : `time cmd1 | cmd2 | ...`

* Output of first cmd is fed as input to second cmd, etc
* `time` is optional and gives the total time of the operation
* Each cmd is executed in it's own subshell
* `|&` will also feed STDERR

## Run une commande

* Directement son nom et ces params : `uname -a`
* Entre ````` 
* `$(command)`

## Chaîner des commandes

* `cmd1 && cmd2` cmd2 is executed only if cmd1 succeded 
* `cmd1 || cmd2` cmd2 is executed only if cmd1 failed 
* `cm1; cmd2;` cmds are executed sequentially



## Script

* `exit nb` Exit the script and returns nb \(0 being success\) 
* `exit` Exit the script and use it's last cmd return value as the script return value 
* `source script.sh` Will run the script and add it's variable to your current shell \(`. script.sh` will do the same\)

## Sources

* [TutoriaLinux](https://www.youtube.com/channel/UCvA_wgsX6eFAOXI8Rbg_WiQ)
* [http://mywiki.wooledge.org/BashFAQ/031](http://mywiki.wooledge.org/BashFAQ/031)
* Stack Exchange \(principally [this](https://unix.stackexchange.com/questions/32210/why-does-parameter-expansion-with-spaces-without-quotes-work-inside-double-brack) and [this](https://unix.stackexchange.com/questions/278707/comparing-integers-arithmetic-expression-or-conditional-expression)\)
* Misc research

