# File system

## Introduction

* In linux, everything is a file. 
* File extensions also don't matters but we use them because they're parsed by other programs and help human gain context.

## Pseudo file system

* These are special file systems that contain information about the current running system.
* They only exist in the RAM, have a size of 0, and don't persist after a reboot
* They're often parsed by other programs to be shown in a more pleasing manner or act upon

### /proc/

* `man proc`
* `/proc/[pid]/` Each subdirectory is a process \(identified by its pid\) and contains information about itself such as :
  * `/attr/` Security-related attributes
  * `cmd` Which cmd the process is currently running
  * `cmdline` Which cmd spawned the process
  * `comm` Name of the process
  * `cwd/` symbolic link to the working directory of the process
  * `environ` State of the env variable when the process was spawned
  * `exe` Symbolic link to executable
  * `stat` Status of the process, used by `ps`
  * `status` Same info but more human-readable
  * Diverse files on memory mapping, groups, file descriptors, threads, mount points, namespace, timers, etc
* Varied info files, such as :
  * `/proc/cmdline` Arguments given to the kernel at boot
  * `/proc/filesystems` List all file systems supported by the kernel
  * `/proc/version` Kernel's version
* Diverse files about bus, devices, mounts, groups, cpu, network, kernel variable \(sys\), partitions, timers, etc

## File description & Permission

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

