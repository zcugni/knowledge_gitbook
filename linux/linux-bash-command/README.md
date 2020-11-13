# Command

## Man

* Man shows the manual page of a function, file, syscall, etc
* `man [section] <thing>` 
* There's 9 sections, but the first 5 are the most important :
  * 1 : User commands
  * 2 : Sys call
  * 3 : Library call
  * 4 : Special files
  * 5 : File format & convention
* If no section is given, man will search them in this order by default : 1 .. 3 2 ... 5 4 ...
  * .. is a placeholder for the other sections i haven't spoken of

## Command line shortcut

| Shortcut | Result |
| :--- | :--- |
| `ctrl-a` | Go to the  start of the line |
| `ctrl-e` | Go to the end of the line |
| `up arrow key` / `ctrl-p` | Previous cmd |
| `down arrow key` / `ctrl-n` | Next cmd |
| `ctrl-r` | Bash cmd search \(use multiple time to go back in history\) |
| `ctrl-c` | Interrupt |
| `ctrl-l` | Clear |
| `ctrl-z` | Stop a job and put it in the background |

## Location

| Command | Definition |
| :--- | :--- |
| `pwd` | Print working dir |
| `.` | Current dir |
| `..` | Parent dir |
| `/` | Root dir |
| `~` | `home` dir |
| `cd` | Change current dir to `home` |
| `cd <path>` | Change current dir to path |
| `cd -` | Switch between last 2 dir |

* `cd` is a shell buitin command, which is why you can't use it with sudo

## Files & Directory

### Creation & removal

| Command | Definition |
| :--- | :--- |
| `touch <path>` | Create empty file |
| `cp <src> <dest>` | Copy file \(warning : it does not copy a SUID bit\) |
| `cp -r <src> <dest>` | Copy dir recursively |
| `mkdir <dir>` | Create dir |
| `mv <old> <new>` | Move file/dir \(can rename\) |
| `rm <file>` | Remove file |
| `rm -r <path>` | Remove dir recursively |

### File content / analysis

| Command | Definition |
| :--- | :--- |
| `cat [-]` | With no files or with `-` cat reads `stdin`  |
| `cat <file> [file]` | Print one or multiple file, you can use \* in names to read multiple files |
| `less` | Print file, but wait after a page for your input to continue |
| `more` | I don't know the diff with `less` |
| `ls <dir>` | List dir content \(default `.`\) |
| `ls -a` | List hidden files |
| `ls -l` | List files with size & permissions |
| `ls -lh` | Same but in human readable format |
| `head <file> [-n <nb>]` | First nb \(default 10\) lines of file |
| `tail <file> [-n <nb>]` | Last nb \(default 10\) lines of file |
| `tail -f <file>` | Last 10 lines and keep following \(showing new content\) |
| `exiftool` |  Read/Write metadata of files |
| `strings` | Extract readable strings from the file |
| `strings -t x <file>` | Gives the offset of each strings within the file |
| `sort` |  |

#### Uniq

* Filter **adjacent** duplicate \(use with sort because of the adjacent rule\)
* `-u` Get only unique entries
* `-c` Get count of occurrences

#### File

* Classify a file
* Perform 3 tests one after the other. Stop if the previous one succeeded :
  * Filesystem : Use the return of `stat`, guess file type depending on your system \(sockets, symlink, etc\)
  * Magic : Check the file's magic number. The db of corresponding entries comes form the compiled binary in `/usr/share/misc/magic.mgc` and user additions in `/etc/magic`
  * Language : Checks the character set 
* The return will either contain `text`, `executable` or `data` in the description, so you can base script on that
* For executable `LSB` means it's little-endian

### File transfer

#### scp

* Through ssh
* Remote to local : 
  * Without port : `scp [<username>@]<ip | hostname><:path>`
  * With port : `scp [<username>@]<ip | hostname>:<port>/<path>`
  * Path is absolute
* Local to remote : `scp <file> <username>@<ip>:<remote_dir>`
* Absolute path

#### wget & curl

* `wget <ip>:<port>/<path>`
  * Add `-O <dest>` if needed, default is current folder & original name 
* `curl <ip>:<port>/<path>`
  * `-s` To not have the banner

{% hint style="info" %}
There's a way to directly execute a file without writing it to a disk, check that
{% endhint %}

#### python

* On the source machine : `python -m HttpSimpleServer` \(will create a server available at port 8000\)
* Use wget/curl to retrieve the file
*  The root of the server is the directory in which this command was executed

