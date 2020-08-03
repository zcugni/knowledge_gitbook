# Linux / \*nix



{% hint style="info" %}
Read this book : [Unix Linux System Administration Handbook](https://www.amazon.fr/UNIX-Linux-System-Administration-Handbook/dp/0134277554)
{% endhint %}

## File system

* In linux, everything is a file. 
* File extensions also don't matters but we use them because they're parsed by other programs and help human gain context.

### Pseudo file system

* These are special file systems which contain informations about the current running system.
* They only exist in the RAM, have a size of 0, and don't persist after a reboot
* They're often parsed by other programs to be shown in a more pleasing manner or act upon
* Complete description available in `man hier` \(for hierarchy\)

#### /proc/

* `man proc`
* `/proc/[pid]/` Each sub directory is a process \(identified by it's pid\), and contains information about itself such as :
  * `/attr/` Security-related attributes
  * `cmd` Which cmd the process is currently running
  * `cmdline` Which cmd spawned the process
  * `comm` Name of the process
  * `cwd/` symbolic link to the working directory of the process
  * `environ` State of the env variable when the process was spawned
  * `exe` Symbolic link to executable
  * `stat` Status of the process, used by `ps`
  * `status` Same info but more human readable
  * Diverse files on memory mapping, groups, file descriptors, threads, mount points, namespace, timers, etc
* Varied info files, such as :
  * `/proc/cmdline` Arguments given to the kernel at boot
  * `/proc/filesystems` List all file systems supported by the kernel
  * `/proc/version` Kernel's version
* Diverse files about bus, devices, mounts, groups, cpu, network, kernel variable \(sys\), partitions, timers, etc 

#### /etc/

* Directory for all the configuration data
* /etc/passwd & /etc/shadow : used for password, check section below
* /etc/group : contains the user group list

#### /opt/

* For optional software, guideline not really defined, not often used
* However, in kali there's all the pen test tools in /usr/share/
* Also, it's writable by root but readable by others, so it might be interesting as a common folder among users for tools

#### /dev/

* All devices
* Regular part disk are written in the form "sda1", "sda2", "sdb1". The letter representing the disk and the number the partition.
* Contains \(for bash\) /dev/tcp/ to connect to

#### Others

| Directory | Content |
| :--- | :--- |
| /sbin/ & /bin/ | Secure binaries \(need root\) & "normal" binaries |
| /tmp/ | For temporary files, blown away each restart |
| /home/ & /root/ | Home for users and root's home |
| /lib/ & /lib64/ | Shared librairies \(dll in windows\) |
| /boot/ | kernel & kernel files |
| /usr/ |  |
| /var/ | Various, contains /var/log |

### File description & Permission

* `ls -l` les représente ainsi : `-rw-r--r--`
  * Le premier char nous indique le type
    * `-` File
    * `d` Directory
    * `l` Link
    * `b` Block device \(anything that can store\)
    * `c` Character device file \(mouse or keyboard\)
  * Puis les permissions sont divisées en 3 catégories : le propriétaire, le groupe et les autres
  * La présence d'une lettre indique une permissions :
    * `r` Lecture
    * `w` Ecriture
    * `x` _E_xécution
* Il y a aussi des permissions spéciales : le SUID & SGID
  * **SUID** permet d'exécuter un fichier avec les droits de son créateur \(ou avec ceux de root ? je suis pas certaine\) au lieu de le faire avec les droits de l'utilisateur lançant la commande. Ca permet par exemple aux utilisateurs d'utiliser la commande `passwd` pour changer leur mdp alors qu'il leur faut pour cela écrire dans /etc/passwd \(réservé à root\). 
  * **SGID** fonctionne de la même façon, à part qu'il utilisera les droits du groupe. De plus, si celui-ci est appliqué à un dossier, tous ces fichiers appartiendront au même groupe.
  * Les deux sont représentés par un `s` au lieu du `x`\(soit de l'utilisateur, soit du groupe\). Si ceux-ci n'ont pas le droit d'exécution, ce sera à la place un `S` majuscule.
* Les permissions ont aussi une notation sous forme de nombre :
  * Read : 4
  * Write : 2
  * Execute : 1
  * SUID : 4 avant le reste
  * SGID : 2 avant le reste

## $PATH

When we use a cmd, the kernel \(i suppose\) search the binary in the directories written in the `$PATH` variable. It goes through each one in order until it finds an executable of this name.

This is why you should always use the full path to the executable, because if someone were to prepend another directory with a malicious version of the cmd to your $PATH, that one will be executed.

* To append to path : `PATH=$PATH:new_path`
* To permanently modify the path, you need to update the files where it's defined. Mais c'est un peu le bordel.
  * De base, les shell regarde dans `$HOME/.profile` pour l'utilisateur courant, mais chaque shell peut avoir son fichier perso \(genre bash utilise .bash-profile\)
  * zsh osef aussi de .profile, mais pour avoir un setup plus globale, j'ai mis la règle dans .profile et j'ai  `source $HOME/.profile` dans `.zshrc`
  * Théoriquement, dans `/etc/profile` ça devrait concerné tout le monde, mais pour moi ça marchait pas
  * De même pour `/etc/login.defs`, peut-être que ça affectait effectivement la variable mais que celle-ci était ensuite override par les clauses des fichiers de zsh & co
* Pour le modifier juste pour cette session, utilisé `export PATH=$PATH:new_path`

Or use this command :

* `export PATH=$PATH:new_path` &lt;- ne marche que pour la session courante

## Process

* Any thing running
* Each process has an address space
* Each process has an id \(_PID_\) 
* First process : à l'époque `init`, maintenant `systemd` \(to check\)
* Chaque process à un parent, quand celui-ci meurt, le 1er pest attribué en tant que parent
* `uid` : quel utilisateur own le process
* `euid` : permet de donner au process des droits différents de ceux de l'utilisateur l'ayant généré
* "niceness" : Determines it's priority in the cpu/ressource usage \(the nicer it is, the less it's gonna use it\)

### Signals

* Signals can be send \(to processes\) by the kernel or other processes
* Terminate tells the process to terminate, kill just does it directly.
* Les process peuvent décider de bloquer ou d'ignored des signaux, mis à part `SIGKILL` et `SIGSTOP`

### Etat

* Runnable : eligible to be schedule for cpu time
* Sleeping : waiting for something
* Zombie state : finished doing what it should and waiting to give back the info and be killed
* Stopped : was doing something but `SIGSTOP` received and waiting for `SIGCON`

## Password

* Change it with `passwd`
* Information are stored in those files : 

### /etc/passwd

* Contains user account information
* Readable by all but writable by root
* One line per user
* Field separated by colons
* 7 fields :
  * Username
  * Password present : an x indicate that a password hash is written in /etc/shadow \(don't know if there's always an x\)
  * UID \(User ID\)
  * GID \(Group ID\)
  * Comment field
  * Home directory
  * Command shell path
* Example : `root:x:0:0:root:/root:/bin/bash`

### /etc/shadow

* Stores password hash
* Only readable & writable by root
* One entry per user listed in /etc/passwd
* Fields separated by colons
* x fields :
  * Username
  * Password hash, format is usually : `$id$salt$hashed`. The id represent the hash algorithm :
    * 1 : MD5
    * 2a : Blowfish
    * 2y : Blowfish
    * 5 : SHA-256
    * 6 : SHA-512
  * Last change : days since Jan 1, 1970 that password was last changed
  * Minimum days between password change
  * Maximum days between password change
  * How many days in advance should the user be warn of expiration
  * Number of days after expiration to disable account
  * Date of expiration \(number of days since Jan 1, 1970\)

## Cron

**Commandes**

* `crontab -l` List your crontab 
* `crontab -e` Edit your crontab 
* `crontab -e -u username` \(As root\) edit a user crontab

**Format** 

* min hour day\_of\_month month\_of\_year week\_day cmd
* Jobs written in the system crontab have an extra field before the cmd for the user id
* Instead of time, you can also specify "boot" to have the cmd run at boot

**Exemple**

* `15 10 * * 3-5 echo "test" >> /home/lyssei/test.txt`
* `15 10 1-10/2 * 5 cmd` At 10h15 every fifth day of the week & for day 1-10 of the month, un jour sur deux
* `15 10 1,2,3,7 * 5 cmd` At 10h15 every fifth day of the week and the 1,2,3 & 7th day of the month

**Fichier**

* `/var/spool/cron/crontabs/` User crontab
* `/etc/cron.d/` Program specific crontab
* `/etc/crontab/` System crontab
* There's an `/etc/cron.allow` file and an `/etc/cron.deny` file \(whitelisting is prefered to blacklisting\)

## DNS & Host

* On spécifie nos hosts localement dans `/etc/hosts`
* On définit les serveurs dns qu'on utilise dans `/etc/resolv.conf`

## Sources

* Man pages
* Misc research
* [TutoriaLinux](https://www.youtube.com/channel/UCvA_wgsX6eFAOXI8Rbg_WiQ)

