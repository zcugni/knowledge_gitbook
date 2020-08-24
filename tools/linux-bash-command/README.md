# Linux / Bash Command

## Command line shortcut

| Shortcut | Result |
| :--- | :--- |
| `ctrl-a` | Go to start of the line |
| `ctrl-e` | Go to end of the line |
| `up arrow key` | Previous cmd |
| `down arrow key` | Next cmd |
| `ctrl-p` | Previous cmd |
| `ctrl-n` | Next cdm |
| `ctrl-r` | Bash cmd search \(use multiple time to go back in history\) |
| `ctrl-c` | Interrupt |
| `ctrl-l` | Clear |

## Déplacement

| Commande | Définition |
| :--- | :--- |
| `pwd` | Print working dir |
| `.` | Current dir |
| `..` | Parent dir |
| `/` | Root dir |
| `~` | `home` dir |
| `cd` | Change current dir to `home` |
| `cd path` | Change current dir to path |
| `cd -` | Switch between last 2 dir |

## Fichier & Dossier

| Commande | Définition |
| :--- | :--- |
| `touch <path>` | Create empty file |
| `cp <src> <dest>` | Copy file |
| `mkdir <dir>` | Create dir |
| `mv <old> <new>` | Move file \(can rename\) |
| `rm <file>` | Remove file |
| `rm -r <path>` | Remove dir |
| `head <file> [-n <nb>]` | First nb \(default 10\) lines of file |
| `tail <file> [-n <nb>]` | Last nb \(default 10\) lines of file |
| `tail -f <file>` | Last 10 lines and keep following \(showing new content\) |

## View info / file

| Commande | Définition |
| :--- | :--- |
| `cat <file>` | "Read" file |
| `less` |  |
| `more` |  |
| `ls <dir>` | List dir content \(default `.`\) |
| `ls -a` | List hidden files |
| `ls -l` | List files with size & permissions |
| `ls -lh` | Same but in human readable format |

## Manipulation de string

| Commande | Définition |
| :--- | :--- |
| `cut -d<symbol> -f<part>` | Cut at the delimiter and returns the part |
| `sort -bf` | Ignore leading whitespace and case insensitive |
| `uniq` | Returns only unique result |
| `wc` | Word count |

## Compression & Decompression

* `.jar` sont des fichiers d'archives de classes java

### Compress

* `tar -zcvf archive.tar.gz Documents/`
  * z : z-zipping \(compress\)
  * c : create archive
  * v : verbose mode
  * f : create archive of name "archive.tar.gz" \(adding gz is good practice when you zipped\)
  * Documents/ : archiving all the content of this directory
* `gzip file.gz`
* `bzip2 file.bz2`

### Decompress

* `tar -zxf archive.tar.gz`
  * x : extract
  * z : for zip file
* `gzip -d file.gz`
* `gunzip file.gz`
* `bzip2 -d file.bz2`

### tarbomb

A tarbomb is a compressed file that when uncompressed will generate a lot of files/directory.

Good practice is to do that `tar -zcvf archive.tar.gz Documents/`, bad practice is to be _in_ Documents/ and do that `tar -zcvf archive.tar.gz`.

The first one will generate only 1 uncompressed directory, the second one will generate multiples.

## Transfert fichier

### scp

* Through ssh
* Remote to local : `scp username@ip:file local_dir`
* Local to remote : `scp file user@ip:remote_dir`
* Be mindful of the `:` ! 
* \(Absolute path, so /home/user/etc\)

### wget & curl

* `wget ip:port/path`
  * add `-O destination` if needed, default is current folder & original name 
* `curl ip:port/path`
  * `-s` To not have the banner

Pour les 2 y'a apparemment moyen de direct exécuter le fichier sans l'écrire sur le disque, faudrait que je regarde

### python

* Sur l'attaquant `python -m HttpSimpleServer` \(va créer un serveur dispo sur 8000\)
* Utiliser wget ou curl pour récup un fichier. Le root du serveur est le dossier dans lequel il a été lancé

### Copié-Collé encodé

* En fonction de la taille du fichier ça peut être plus simple de le base64, copier le résultat puis de le décoder et rediriger dans un fichier dans le terminal de destination

## Hex dump

* There's multiple tools to see the hex dump of a file
* `xxd` My preferred one
  * `xxd <in_file> [out_file]` Dump it's hexa, with it's ascii equivalent
  * `xxd -r -p <in_file> <out_file>`
    * The `-p` is necessary to write the hex in a friendly manner \(without offset, etc\)
    * `<in_file>` can be set as the standard input with `-`
    * If you want to add content to that file after the new hex, just do `cat other_file >> new_file`
  * `-e` Switch to little-endian
  * `-b` Switch to binary representation \(`-r` doesn't work with it\)
* `hexdump <file>` Dump it's hexa, but i find the format less friendly \(it doesn't show the ascii equivalent and the endianness is inversed from xxd\)

