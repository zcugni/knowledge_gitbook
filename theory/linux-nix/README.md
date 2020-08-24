# Linux / \*nix

{% hint style="info" %}
Read this book : [Unix Linux System Administration Handbook](https://www.amazon.fr/UNIX-Linux-System-Administration-Handbook/dp/0134277554)
{% endhint %}

## $PATH

When we use a cmd, the kernel \(i suppose\) searches the binary in the directories written in the `$PATH` variable. It goes through each one in order until it finds an executable of this name.

This is why you should always use the full path to the executable, because if someone were to prepend another directory with a malicious version of the cmd to your $PATH, that one will be executed.

* To append to $PATH : `PATH=$PATH:new_path`
* To permanently modify the path, you need to update the files where it's defined. Mais c'est un peu le bordel.
  * De base, les shell regarde dans `$HOME/.profile` pour l'utilisateur courant, mais chaque shell peut avoir son fichier perso \(genre bash utilise .bash-profile\)
  * zsh osef aussi de .profile, mais pour avoir un setup plus globale, j'ai mis la règle dans .profile et j'ai  `source $HOME/.profile` dans `.zshrc`
  * Théoriquement, dans `/etc/profile` ça devrait concerné tout le monde, mais pour moi ça marchait pas
  * De même pour `/etc/login.defs`, peut-être que ça affectait effectivement la variable mais que celle-ci était ensuite override par les clauses des fichiers de zsh & co
* Pour le modifier juste pour cette session, utiliser`export PATH=$PATH:new_path`

## Hierarchy

* `man hier`
* `/etc/`
  * Configuration files
  * `/etc/passwd` & `/etc/shadow` : used for password, read [this](https://zcugni.gitbook.io/notes/theory/linux-nix#password)
* `/opt/`
  * For optional software, the guideline is not really defined
* `/dev/`
  * All devices
  * Regular part disk are written in the form "sda1", "sda2", "sdb1". The letter representing the disk and the number the partition.
  * Contains \(for bash\) /dev/tcp/ to connect to
* `/proc/` is detailed [here](https://zcugni.gitbook.io/notes/theory/linux-nix/file-system#proc)
* Others :

| Directory | Content |
| :--- | :--- |
| /sbin/ & /bin/ | Secure binaries \(need root\) & "normal" binaries |
| /tmp/ | For temporary files, blown away each restart |
| /home/ & /root/ | Home for users and root's home |
| /lib/ & /lib64/ | Shared librairies \(dll in windows\) |
| /boot/ | kernel & kernel files |
| /usr/ |  |
| /var/ | Various, contains /var/log |

## Process

* Anything running
* Each process has an address space and an id \(_PID_\) 
* First process : before `init`, now [`systemd`](https://zcugni.gitbook.io/notes/theory/systemd)
* Each process has a parent. If it dies, the previous one is attributed as parent
* _Uid_ : Who owns the process
* _Euid_ : Lets you give other permissions than the ones of the owner to the process.
* "Niceness" : Determines its priority in the cpu/resource usage \(the nicer it is, the less it's gonna use it\)

### Signals

* Signals can be sent to processes by the kernel or other processes
* "Terminate" ask the process to terminate itself, "kill" just does it directly.
* Processes can decide to block and ignore signals, except for `SIGKILL` & `SIGSTOP`

### State

* Runnable : Eligible to be scheduled for CPU time
* Sleeping : Waiting for something
* Zombie state : Finished doing what it should and waiting to give back the info and be killed
* Stopped : Was doing something but `SIGSTOP` received and waiting for `SIGCON`

## Password

* Change it with `passwd`
* Information are stored in those files : 

### /etc/passwd

* Contains user account information
* Readable by all but writable only by root
* One line per user
* Field separated by colons
* 7 fields :
  * Username
  * Password present : an x indicates that a password hash is written in /etc/shadow
  * UID \(User ID\)
  * GID \(Group ID\)
  * Comment field
  * Home directory
  * Command shell path
* Example : `root:x:0:0:root:/root:/bin/bash`

### /etc/shadow

* Stores password hash
* Only readable & writable by root
* One entry per user listed in `/etc/passwd`
* Fields separated by colons
* 8 fields :
  * Username
  * Password hash, the format is usually : `$id$salt$hashed`. The id represents the hash algorithm :
    * 1 : MD5
    * 2a : Blowfish
    * 2y : Blowfish
    * 5 : SHA-256
    * 6 : SHA-512
  * Last change : days since Jan 1, 1970 since the password was last changed
  * Min days between password change
  * Max days between password change
  * How many days in advance should the user be warned of expiration
  * Number of days after expiration to disable the account
  * Date of expiration \(number of days since Jan 1, 1970\)
* Accounts that don't have a password can't log in, and there's an `*` or a `!` in their hash field.

## DNS & Host

* On spécifie nos hosts localement dans `/etc/hosts`
* On définit les serveurs dns qu'on utilise dans `/etc/resolv.conf`

## Sources

* Man pages
* Misc research
* [TutoriaLinux](https://www.youtube.com/channel/UCvA_wgsX6eFAOXI8Rbg_WiQ)


