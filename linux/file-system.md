# File system

## Generalities

* All files accessible in a Unix system are a part of one big tree rooted at `/`
* These files can be spread out on different devices, so they need to be mounted to the main tree, that is a **Virtual File System** \(VFS\)
* In Linux, every thing is a file or represented by filesystems \(for example systemd or cgroups\)
* File extensions don't matters but we use them because they're parsed by other programs and help human gain context.

## File descriptors

* Each file created or opened get an associated file descriptor that can be use to target it with read or write call \(for example\)
* File descriptors are per processes, you can check all the fd of a process in `/proc/[PID]/fd`
  * So the same file might be accessed from different fd number depending on the process
* There's 3 special fd :
  * 0 : `STDIN`
  * 1 : `STDOUT`
  * 2 : `STDERR`

## Permissions

* `ls -l` represent them as such : `-rw-r--r--`
* The first character indicates the type :
  * `-` File
  * `d` Directory
  * `l` Link
  * `b` Block device file
  * `c` Character device file
* Then 3 blocks of 3 letters indicates permissions, in order they represent :
  * The owner
  * The group
  * Others
* Additionally to `ls` letters, `chmod` also uses numbers to represent permission :
  * Read : `r` / `4`
  * Write : `w` / `2`
  * Execute : `x` / `1`
  * Nothing :  `-` / `0`
  * SUID with execution : `s` \(replace the x\) / `4` \(before the rest\)
  * SGID with execution  : `s` \(replace the x\) / `2` \(before the rest\)
  * SUID without execution : `S` \(replace the x\) / `4` \(before the rest\)
  * SGID without execution : `S` \(replace the x\) / `2` \(before the rest\)

### SUID / GUID

* Allows you to execute a binary with the rights of it's creator instead of yours
* For security reasons, **it only works on exes, not scripts** \([ref](https://unix.stackexchange.com/questions/364/allow-setuid-on-shell-scripts)\)
* SUID will use the creator owner while SGID the creator group
* `passwd` is the typical example
  * It's launched by the users, but only root can modify `/etc/passwd`, so the SUID bit let the user temporarily write in this file

### chmod

* Lets you change a file permissions
* I explained earlier the notation for the permission, the notation for the 3 entities are as such :
  * `u` The owner of the file
  * `g` The group owner of the file
  * `o` Others
* You can change permissions in different ways :
  * `chmod u+w <file>`, `chmod o-x <file>`, `chmod g=wr <file>`, `chmod u+s <file>` Add, delete or define permissions for an entity
  * `chmod 754 file` Define permissions by adding each value
    * The first number is for the owner, the second the group and the third others
    * With a SUID bit, it's like this :`chmod 4755 file`

### chown

Change file owner

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

## Special / device files

* They're interfaces for device drivers, when a request is made \(aka read or write\), the associated driver deals with it
* They're stored in `/dev`
* There's 2 types of special files :
  * **Character special files** \(or character device files\) : each character is handled one by one \(usually stuff like keyboard or mouse\)
  * **Block special files** \(or block device files\) : calls can be buffered, characters are handled block by block \(usually stuff that can store\)

## Weird file name

* To interact with a file with a strange or composed name, you can use :
  * Quotes :`"file name with space"`
  * `./` : `./-`

## Sources

* man pages
* Stack overflow - [Special files](https://unix.stackexchange.com/questions/60034/what-are-character-special-and-block-special-files-in-a-unix-system)
* Stack overflow - [File descriptor](https://stackoverflow.com/questions/5256599/what-are-file-descriptors-explained-in-simple-terms)

