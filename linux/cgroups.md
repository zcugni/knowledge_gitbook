# Cgroups

## Terminology

* `cgroups` \(control group\) organize processes in hierarchical groups whose usage of memory, cpu, etc, can be limited
* The cgroup interface is provided by the `cgroupfs` files system
* Cgroups are the actual groups of processes while the `subsystems` \(or resource controllers, or controllers\) are kernel components that modifies their behavior

## Version

* The first version of cgroups was created in Linux 2.6.24, but it grew rather complex
* A second version was implemented in Linux 4.5, it's more streamlined
* However, the v1 won't be removed for compatibility issues, and the v2 doesn't have all the controllers of the v1
* For this reason, the two coexist. You can mount some controller on the v1, and some on the v2, but never both at the same time

## Structure

* In the `/sys/fs/cgroup` directory, there's a directory per controller \(for V1\) and an `unified` directory \(for v2\)
* Each cgroup contains certain files and sub directories that are child cgroup
* For a process to belong to a group, write it's PID into the correspond `cgroup.procs` file
  * Only one PID should be added at a time
* In v2, processes can only be in leaf node
* You can only remove a group if it doesn't have any child groups or processes
  * To do it, remove the directory \(files within needn't be deleted\)

## Controller

* Those are some of the available controllers in v1
* They require a kernel option to be set

| Controller | Kernel option | Description |
| :--- | :--- | :--- |
| `cpu` | `CONFIG_CGROUP_SCHED` | Guarantee a minimum number of cpu shares |
| `cpu` | `CONFIG_CFS_BANDWITH` | Control bandwidth |
| `memory` | `CONFIG_MEMCG` | Limit and report process memory, kernel memory & swap used |
| `devices` | `CONFIG_CGROUP_DEVICE` | Control creation of device, specified as allow-list & deny-list |
| `freezer` | `CONFIG_CGROUP_FREEZER` | Suspend or restore processes |
| `net_cls` | `CONFIG_CGROUP_NET_CLASSID` | Place a classid identifying a cgroup in packets, can be used in firewall rules |
| `net_prio` | `CONFIG_CGROUP_NET_PRIO` | Allow priorities for cgroups, per network interface |
| `pids` | `CONFIG_CGROUPS_PIDS` | Limit the number of processes in a cgroup |

* All v2 controller have the same name of their v1 predecessor, except for `net_cls`& `net_prio` who don't exist in v2. Instead, functionalities were added to `iptables`
* Each v2 cgroup contains 2 file :
  *  `cgroup.controllers` List all controllers available to this group
  * `cgroup.subtree_control` List all controller that are active \(enabled\) in this cgroup. It's a subset of the other file
    * To enable or disable a controller, write it's name preceded by a `-` or a `+` \(Example : `+memory`\)
    * A controller disabled higher in the hierarchy can't be enabled

## Threads

#### V1

* A distinction is made between _processes_ and _tasks_ \(threads\)
* Processes & threads may belong to different `cgroups`
* To move it to another cgroup, add it's thread id to the `task` file

#### V2

* The `task` file is removed

{% hint style="info" %}
It's a bit long, i'll read it another time
{% endhint %}

## Notification

#### V1

* Two files are used to determined whether & how the kernel notifies when a cgroup is empty
*  The path to the program to call when a cgroup is empty is written inside the `release_agent`  file in the root cgroup
* The release\_agent program will call it if the file `notify_on_release` in the specific cgroup contains 1

#### V2

* Each non root cgroup contains a read-only file `cgroup.events` containing 2 key-value pairs :
  * `populated` has the value of 1 if the cgroup & it's descendant has any process
  * `frozen` Says if the cgroup is frozen
* This file can be monitored more easily than the `notify_on_realase` of the v1
* The two files of the v1 has been removed

## V2 specifics

* Each cgroup has a `cgroup.stat` file containing 2 key-value pairs :
  * `nr_descendants` The number of descendant cgroups
  * `nr_dying_descendants` The number of dying descendant cgroups. A cgroup enters the dying state after being deleted, while it wait on the system for resources to be freed
* They also have these two files  :
  * `cgroup.max.depth` Limit the nesting of cgroups, write `max` for infinite
  * `cgroup.max.descendant` Limit the number of descendant, write `max` for infinite

## Delegation

{% hint style="info" %}
To do
{% endhint %}

## Sources

* man pages



