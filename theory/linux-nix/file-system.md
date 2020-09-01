# File system

## Generalities

* All files accessible in a Unix system are a part of one big tree rooted at `/`
* These files can be spread out on different devices, so they need to be mounted to the main tree, that is a VFS \(virtual file system\)
* Lots of things are represented by filesystems \(for example systemd or cgroups\)
* File extensions don't matters but we use them because they're parsed by other programs and help human gain context.

## Special files

* They're interface for a device driver, when a request is made \(aka read or write\), the associated driver deals with it
* They're stored in `/dev`
* There's 2 types of special files :
  * Character special files \(or character device files\) : each character is handled one by one
  * Block special files \(or block device files\) : calls can be buffered, characters are handled block by block

## File descriptor

* Each file created or opened get an associated file descriptor that can be use to target the file with read or write call \(for example\)
* File descriptor are per processes, you can check all the fd of a process in `/proc/[PID]/fd`
  * So the same file might be accessed from different fd number depending on the process
* There's 3 special fd :
  * 0 : STDIN
  * 1 : STDOUT
  * 2 : STDERR

## Pseudo file system

* These are special filesystems that contain information about the current running system
* They only exist in the RAM, have a size of 0, and don't persist after a reboot
* They're often parsed by other programs to be shown in a more pleasing manner or act upon

### /proc/

* `man proc`
* `/proc/[pid]/` Each subdirectory is a process \(identified by its pid\) and contains information about itself such as :
  * `/attr/` Security-related attributes
  * `cgroup` To which cgroup the process belong
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
  * La présence d'une lettre indique une permission :
    * `r` Lecture
    * `w` Ecriture
    * `x` Exécution
    * `s` SUID/SGID avec exécution \(à la place du x\)
* SUID & SGID
  * Ils permettent d'exécuter un fichier avec les droits de son créateur au lieu de le faire avec les droits de l'utilisateur lançant la commande
  * Pour des raisons de sécurité, **cela ne fonctionnera que sur les exe, pas sur les scripts** \([ref](https://unix.stackexchange.com/questions/364/allow-setuid-on-shell-scripts)\)
  * SUID utilisera l'utilisateur, SGID le groupe
  * `passwd` est un exemple type vu qu'il est lancé par les utilisateurs mais qu'il lui faut modifier `/etc/passwd` \(écrivable uniquement par root\).

## Sources

* man pages
* Stack overflow - [Special files](https://unix.stackexchange.com/questions/60034/what-are-character-special-and-block-special-files-in-a-unix-system)
* Stack overflow - [File descriptor](https://stackoverflow.com/questions/5256599/what-are-file-descriptors-explained-in-simple-terms)