#### Copy-paste encoded

* Depending on the size of the file, it's might be easier to base64 encode it, copy the result then decode it in the destination terminal

## Search

* [Find](https://zcugni.gitbook.io/notes/tools/linux-bash-command/find)
* Others :

| Command | Definition |
| :--- | :--- |
| `which <cmd>` | Show binary location of cmd |
| `locate <pattern>` | Locate files with this pattern within the system |

### Grep

* Finds strings within an input
* `grep <pattern> <file> <file>`
* There's multiple modes for regex
* Options :
  * `-f <file>`
  * `-e <pattern>` Specify a pattern, if there's only one, you can omit the `-e`
  * `-i` Case-insensitive
  * `-v` Invert match \(shows lines that are not matching\)
  * `-w` / `-x` Only match whole words / lines
  * `-c` Replace output with a count of matches
  * `-m nb` Stop after nb matches
  * `-n` Prefix each lines in result with a number
    * `-T` Align result \(useful when used with `-n` or other prefixing options\)
  * `-Z` Replace newline character by null byte \(useful when dealing with filenames containing new lines\)
  * `-A <nb>` / `-B <nb>` / `-C <nb>` Print nb lines of context after / before / around the match
  * `-r` Recursive

## Compression & Decompression

#### Compress

* `tar -zcvf <file.tar.gz> [dir/]`
  * `-z` Z-zipping \(compress\)
  * `-c` Create archive
  * `-v` Verbose mode
  * `-f <name>` Create archive of specific name, if you used -z, add `.gz` at the end
  * Give a dir name to compress all it's content without creating a tar bomb
* `gzip <file>`
* `bzip2 <file>`

#### Decompress

* `tar -zxf <file.tar.gz>`
  * `-x` Extract
  * `-z` For zip file
  * `-f <name>` Select which file
* `gzip -d <file.gz> [-k]`
* `gunzip <file.gz> [-k]`
* `bzip2 -d <file.bz2> [-k]`
* `-k` To keep the original file

#### tarbomb

* A compressed file that when uncompressed will generate a lot of files/directory
* Good practice is to do that`tar -zcvf archive.tar.gz Documents/`
  * Bad practice is to be _in_ Documents/ and do that `tar -zcvf archive.tar.gz`
  * The first one will generate only 1 uncompressed directory, the second one will generate multiples

## Users & password

| Command | Definition |
| :--- | :--- |
| `useradd` |  |
| `passwd <username>` |  |
| `usermod` |  |
| `userdel` |  |
| `newusers` |  |
| `whoami` | Current user name |
| `id` | See all groups i'm part of |
| `who` | Who's logged in, from where, when and what they are currently running |
| `w` | Same as who but more complete |

## Jobs

* Jobs are processes run in the background
* They run asynchronously from their origin and are in a subshell
* Jobs have a job id

| Command | Description |
| :--- | :--- |
| `jobs` | List all jobs with their ids & status \(stopped or not\) |
| `<cmd> &` | Put a command in the background |
| `fg [%<job_id>]` | Put the job in the foreground |
| `bg [%<job_id>]` | Put the job in the background |
| `ctrl-z` | Stop the job and puts it in the background |

## Disk info

* `df` Report disk space usage, also explicit where things were mounted \(use with  `-lh`\)
* `mount` Show where things were mounted

## String manipulation

| Command | Definition |
| :--- | :--- |
| `cut -d<symbol> -f<part>` | Cut at the delimiter and returns the part |
| `sort -bf` | Ignore leading whitespace and case insensitive |
| `uniq` | Returns only unique result |
| `wc` | Word count |

## Screen Multi-plexer

* [Tmux](https://zcugni.gitbook.io/notes/tools/tmux)
* Screen
  * `screen -ls` To list sessions
  * `screen -r <session_name>` To attach to the session
  * `screen -dr <session_name>` To detach others and then attach to the session

## Hacking cmd

### Hex dump

* There's multiple tools to see the hex dump of a file
* `xxd` My preferred one
  * `xxd <in_file> [out_file]` Dump it's hexa, with it's ascii equivalent
  * `xxd -r [-p] <in_file> <out_file>` Create the bytestream corresponding to an hex dump
    * `-r` Reverse
    * The `-p` is necessary to write the hex in a friendly manner \(without offset, etc\)
    * `<in_file>` can be set as the standard input with `-`
    * If you want to add content to that file after the new hex, just do `cat <other_file> >> <new_file>`
  * `-e` Switch to little-endian
  * `-b` Switch to binary representation \(`-r` doesn't work with it\)
* `hexdump <file>` Dump it's hexa, but i find the format less friendly \(it doesn't show the ascii equivalent and the endianness is inversed from xxd\)

### Base64 Encoding

* `base64 -d <file>` Decode
  * To do it from a file use `echo <file_name> | base64 -d`

### GPG

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

### Elf

* `readelf` Analyze elf files

### Tracing

* `strace` Intercepts system calls made by libs to the kernel
* `ltrace` Intercepts library & system calls made by your application to libs

### Debugging

* `ulimit -c unlimited` The the OS to dump the memory whenever a crash occurs \(must be root\) 

## Misc

### Alias

* `alias <name>="[cmd] [-param]"`
* `alias` List all aliases
* `unalias alias_name`
* Does not persist outside the shell. To make it definitive, add it to `.rc` file \(example : `/home/user/.bashrc`\)
* `ld` Linker

### JQ - Traitement json

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

* You can access different element like that :
  * `.. | jq ".level1"`
  * `.. | jq ".level1 .object .obj_att2"`
  * `.. | jq ".level1 .array[]"`
  * `.. | jq ".level1 .array[0] .array_inner1_3"`

### Script cmd

* Record what you do in your shell
* `script <file>` Record and write output to a file \(default typescript.txt\)
  * These can only be shown \(via cat for example\)
* `script myscript.log --timing=time.log` \(-t probably sufficient\) Records with timestamp
  * Will replay recording in the shell with `scriptreplay -s myscript.log -t time.log`
* `ctrl-d` or exit to stop recording

### Other small things

<table>
  <thead>
    <tr>
      <th style="text-align:left">Command</th>
      <th style="text-align:left">Definition</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><code>ln -s &lt;real_file&gt; &lt;link_file&gt;</code>
      </td>
      <td style="text-align:left">Create symbolic link (absolute path)</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>clear</code>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><code>tree</code>
      </td>
      <td style="text-align:left">Tree view of directory/files</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>set</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li>List shell var &amp; func</li>
          <li>Set/unset predefined shell options</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>watch -n &lt;sec_interval&gt; &lt;cmd&gt;</code>
      </td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><code>htop</code>
      </td>
      <td style="text-align:left">Mix between <code>w</code> &amp; <code>top</code> with an ncurse interface</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>uname</code>
      </td>
      <td style="text-align:left">Gives info on kernel version (use <code>-a</code> for everything)</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>tr</code>
      </td>
      <td style="text-align:left">Translate, squeeze, and/or delete characters from strings</td>
    </tr>
  </tbody>
</table>

* `seq [first_nb] [increment] <last_nb>` Generate a sequence of number
  *  Start at `first_nb` and incremented by `increment` until 
  * Default `first_nb` & `increment` are 1
* `history` Shows history of cmd
  * For bash, there's a more detailed history in`/home/<user>/.bash_history`  only readable by the user
  * The cmd is more permissive but less complete
* `echo <string>` Print the string to the `stdout`
  * Its a **builtin** command and each shell can change it so it won't necessarily act like the man describe
    * In some setup 
  * `-n` Remove the last `\n`
  * `-e` Interpret backslash escape, will interpret sequence like `\n` or `\x41`
    * Since it's a builtin in certain setup `-e` doesn't do anything, instead it interpret these sequences if the string is between `'` or `"` 

## Bash tips

* `bash -c "cmd string"` Execute la chaîne
  * S'il y a d'autres arguments après celle-ci, ils sont transmis comme des arguments transitionnels commençant par $0
  * On peut éviter d'avoir des espaces en faisant cela `bash -c "{echo,string}"` au lieu de `bash -c "echo string"`
  * Si on veut piper des commandes qui ont des paramètres, faut les mettre par tas : `bash -c "{echo,string}|{base64,-d}"`
* `bash -i` Si l'option -i est présente, l'interpréteur est interactif \(utilisé dans des payloads mais je comprends pas entièrement pourquoi\)

## Sources

* Multiple pages of man
* [IppSec](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA)
* Misc research
* [linux.101hacks.com](https://linux.101hacks.com/unix/gpg-command-examples/) for GPG 
* [thegeekdiary.com](https://www.thegeekdiary.com/understanding-the-job-control-commands-in-linux-bg-fg-and-ctrlz/) for jobs

