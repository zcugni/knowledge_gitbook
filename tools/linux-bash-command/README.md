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
| `rm <path> -r` | Remove dir |
| `head <file> -n x` | First x \(default 10\) lines of file |
| `tail <file> -n x` | Last x \(default 10\) lines of file |
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

## Recherche

* [Find](https://zcugni.gitbook.io/notes/tools/linux-bash-command/find)
* [Grep](https://zcugni.gitbook.io/notes/tools/linux-bash-command/grep)

## Permission

### chmod

* Représente ainsi les permissions :
  * Read : r ou 4
  * Write : w ou 2
  * Execute : x ou 1
  * Rien : 0
  * SUID : s \(chez le user\) ou 4 \(avant le reste\)
  * SGID : s \(chez le groupe\) ou 2 \(avant le reste\)
  * u : L'utilisateur auquel appartient le fichier
  * g : Le groupe auquel appartient le fichier
  * o : Les autres
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

## Alias

* `alias name="cmd -param"`
* Does not persist outside the shell. To make it definitive, add it to the .rc file \(example : `/home/user/.bashrc`\)
* `alias` List all aliases
* `unalias alias_name`

## Net cmd

Les net-tools \(`netstat` & `ifconfig`\) ont été dépréciés car ils n'étaient plus maintenu.

### Remplacement

* `netstat` : `ss`
* `nestat -r` : `ip route` \(iproute2 ? check that\)
* `netstat -i` : `ip -s link`
* `netstat -g` : `ip maddr`
* `ifconfig` : `ip`

### Ports stat

\(Run as root\)

| Commande | Définition |
| :--- | :--- |
| `ss -t` | See tcp port |
| `ss -u` | See udp port |
| `ss -p` | See program attach to the port |
| `ss -l` | See listening port |
| `ss -n` | Numeric name |

### Ping

* Sonar-like request basically saying "Hello ? Are you there ?"
* Does not give any information about the host's services
* Needs one argument, the hostname or ip address of the host
* Successful pings tells you the speed of the response

#### Sweep ping

* `fping -a -g 10.54.12.0/24`
  * `-a` To show only live host
  * `-g` To do a ping sweep instead of a simple ping 
  * Redirects error to `2>/dev/null` because you'll always get some

### ARP

* `arp -a` montre la table contenant ces infos :
  * Le hostname \(représenté par un ? s'il est inconnu\). Il est trouvé via le _System Name Service_, ce qui peut être inutilement long. Utiliser `-n` pour ne pas l'obtenir.
  * Le temps d'expiration
  * Souvent, la propre mac adresse de l'appareil est harcodée et notée comme permanente
* `apr ip` Montre une entrée spécifique de la table

### ND command

They're different on each distro

### tcpdump \(Packet Sniffing\)

[tcpdump](https://zcugni.gitbook.io/notes/tools/linux-bash-command/tcpdump)

### Wireshark \(Packet sniffing\)

* Traffic analysis tool
* Graphic interface
* Always install Wireshark in a disposable virtual machine for security reasons

### Netcat \(Create packet\)

* Lets you generate arbitrary TCP/IP traffic.
* It can also simulate sockets and report on the data they receive.
* netcat is an old tool that has been forked, rewritten, extended and improved many times.
  * The openBSD version is the most popular \(and it support IPv6\).
* Defaults using TCP
* `netcat hostname port` If the client establish a TCP session, you'll get a blank line. 
  * Netcat hasn't sent any data but it has completed the three-way handshake. You can then write your own request \(`GET / HTTP/1.1`\)
* `-v` Shows errors
* `-u` Connects with UDP \(the protocol doesn't imply an answer, but netcat will listen for one and display it\)
* `netcat -l port` Creates netcat listeners \(Can also use `-u`\)
* You can use netcat to transfer files by redirecting the input of a listener to a file and using `netcat -q o ip port < file` on the sender machine \(or `-N` on some distribution\).
  * The data is sent in clear.
* You can also attach a command shell to a netcat listener but that's a security issue \(anyone connecting to the port will have shell access\)
* To send a command through netcat, it's easier to prepare it with echo before `echo "ping -c 1 my_ip" | nc their_ip their_port` 

### Traceroute \(Follow packet\)

* Lets you follow the packets as they travel between hosts
* `traceroute destination_host`
* It sends 3 packets to each hop as test. You get the time it took them de faire un aller-retour jusqu'à cet host
* You also get the hostname \(if available\) and ip of each host along the way
* A lost packet is represented by a \*. A few one is normal
* Disable DNS lookups with `-n`

#### Errors

* A line of `***` means that the previous host forwarded a packet, but no response came back so traceroute can't print an hostname or ip.
  * It can mean that the host filters UDP or ICMP.
  * Sometimes this will be the case only for one host so let traceroute run.
  * However if there's multiple lines like that you probably won't get more info.
* Time Spikes : Responding to a traceroute request takes more time that forwarding a packet, and it's not the prioriy of the router when it's busy. That's why an hop might have a longer time response that the next one.
* Multiple hostname might respond at one hop because of load balancers making the whole trace more confusing
* `!H` Means the next host is unreachable
* `!N` Means that the entire destination network is unreachble
* `!A`, `!X` & `!Z` Means that further communication is refused
* traceroute follows the path _to_ the target, but not the return one, which might differ \(aka, asymmetric routing\), so don't bother too much with high roundtrip times.
* Traceroute servers \(find them on internet\) lets you run traceroute from them, so that you can see if the problem lies within the host you're hoping through or from the return route it's using with you.
* To see if the dropped packets are a separate occurrence or an ongoing issue, just run multiple traceroute. `mtr` does that automatically.

### DNS \(host command\)

* Don't use `nslookup` on Unix because it was once the standard but it's been abandonned and then resurrected, so it's hard to know if your version is a decent one.
* Use `host` instead for basic queries. 
* For more complex one, eiter BIND \(Berkley Internet Name Daemon\)'s `dig` or Unbound's `unbound-host`
* `host hostname` Gives ip back
* `host ip` Gives hostname back \(in the result the ip is also reversed but don't pay attention\)
* For both case you can add a specific DNS IP as a third parameter to query it
* `-v` Gives more details \(response code, authority, records, ...\)

### nmap \(map network\)

Go see [Nmap](https://zcugni.gitbook.io/notes/tools/nmap)

### httprint \(fingerprint web server\)

* `httprint -P0 -h <target ip> -s <signature file>`
* `P0` to avoid pinging \(web server generally don't respond\)

## Compression & Decompression

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

## Encoding

### Base64

* `base64 -d file` Decode
  * Si on ne veut pas le faire depuis un fichier on peut faire ainsi `echo "..." | base64 -d`

### Hex to bin

* `xxd` Dump a file in hexa
* `echo "FF D8 FF DB" | xxd -r -p > new_file` Convert the hex back to binary and write it in a file
  * `-p` Let you write the hex in a more friendly manner \(without offset, etc\)
  * `-r` Actually does the reverse
  * If you want to add content to that file after the new hex, just do `cat other_file >> new_file`

## Tracing

* `strace` Intercepts system calls made libs to the Linux Kernel
* `ltrace` Intercepts library & system calls made by your application to libs

## Analyse de fichier

* `exiftool` Read/Write metadata of files
* `strings` Print the strings of printable characters of a file \(useful for determining content of non-text file\)

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

* \*\(1\) With parameters, `set` can also set our unset predefined shell options.
* \*\(2\) Une version plus détaillée d'`history` est dispo dans `/home/user/.bash_history` mais n'est lisible que par cet utilisateur. La commande semble plus laxiste dans les droits même si elle est moins précise dans le résultat.

## Tmux

[tmux](https://zcugni.gitbook.io/notes/tools/tmux)

## Script cmd

* Record what you do in your shell
* `script <file>` Record and write output to a file \(default typescript.txt\)
  * These can only bat shown \(via cat for example\)
* `script myscript.log --timing=time.log` \(-t probably sufficient\) Records with timestamp
  * Will replay recording in the shell with `scriptreplay -s myscript.log -t time.log`
* `ctrl-d` or exit to stop recording

## Bash specific commands

* `bash -c "cmd string"` Execute la chaîne
  * S'il y a d'autres arguments après celle-ci, ils sont transmis comme des arguments transitionnels commençant par $0
  * On peut éviter d'avoir des espaces en faisant cela `bash -c "{echo,string}"` au lieu de `bash -c "echo string"`
  * Si on veut piper des commandes qui ont des paramètres, faut les mettre par tas : `bash -c "{echo,string}|{base64,-d}"`
* `bash -i` Si l'option -i est présente, l'interpréteur est interactif \(utilisé dans des payloads mais je comprends pas entièrement pourquoi\)

## Sources

* Multiple pages of man
* _Networking for Systems Administrators_ by Michael W. Lucas
* [IppSec](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA)
* Misc research

