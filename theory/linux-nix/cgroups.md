# Cgroups

## Introduction

* `cgroups` \(Control Group\) organize processes in hierarchical groups whose usage of memory, cpu, etc, can be limited
* The cgroups interface is provided by the `cgroupfs` pseudo-filesystem
* A `cgroup` is a collection of processes bound to limits or parameters defined in the cgroup filesystem
* A `subsystem` \(or Resource Controller, or Controller\), are kernel component that modifies the behavior of processes in a cgroups.



* The first version of cgroups was created in 2.6.24, but it grew rather complex
* A second version was implemented in Linux 4.5, it's more streamlined
* However, the v1 won't be removed for compatibility issue, and the v2 doesn't have all the controllers of the v1.
* For this reason, the two coexist. You can mount some controller on the v1, and some on the v2, but never both at the same time.



* In v1, controllers may be mounted against a separate cgroups filesystem that provides its own hierarchy.
* In v1, a distinction is made between _processes_ and _tasks_ \(threads\). Processes & threads may belong to different `cgroups`
* It didn't make sense for all controller and introduced problem, so in the v2 it's more limited. The task file also doesn't exist anymore.



* In v1, cgroups requires the kernel option  `CONFIG_CGROUP`
* Each controller also has an associated config option
* To use a controller, you need to mount it against a cgroup filesystem, usually under `sys/fs/cgroup`

\`\`

* These are some of the controllers available in v1

| Controller | Kernel option | Description |
| :--- | :--- | :--- |
| `cpu` | `CONFIG_CGROUP_SCHED` | Garantuee a minimum number of cpu shares |
| `cpu` | `CONFIG_CFS_BANDWITH` | Control bandwith |
| `memory` | `CONFIG_MEMCG` | Limit and report processe memory, kernel memory & swap used |
| `devices` | `CONFIG_CGROUP_DEVICE` | Control creation of device, specified as allow-list & deny-list |
| `freezer` | `CONFIG_CGROUP_FREEZER` | Suspend or restore processes |
| `net_cls` | `CONFIG_CGROUP_NET_CLASSID` | Place a classid identifying a cgroup in packets, can be used in firewall rules |
| `net_prio` | `CONFIG_CGROUP_NET_PRIO` | Allow priorities for cgroups, per network interface |
| `pids` | `CONFIG_CGROUPS_PIDS` | Limit the number of processes in a cgroup |



* The filesystem start with the root cgroup `/` to which all processes belong
* A new cgroup is created by adding a directory
* A process is added to a cgroup by writing it's PID into the `cgroup.procs` file \(only one PID should be added at a time\)
* In v2, processes can only be in leaf node
* In v1, a thread may be moved to another cgroup by adding it's thread id to the `task` file
* You can only remove a group if it doesn't have any child groups or processes.
  * To do it, remove the directory \(files within needn't be deleted\)



* In v1, two files are used to determined whether & how the kernel notifies when a cgroup is empty
*  The path to the program to call when a cgroup is empty is written inside the `release_agent`  file in the root cgroup
* The release\_agent program will call it if the file `notify_on_release` in the specific cgroup contains 1



* In v2, all controller reside within the same hierarchy
* In v2, active controllers are specified in `cgroup.controllers`& `cgroup.subtree_control` 
* In v2, notification are done via the `cgroups.event` file
* All v2 controller have the same name of their v1 predecessor, except for `net_cls`& `net_prio` who doesn't exist in v2. Instead, functionalities were added to `iptables`
* Also, the device controller  doesn't have an interface file. You must instead attach an ePBF \(`PFB_CgROUP_DEVICE`\) program to a cgroup
* Each v2 cgroup contains 2 file :
  *   `cgroup.controllers` List all controllers available to this group
  * `cgroup.subtree_control` List all controller that are active \(enabled\) in this cgroup. It's a subset of the other file
    * To enable or disable a controller, write it's name preceded by a `-` or a `+` \(Example : `+pid`\)
    * A controller enabled higher in the hierachy can't be enabled



* In v2, each non root cgroup contains a read-only file `cgroup.events` following this format :

```text
populated 1
frozen 0
```

* `populated` has the value of 1 if the cgroup & it's descendant has any process
* `frozen` Says if the cgroup is frozen
* This file can be monitored more easily than the `notify_on_realase` of the v1
* The two files of the v1 has been removed



* In v2, each cgroup has a `cgroup.stat` file containing 2 key-value pairs :
  * `nr_descendants` The number of descendant cgroups
  * `nr_dying_descendants` The number of dying descendant cgroups. A cgroup enters the dying state after being deleted, while it wait on the system for resources to be freed



* V2 cgroup also have these two files  :
  * `cgroup.max.depth` Limit the nesting of cgroups, write `max` for infinite
  * `cgroup.max.descendant` Limit the number of descendant, write `max` for infinite



* There's also stuff on delegation & threads in v2, mais j'ai la flemme

## Sources

* man pages
* 


