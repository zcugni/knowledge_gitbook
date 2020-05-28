# Systemd

## Généralités

* System & service manager lancé après le boot pour démarrer ce qui est nécessaire.
* A remplacé `init`, basé sur `upstart` \(pour mac\)
  * En partie mal reçu car considéré comme ne suivant pas la philosophie de Linux \(il fait trop de choses\). Malgré tout, mtn répandu partout.
  * Il est en partit background compatible avec init et ces SysV script
* Lancé soit au boot \(avec un PID de 1\), soit au login de chaque user
* Rarement utilisé directement par les utilisateurs, il vaut mieux passé par `systemctl` pour cela





* On boot, systemd read `default.target` which is usually a symlink to `graphical.target` or `multi-user.target`
* Process spawned by systemd are placed under control groups \(cgroups\) of the name of the unit 

{% hint style="info" %}
Check what cgroups are
{% endhint %}

## Units

* Systemd permet de gérer différentes entités appelées des _Unit_. Il y a en a 11
* Leur configuration est écrite dans des fichiers, avec des sections communes à toutes et des sections réservés à certains types
* Elles peuvent aussi être générées par des scripts

### Types

| Type | Extension | Description |
| :--- | :--- | :--- |
| Service | `.service` | Describe how to manage a service |
| Socket | `.socket` | Describes a network/IPC socket, or a FIFO buffer that `systemd` uses for socket-based activation. Always has an associated `.service` file that will be started when activity is seen on the socket. |
| Target | `.target` | Provides synchronization points for other units when booting up or changing states. Can be used to bring the system to a new state. Other units specify their relation to targets to become tied to the target’s operations. |
| Device | `.device` | Describe a device that needs systemd \(not all of them do\) |
| Mount | `.mount` | Control mount points. Named after the mount path \(with - instead of /\). Entries within `/etc/fstab` can have units created automatically. |
| Automount | `.automount` |  Configures a mount point that will be automatically mounted. These must be named after the mount point they refer to and must have a matching `.mount` unit to define the specifics of the mount. |
| Timer | `.timer` | Defines a timer, similar to a cron job |
| Swap | `.swap` | Describes swap space on the system. The name of these units must reflect the device or file path of the space. |
| Path | `.path` | Defines a path that can be used for path-based activation. By default, a `.service` unit of the same base name will be started when the path reaches the specified state.  |
| Slice | `.slice` | Associated with Linux Control Group nodes, allowing resources to be restricted or assigned to any processes associated with the slice. The name reflects its hierarchical position within the `cgroup` tree. Units are placed in certain slices by default depending on their type. |
| Scope | `.scope` | Created automatically from information received from bus interfaces. Used to manage sets of system processes that are created externally. |

### Files &  dir

* Systemd reads files from different directories
* All symlink files \(to others units\) in `.wants/`& `.requires/` \(appended to the unit name\) will be added to it's Wants & Requires directives
* The base unit files are written in  `/lib/systemd/system` when installed, don't edit them
  * Overwrite them by creating files with the same name in `/etc/systemd/system`
  * To extend or overwrite only some of the directives of a unit file, you need to create a `.d` directory of the same name are write them into a `.conf` file
* Files created at runtime are written into `/run/systemd/system`, which is read after /lib/systemd/system but before /etc/systemd/system
* The `/usr/lib/systemd/user/` directory is the default location where unit files are installed by packages. Unit files in the default directory should not be altered.
* The `/run/systemd/system/` directory is the runtime location for unit files.
* The `/etc/systemd/system/` directory stores unit files that extend a service. This directory will take precedence over unit files located anywhere else in the system.

{% hint style="info" %}
Check the template thingy
{% endhint %}

### Base config section

* Options are space separated list \(except for Description\)
* Format `Option=value list`

#### \[UNIT\]

Main options are as follows :

| Option | Description |
| :--- | :--- |
| Description | Simple name description \(not a sentence\). Something like "Apache2 Web Server". Will be used in these kind of sentences "Starting ...", "Failed to start ..." |
| Documentation | List of uri \(ordered by relevance\). Accepted uris are : "http://", "https://", "file:", "info:", "man:" |
| Wants | Ask for target units, but can run even if they fail. |
| Requires | Needs target units, will stop if they fails \(but not for all cases where they stops\) |
| BindsTo | Needs target units, will stop if they fails or stop |
| Conflicts | Can't run simultaneously to target units, the others will be stopped |
| Before/After | Target units must be started before or after another one. They'll also be stopped in the opposing order. If these aren't set, they will be started/stopped in parallel. If one is started and the other stopped, the shutdown is always done first. |

* There's also condition that you can specify that will skip the launch of a unit without putting it in a fail state. These let's you start a unit depending of the kernel/runtime environment, etc. but i won't describe them in details

#### \[INSTALL\]

* Those are not used by systemd during runtime but by  the `enable` & `disable` commands of systemctl

| Option | Description |
| :--- | :--- |
| Alias | Symlink will be created from those names to this unit. Not supported by mount, slice, swap, and automount units |
| WantedBy / RequiredBy | Adds a symlink to this unit in the .wants/ and .requires/ directory of the target units |
| Also | Target units to install/uninstall when this one is |

Check default instance

{% hint style="info" %}
Read the uri & daemon man
{% endhint %}

## Systemctl

* Check & control the state of the "systemd" system and service manager

| Options | Description |
| :--- | :--- |
| `list-units` |  |
| `list-sockets` |  |
| `list-timers` |  |
| `start ...` |  |
| `stop ...` |  |
| `reload ...` |  |
| `restart ...` |  |
| `status ...` |  |

## Sources

* man pages
* [https://www.digitalocean.com/community/tutorials/systemd-essentials-working-with-services-units-and-the-journal](https://www.digitalocean.com/community/tutorials/systemd-essentials-working-with-services-units-and-the-journal)
* [https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files](https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files)
* [https://www.linode.com/docs/quick-answers/linux-essentials/what-is-systemd/](https://www.linode.com/docs/quick-answers/linux-essentials/what-is-systemd/)
* [https://www.linux.com/training-tutorials/understanding-and-using-systemd/](https://www.linux.com/training-tutorials/understanding-and-using-systemd/)