## Encoding

### Base64

* `base64 -d <file>` Decode
  * Si on ne veut pas le faire depuis un fichier on peut faire ainsi `echo "..." | base64 -d`

## Recherche

* [Find](https://zcugni.gitbook.io/notes/tools/linux-bash-command/find)
* [Grep](https://zcugni.gitbook.io/notes/tools/linux-bash-command/grep)

## Analyse de fichier

* `exiftool` Read/Write metadata of files
* `strings` Print the strings of printable characters of a file, useful for determining content of non-text file. Can also be used on block device to analyze their content.

### File

* Classify a file, perform 3 tests : Filesystem, Magic & Language \(one after the other, stop if the previous one succeeded\). 
  * Filesystem test use the return of `stat`, file type depending on your system \(sockets, symlink, etc\), are intuited here.
  * Magic test check for the magic number of the file \(a predefined number near the beginning of the file's data, that describe it's type\). The db of corresponding entries comes form the compiled binary in `/usr/share/misc/magic.mgc` and user additions in `/etc/magic`
  * Language test checks for character set 
* Le retour contient généralement soit `text`, soit `executable`, soit `data` dans sa description, on peut donc baser des scripts là-dessus.

## Permission

### chmod

* Représente ainsi les permissions :
  * Read : `r` / `4`
  * Write : `w` / `2`
  * Execute : `x` / `1`
  * Rien :  `-` / `0`
  * SUID avec exécution : `s` \(à la place du x\) / 4 \(avant le reste\)
  * SGID avec exécution : `s` \(à la place du x\) ou 2 \(avant le reste\)
  * SUID sans exécution : `S` \(à la place du x\) / 4 \(avant le reste\)
  * SGID sans exécution : `S` \(à la place du x\) ou 2 \(avant le reste\)
  * `u` : L'utilisateur auquel appartient le fichier
  * `g` : Le groupe auquel appartient le fichier
  * `o` : Les autres
* Permet de set les permissions de différentes façons :
  * `chmod u+w file`, `chmod u-x file`, `chmod u=wr file`, `chmod u+s file` Ajoute, supprime ou définit les droits de l'utilisateur \(remplacer par _g_ ou _o_ si nécessaire\)
  * `chmod 754 file` Définit les droits pour chaque groupe en les additionnant
  * `chmod 4755 file` Same mais en ajoutant le SUID

### chown

Change file owner

## Users & password

| Commande | Définition |
| :--- | :--- |
| `useradd` |  |
| `passwd <username>` |  |
| `usermod` |  |
| `userdel` |  |
| `newusers` |  |
| `whoami` | Current user name |
| `id` | See all groups i'm part of |
| `who` | Gives multiple info \*\(1\) |
| `w` | Same as who but more complete |

\*\(1\) : who's logged in, from where, when and what they are currently running

## Process

| Commande | Définition |
| :--- | :--- |
| `kill -l` | List signal |
| `kill <signal_nb> <pid>` | Send signals \(default `SIGTERM`\) |
| `killall <name>` | Sends to all processes starting by name |
| `pkill -u <username>` | Kill all username's processes |
| `nice` | Set niceness of process |
| `renice` | Change niceness of process |
| `ps` | List running processes |
| `which <name>` | Tell where the binary is located |
| `top` | List running processes \*\(1\) |

* \*\(1\) Par défaut ceux utilisant le plus de puissance en premier

### PS

* Display information on active process
* Il accepte plusieurs style de paramètre :
  * Unix \(par ex `-a`\)
  * BSD \(sans \`-\` : `a`\)
  * Les long \(`--a`\)
  * Du coup y'a des options redondantes entre chacun
* By default, ps select all processes with the same EUID as the current user and associated with the same terminal as the invoker.

| Option | Effect |
| :--- | :--- |
| `a` | Lift the BSD "only yourself" restriction |
| `-A`/`-e` | Select all processes |
| `-N ..` | Not ... |
| `x` | Lift the BSD "must have a terminal" restriction |
| `T` | Select all processes associated with this terminal |
| `r` | Only running processes |
| `-p <pid_list>` / `p <pid_list>` | Select by PID |
| `-c <cmd_list>` | Select by cmd |
| `-t <terminal_list>` / `t <terminal_list>` | Select by terminal |
| `-u <euid_list>` / `U <euid_list>` | Select by EUID |
| `u` | Display in user-oriented format |
| `-f` | Display in full format listing |

## Alias

* `alias name="cmd -param"`
* Does not persist outside the shell. To make it definitive, add it to the .rc file \(example : `/home/user/.bashrc`\)
* `alias` List all aliases
* `unalias alias_name`

## Net cmd



## GPG

* pgp encoding
* `gpg --gen-key` Generate a key pair with default setting
* `gpg --export <username> > <file>` Export the public key of the user to a file \(binary format\)
  * To export the ascii representation of it, add `--armor`
* `gpg --import <file>` Import public key from file
* `gpg -r <username> --encrypt <file>` Encrypt file using user's public key \(binary format\)
  * To get an ascii file as result, add `--armor`
* `gpg --decrypt <file>` Decrypt file using your private key
* `gpg --list-keys` To see all the imported keys
* `gpg --fingerprint <username>` To get the fingerprint of the user's public key

## Tracing

* `strace` Intercepts system calls made libs to the Linux Kernel
* `ltrace` Intercepts library & system calls made by your application to libs

## Traitement json

* `jq`

With this kind of json :

```text
{
    "level1": {
        "attribute1": "...",
        "attribute2": "...",
        "object": {
            "obj_att1": "...",
            "obj_att2": "...",
            "obj_att3": "...",
        }
        "array": [
            {
                "array_inner1_1" : "...",
                "array_inner1_2" : "...",
                "array_inner1_3" : "...",
            },
            {
                "array_inner2_1" : "...",
                "array_inner2_2" : "...",
                "array_inner2_3" : "...",
            },
        ]
    }
}
```

On peut accéder aux différents éléments ainsi :

* `.. | jq ".level1"`
* `.. | jq ".level1 .object .obj_att2"`
* `.. | jq ".level1 .array[]"`
* `.. | jq ".level1 .array[0] .array_inner1_3"`

## Disk info

* `df` Report disk space usage, also explicit where things were mounted \(use with  `-lh`\)
* `mount` Show where things were mounted

## Screen Multi-plexer

* [Tmux](https://zcugni.gitbook.io/notes/tools/tmux)
* Screen
  * `screen -ls` To list sessions
  * `screen -r <session_name>` To attach to the session
  * `screen -dr <session_name>` To detach others and then attach to the session

## Script cmd

* Record what you do in your shell
* `script <file>` Record and write output to a file \(default typescript.txt\)
  * These can only bat shown \(via cat for example\)
* `script myscript.log --timing=time.log` \(-t probably sufficient\) Records with timestamp
  * Will replay recording in the shell with `scriptreplay -s myscript.log -t time.log`
* `ctrl-d` or exit to stop recording

## Bash tips

* `bash -c "cmd string"` Execute la chaîne
  * S'il y a d'autres arguments après celle-ci, ils sont transmis comme des arguments transitionnels commençant par $0
  * On peut éviter d'avoir des espaces en faisant cela `bash -c "{echo,string}"` au lieu de `bash -c "echo string"`
  * Si on veut piper des commandes qui ont des paramètres, faut les mettre par tas : `bash -c "{echo,string}|{base64,-d}"`
* `bash -i` Si l'option -i est présente, l'interpréteur est interactif \(utilisé dans des payloads mais je comprends pas entièrement pourquoi\)

## Misc

| Commande | Définition |
| :--- | :--- |
| `ln -s <real_file> <link_file>` | Create symbolic link |
| `clear` | Clear shell |
| `man <cmd>` | Manuel de la cmd |
| `sudo` | Super user do |
| `sudo !!` | Run last cmd as root |
| `tree` | Tree view of directory/files |
| `which <cmd>` | Show binary location of cmd |
| `set` | List shell var & func \*\(1\) |
| `history` | Shows history of cmd |
| `watch -n <sec_interval> <cmd>` | Execute une cmd à une intervalle donnée |
| `htop` | Mix entre `w` & `top` avec une interface ncurse |
| `uname` | Gives info on kernel version \(use `-a` for everything\) |

* \*\(1\) With parameters, `set` can also set our unset predefined shell options.
* \*\(2\) Une version plus détaillée d'`history` est dispo dans `/home/user/.bash_history` mais n'est lisible que par cet utilisateur. La commande semble plus laxiste dans les droits même si elle est moins précise dans le résultat.

## Sources

* Multiple pages of man
* _Networking for Systems Administrators_ by Michael W. Lucas
* [IppSec](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA)
* Misc research
* [https://linux.101hacks.com/unix/gpg-command-examples/](https://linux.101hacks.com/unix/gpg-command-examples/)

