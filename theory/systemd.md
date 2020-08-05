# Systemd

## Généralités

* System & service manager lancé après le boot pour démarrer ce qui est nécessaire.
* A remplacé `init`, basé sur `upstart` \(pour mac\)
  * En partie mal reçu car considéré comme ne suivant pas la philosophie de Linux \(il fait trop de choses\). Malgré tout, mtn répandu partout.
  * Il est en partit background compatible avec init et ces SysV script
* Lancé soit au boot \(avec un PID de 1\), soit au login de chaque user
* Rarement utilisé directement par les utilisateurs, il vaut mieux passé par `systemctl` pour cela
* On boot, systemd read `default.target` which is usually a symbolic link to `graphical.target` or `multi-user.target`

## Units

* Systemd permet de gérer différentes entités appelées des _Unit_. Il y a en a 11
* Leur configuration est écrite dans des fichiers, avec des sections communes à toutes et des sections réservées à certains types
* Elles peuvent aussi être générées par des scripts
* Process spawned by systemd are placed under control groups \(cgroups\) of the name of the unit 

### Types

| Type | Extension | Description |
| :--- | :--- | :--- |
| Service | `.service` | Describe how to manage a service |
| Socket | `.socket` | Describes a network/IPC socket or a FIFO buffer that `systemd` uses for socket-based activation. Always has an associated `.service` file that will be started when activity is seen on the socket. |
| Target | `.target` | Provides synchronization points for other units when booting up or changing states. Can be used to bring the system to a new state. Other units specify their relation to targets to become tied to the target’s operations. |
| Device | `.device` | Describe a device that needs systemd \(not all of them do\) |
| Mount | `.mount` | Control mount points. Named after the mount path \(with - instead of /\). Entries within `/etc/fstab` can have units created automatically. |
| Automount | `.automount` |  Configures a mount point that will be automatically mounted. These must be named after the mount point they refer to and must have a matching `.mount` unit to define the specifics of the mount. |
| Timer | `.timer` | Defines a timer, similar to a cron job |
| Swap | `.swap` | Describes swap space on the system. The name of these units must reflect the device or file path of the space. |
| Path | `.path` | Defines a path that can be used for path-based activation. By default, a `.service` unit of the same base name will be started when the path reaches the specified state.  |
| Slice | `.slice` | Associated with Linux Control Group nodes, allowing resources to be restricted or assigned to any processes associated with the slice. The name reflects its hierarchical position within the `cgroup` tree. Units are placed in certain slices by default depending on their type. |
| Scope | `.scope` | Created automatically from information received from bus interfaces. Used to manage sets of system processes that are created externally. |

### Special  directories

* `/lib/systemd/system/` Base unit written here on installation, don't edit them
* `/run/systemd/system/` Files created at runtime
* `/usr/lib/systemd/user/` Unit files installed by packages, don't edit them
* `/etc/systemd/system/` Unit files that extend or overwrite a service
  * To extend or overwrite only some of the directives of a file, create a `<unit_name>.d/` dir and write them into a `.conf` file.
* The read order is as follows : `/usr/lib/systemd/user/ -> /run/systemd/system/ -> /etc/systemd/system/`
* All symbolic link files \(to others units\) in `<unit_name>.wants/`& `<unit_name>.requires/` will be added to the Wants & Requires directives of it's unit.

{% hint style="info" %}
Check the template thingy
{% endhint %}

### Base config section

* Options are space separated list \(except for Description\)
* Format `Option=value_list`

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

* There's also conditions that you can specify that will skip the launch of a unit without putting it in a fail state. These let's you start a unit depending of the kernel/runtime environment, etc. but i won't describe them in details

#### \[INSTALL\]

* Those are not used by systemd during runtime but by  the `enable` & `disable` commands of systemctl

| Option | Description |
| :--- | :--- |
| Alias | Symbolic links will be created from those names to this unit. Not supported by mount, slice, swap, and automount units |
| WantedBy / RequiredBy | Adds a symbolic link to this unit in the .wants/ and .requires/ directory of the target units |
| Also | Target units to install/uninstall when this one is |

{% hint style="info" %}
Read the uri & daemon man

Check default instance
{% endhint %}

## Systemctl

* Check & control the state of the "systemd" system and service manager

| Options |
| :--- |
| `list-units` |
| `list-sockets` |
| `list-timers` |
| `start ...` |
| `stop ...` |
| `reload ...` |
| `restart ...` |
| `status ...` |

## Sources

* man pages
* [https://www.digitalocean.com/community/tutorials/systemd-essentials-working-with-services-units-and-the-journal](https://www.digitalocean.com/community/tutorials/systemd-essentials-working-with-services-units-and-the-journal)
* [https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files](https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files)
* [https://www.linode.com/docs/quick-answers/linux-essentials/what-is-systemd/](https://www.linode.com/docs/quick-answers/linux-essentials/what-is-systemd/)
* [https://www.linux.com/training-tutorials/understanding-and-using-systemd/](https://www.linux.com/training-tutorials/understanding-and-using-systemd/)

