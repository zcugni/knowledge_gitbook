# Bash

## Terminologie

Bash \(_Bourne-Again-Shell_\) est le shell, ou _command line interpreter_ pour l'os GNU. Il est compatible avec `sh` et intègre des fonctionnalités du _Korn Shell_ `ksh` et du _C Shell_ `csh`.

Shell est un _macro processor that executes commands_. Un shell unix est à la fois un _command interpreter_ et un langage de programmation.

> Terminal, shell & command line are often used interchangeably to indicate a text based system for navigating your OS. Command line is very windows centric terminology, terminal is very mac centric.

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
#!/bin/bash
# The above line is called a she-bang

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

Différent type de test, structure de base :

```bash
if cmd1; then
    cmd2
elif cmd3; then
    cmd4
else
    cmd5
fi
```

* `elif` & `else` optionnels
* `;` peut être remplacé par un retour à la ligne

### Type de test

**if \[ condition \]**

Old POSIX test \(so it's portable\)

**if \[\[ condition \]\]**

Upgraded but not POSIX test \(so not portable\). Same but better \(among its extended features, it can test whether a string matches a regular expression\).

**if \(\(condition\)\)**

Another ksh extension that bash and zsh also support \(but not POSIX\). This performs arithmetic. It returns an exit code of zero \(true\) if the result of the arithmetic calculation is nonzero.

**if \(command\)**

This runs command in a subshell. When command completes, it sets an exit code and the if statement acts accordingly.

A typical reason for using a subshell like this is to limit the side-effects of a command if it requires variable assignments or other changes to the shell's environment. Such changes do not remain after the subshell completes.

**if command**

Command is executed and the if statement acts according to its exit code.

### Opérateurs

**String comparaison**

| Old | New |
| :--- | :--- |
| `>` \(pas systématique\) | `>` |
| `<` \(pas systématique\) | `<` |
| `=` | `=` ou `==` |
| `!=` | `!=` |

**Integer comparaison**

| New & Old | Sens |
| :--- | :--- |
| `-gt` | greater than |
| `-lt` | lesser than |
| `-ge` | greater of equal |
| `-le` | lesser or equal |
| `-eq` | equal |
| `-ne` | not equal |

**Conditions**

| Old | New |
| :--- | :--- |
| `-a` and, deprecated | `&&` |
| `-o` or, deprecated | ````` |
|  | `!` |

**Special**

Commun :

* `-f`
* `-s`
* `-n`
* `-z`

Spécifique au nouveau :

* `-e file` File or directory exist
* `file1 -nt / -ot file2` File1 is newer/older than file2
* `file1 -ef file2` Files are the same

**Autre**

Quand une variable contient des strings séparées par des espaces, avec l'ancien test il fallait l'entouré de " " :

```bash
file="file name"
[ -f "$file" ] && echo "$file is a regular file"
```

Avec le nouveau ce n'est pas nécessaire.

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
* `cmd1 >> file` Appends à la fin du fichier au lieu de le remplacer
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
* `cmd &` cmd1 is executed in the background \(asynchronously in a subshell, returns status is 0 \(success\) 
* `cm1; cmd2;` cmds are executed sequentially

## Script

* `exit nb` Exit the script and returns nb \(0 being success\) 
* `exit` Exit the script and use it's last cmd return value as the script return value 
* `source script.sh` Will run the script and add it's variable to your current shell \(`. script.sh` will do the same\)

## Sources

* [TutoriaLinux](https://www.youtube.com/channel/UCvA_wgsX6eFAOXI8Rbg_WiQ)
* [http://mywiki.wooledge.org/BashFAQ/031](http://mywiki.wooledge.org/BashFAQ/031)
* Misc research

