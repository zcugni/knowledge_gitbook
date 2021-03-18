# Bash

{% hint style="info" %}
Add something about parameter substitution : [https://tldp.org/LDP/abs/html/parameter-substitution.html](https://tldp.org/LDP/abs/html/parameter-substitution.html)
{% endhint %}

## Terminology

### Bash, shell, terminal, etc

* **Bash** \(_Bourne-Again-Shell_\) is a GNU shell \(command line interpreter\) 
  * It's compatible with `sh` and include functionalities of the Korn Shell \(`ksh`\) and the C Shell \(`csh`
* **Shell** is a _macro processor_ that executes commands
  * A unix shell is both a _command interpreter_ and a programmation language
* **Terminal**, **shell** & **command line** are often used interchangeably to indicate a text based system for navigating your OS
  * Command line is very windows centric terminology, terminal is very mac centric

### Interactive, login & pseudo shell

* **Interactive** shells are simply shell
  * We call them interactive because we can give them input and receive output
  * For example, sudo can either :
    * Send a cmd to a shell and give back the result
    * Return an interactive shell to u
* **Login** shells are interactive shells that simulate the state you get at login
  * It clears environment vars, read login files like `.profile`, etc
* **Pseudo**-shell \(or pseudo terminal ?\) are independent from the process/session that spawns them, so there's less security issues
  * I guess it's kind of like a sandbox

## Shebang

* A one-line comment at the start of the script
* Indicates the path to the interpreter to use when you launch the script without specifying it
* Generally, use `#!/bin/bash`

## Variable

* Assignment : `var="value"`
  * Won't work with spaces around the `=` because if will search for the cmd name
* Call :

```bash
echo "str is $var ." # You can't write chars directly next to $var
echo "str is ${var}s." # You can
```

* Environment variable / constant in CAPS
* There's some special chars :
  * `$?` Last cmd's return value
  * `$#` Number of args in the script
  * `$0` Name of the script \(0th arg\)
  * `$<nb>` Script arg number `n`
  * `$@` List args

{% hint style="info" %}
* I wrote " To put a command in a var do `x=(printf "test");` \(test avec un $ devant la paranthèse si ça marche pas\)" but update it
* With `x=$(pwd)`, test if `x` gives always the same result or not \(aka, does it contain the cmd or the result ?
* Test without `$`
{% endhint %}

## Quoting

```bash
echo 'won t be interpreted'
echo "\$,\`,\\ will be interpreted"
echo "this is a `cmd_to_be_interpreted`."
```

## Functions

```bash
function_name(){
    # code
}

function function_name(){
    # code
}
```

* The keyword `function` is optional
* If you use `$1` inside a function, it will return it's first parameter and not the one of the script

{% hint style="info" %}
* I wrote that but i think i mix it up with command :
  * On les appelles directement sans \(\) : echo "test".
  * La valeur de retour est celle de la dernière commande.
{% endhint %}

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

### Generalities

* There's multiple if syntaxes, more or less old & complete
  * Interpreters \(bash, sh, zsh, etc\) don't react the same to all of them
  * The following explanations are for **bash**
* I wrote a [test script](https://github.com/zcugni/misc_script/blob/master/test.sh) to verify its behavior
  * Use it with `bash test.sh`, `sh test.sh`, etc to see the difference

{% hint style="info" %}
I think this test script is private in my github
{% endhint %}

* Usually, use this syntax :
  * ```bash
    if [[ condition ]]; then
        # cmd...
    elif [[ condition ]]; then
        # cmd...
    else
        # cmd...
    fi
    ```
  * `elif` & `else` are optional
  * `;` Can be replace by a new line

### Type of test

* Warning :  if you use an unknown symbol, you won't necessarily get an error, but the result might be wrong
* `if [[ condition ]]`
  * The spaces after the `[` and before the `]` are necessary
  * String operators : `=`, `==`, `>`, `<`
  * Int operators : `-eq`, `-ne`, `-gt`, `-lt`, `-ge`, `-le`, `=`, `==`, `!=`, `>`, `<`
  * Combination operators : `&&`, `||`
  * Not POSIX compliant but supported by bash
  * Support regular expression
* `if (( condition ))`
  * Used only for numerical comparison
  * Int operators : `==, !=, >, <, >=, <=`
  * Combination operators : `&&, ||`
* `if [ condition ]`
  * Old \[\[ \]\] version, alias to `test`
  * The spaces after the `[` and before the `]` are necessary
  * String operators : `=`, `==`
  * Int operators : `-eq`, `-ne`, `-gt`, `-lt`, `-ge`, `-le, =, ==, !=`
  * Combination operators : `-o`, `-a`
  * If one part of the equation evaluate to an empty string, it won't work
  * If a variable contains a string with space, you need to write it into `"` :
  * ```bash
    file="file name"
    [ -f "$file" ] && echo "$file is a regular file"
    ```
* There's some special symbols to work with files, for example : `-e` or `-nt`, but i have not experienced with them
* There's 2 syntaxes to mix `if` with cmds :
  * `if (command)`
    * Runs the command in a subshell. When it completes, it sets an exit code and the if statement acts accordingly
    * Used to limits side-effects for commands that need special var or other changes to the shell's environment. Those disappear with the subshell
  * `if command` The command is executed and the if statement acts accordingly to its exit code

## Condition - Case

```bash
case $var in
  pattern1 | pattern2 | pattern3) cmd;;
  pattern4 | pattern5 ) cmd;;
  *) cdm;;
esac
```

* Each pattern is separated by a  `|`
* A list of pattern is ended by a `)`
* `*` is the default case
* `;;` If it matches, stop  after executing the command for this condition
* `;&` If it matches, execute this condition cmd and the next one
* `;;&` if it matches, execute this condition cmd and test the next condition
* If there's no match, return 0, else return the status of the last cmd executed

## Condition - Select

Special command that lets you create menus :

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

* You can redirect with `>` & `<` :
  * `cmd1 > cmd2` or `cmd1 1> cmd2` Use the cmd1 output as input for cmd2
  * `cdm1 2> cmd2` Use the cmd1 `STDERR` as input for cmd2
  * `cmd1 < cmd2` Usethe cmd2 output as input for cmd1
  * `cmd1 >> file` Append to the file instead of replacing it
    * You can also use it with  `2>>`
  * `cm1 &> file` Redirects both `STDOUT` & `STDERR` \(equivalent to `>&` & `2>&1`\)
* You can chain with `|` : `[time] cmd1 | cmd2 | ...`
  * Output of the first cmd is fed as input to the second cmd, etc
  * `time` is optional and gives the total time of the operation
  * Each cmd is executed in it's own subshell
  * `|&` will also feed `STDERR`

## Run a command

* Directly use it's name and parameters : `uname -a`
* Write it between `````
* Write it inside `$()` : `$(command)`

## Chain commands

* `cmd1 && cmd2` cmd2 is executed only if cmd1 succeeded
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

