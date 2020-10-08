# Find

## Generalities

* Quite complete, i won't detail everything
* Basic syntax :`find <origin_path> [general_options] <search_options> [actions]`
* Use it with`2>/dev/null` \(because of all the Permission denied\)

## General options

* `-depth` / `-d` Process directory content before the directory itself \(implied by `-delete`\)
* `-maxdepth <nb>`

## Search options

* For every option with `nb`, we can also use `-nb` \(less than nb\) & `+n` \(more than nb\)
* For every option with `pattern`, those should be in `''` \(so the shell won't expand it\) and you can use `*` as wildcard

| Commande | Effect |
| :--- | :--- |
| `-amin <nb>` / `-mmin <nb>` / `-cmin <nb>` | File last accessed / modified / permission changed nb min ago |
| `-atime <nb>` / `-mtime <nb>` / `-ctime <nb>` | File last accessed / modified / permission changed nb\*24h ago \(1\) |
| `-anewer <other_file>` / `-newer <other_file>` / `-cnewer <other_file>` | Last accessed / modified / permission changed more recent than the other file |
| `-empty` | File or directory is empty |
| `-executable` / `-readable` / `-writable` | Files with these permissions for the current user |
| `-group <name>` / `-user <name>` |  |
| `-name <"pattern">` / `-iname <"pattern">` | Files which name matches the pattern, iname is case-insensitive |
| `-path <pattern>` / `-ipath <pattern>` | Files which path matches the pattern, ipath is case-insensitive \(2\) |
| `-regex <regex_pattern>` |  |

* \(1\) Fraction part are ignored, so -mtime +1 would only show files accessed two or more days earlier
* \(2\) The path is relative to the starting point of find



* `-perm ...`
  * `-perm 644` Files with these exact permissions bits set
  * `-perm -220` / `-perm -u+w,g+w` Files with _at least_ these permissions bits set \(a 777 would match for example\)
  * `-perm /220` / `-perm /u+w,g+w` Files with any of these permissions bits set
* `-size <nb><unit>`
  * b : 512-byte blocks
  * c : Bytes
  * k : Kibibytes\(KiB\)
  * M : Mebibytes \(MiB\)
  * G : Gibibytes \(GiB\)
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
* `-exec` Execute commands on found files
  * Example : `-exec wc -l {} \;`
    * `{}` will be replaced by each instance, and `;` must be escaped because reasons
  * People often use a pipe with `xargs` but i don't know the details

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

