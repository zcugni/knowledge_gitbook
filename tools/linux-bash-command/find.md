# Find

## Généralités

* Find est très complet, donc je ne vais détailler que l'essentiel ici
* Structure d'une commande : `find <origin_path> [general_options] <search_options> [actions]`
* Faut le coupler à `2>/dev/null` à cause de tout les Permission denied.

## General options

* `-depth` / `-d` Process directory content before the directory itself \(implied by `-delete`\)
* `-maxdepth <nb>`

## Search options

* For every option with `n`, we can also use `-n` \(less than n\) & `+n` \(more than n\)
* For every option with `pattern` those should be in `''` \(so the shell won't expand it\) and you can use `*` as wildcard

| Commande | Effect |
| :--- | :--- |
| `-amin <nb>` / `-mmin <nb>` / `-cmin <nb>` | File last accessed / modified / permission changed n min ago |
| `-atime <nb>` / `-mtime <nb>` / `-ctime <nb>` | File last accessed / modified / permission changed n\*24h ago \(1\) |
| `-anewer <other_file>` / `-newer <other_file>` / `-cnewer <other_file>` | Last accessed / modified / permission changed more recent than the other file |
| `-empty` | File or directory is empty |
| `-executable` / `-readable` / `-writable` | Files with these permissions for the current user. |
| `-group <name>` / `-user <name>` |  |
| `-name <pattern>` / `-iname <pattern>` | Files which name matches the pattern, iname is case-insensitive |
| `-path <pattern>` / `-ipath <pattern>` | Files which path matches the pattern, ipath is case-insensitive \(2\) |
| `-regex <regex_pattern>` |  |

\(1\) fraction part are ignored, so -mtime +1 would only show files access two or more days earlier

\(2\) Le path est relatif au starting point donné à find. Donc si on est dans notre home, on va pas utiliser `/home/user/file.txt` mais juste `./file.txt`

* `-perm ...`
  * `-perm 644` Files with these exact permissions bits set
  * `-perm -220` / `-perm -u+w,g+w` Files with _at least_ these permissions bits set \(a 777 would match for example\)
  * `-perm /220` / `-perm /u+w,g+w` Files with any of these permissions bits set
* `-size <nb><unit>`
  * b : 512-byte blocks
  * c : bytes
  * k : kibibytes\(KiB\)
  * M : mebibytes \(MiB\)
  * G : gibibytes \(GiB\)
  * Example : `size +2G`
  * Size are rounded to the next unit
* `-type <letter>`
  * d : Directory
  * f : Regular File
  * l : symbolic link
  * s : socket

## Actions

* `-delete`
* `-ls` / `-fls <file>` Run `ls -dils` on files, -fls write the result to the specified file
* `-print0` / `-fprint0 <file>` Prints files name on the output \(or write it to the specified file\), with null characters separating them instead of new lines \(like in `-print`\), which make it less prone to breaking with filename containing white spaces
* `-printf <format>` / `-fprintf <file> <format>` Write the result to the specified file in the format describe in printf manner
* `-prune` If the file is a directory, do not descend into it
* `-exec` Permet d'appliquer des commandes aux fichiers trouvés
  * Exemple : `-exec wc -l {} \;` {} sera remplacé par chaque instance et le ; doit être escape because reasons
  * Ils font plutôt un \| avec `xargs` et je connais pas trop les détails/la diff. _A compléter_

## Operators

Expression will be abbreviated to expr

| Syntax | Meaning |
| :--- | :--- |
| `( expr )` | Priority |
| `! expr` | Not |
| `expr expr` / `expr -a expr` | And |
| `expr -o expr` | Or |

## Source

* find's man page

