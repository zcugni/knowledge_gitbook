# Mount

## Generalities

* Mount a filesystem
* `mount` & `unmount`
* `mount [device] [dir]`
  * dir is also called a _mountpoint_
* If the device or the dir aren't given, mount will look for a device or a mountpoint in the `/etc/fstab` file
* The previous content of the dir becomes invisible, and the pathname of the dir refers to the root of the filesystem in the device
* Filesystem may be mounted more than once and even multiple times on the same mountpoint. This isn't manage by the mount command but by the kernel and the filesystem driver
* There's a lot of details that i won't describe, check the man in doubt

## Options

* These are mount options, not command line parameters
* They're file type independent options, there's also type-specific options but there's too many so i won't describe them
* There's also a lot of options dealing with how access time, etc are updated

### General

| Option | Description | Opposite |
| :--- | :--- | :--- |
| `async` | I/O to filesystem should be done asynchronously | `sync` |
| `auto` | Can be mounted with the `-a` parameter | `noauto` |
| `exec` | Permit execution of binaries | `noexec` |
| `dev` | Interpret character or block special device | `nodev` |
| `_netdev` | The filesystem reside in a device that needs network access |  |
| `nofail` | Do not report error if the device doesn't exist |  |
| `suid` | Honor suid capabilities within this filesystem | `nosuid` |
| `silent` | Turn on silent flag | `loud` |
| `remount` | Attempt to remount an already mounted system \(to change it's options basically\) |  |
| `ro` | Mount the filesystem read-only |  |
| `rw` | Mount the filesystem read-write |  |

### Rights

* By default, only a privileged user can mount a filesystem, but these options can override that

| Option | Description |
| :--- | :--- |
| `nouser` | \(Default\) Forbid a non-privileged user from mounting the filesystem |
| `user` | Allow a non-privileged user to mount the filesystem |
| `group` | Allow a non privileged user to mount the filesystem if they share a group |
| `owner` | Allow a non-privileged user to mount the filesystem if it's the owner of the device |
| `users` | Allow a non-privileged user to unmount the filesystem even if it's not him that mounted it |

* `user` & `users` imply `noexec`, `nosuid` & `nodev` if they're not overridden
* `group` & `owner` imply `nosuid` & `nodev` if they're not overridden

## /etc/fstab

* This file describe what filesystem should be mounted where and with which options
* It's read sequentially by programs so the order is important

### Syntax

* One filesystem by line
* Fields separated by spaces or tabs
* The hashtag is used for comment \(\#\)

### Fields

* Format : `<fs_spec> <mount_point> <fs_type> <options> <fs_freq> <fs_passno>`
* Example : `LABEL=t-home2 /home ext4 defaults,auto_da_alloc 0 2`
* `fs_spec` Define the block special device/remote filesystem/filesystem image to be mounted or swap file/swap partition to be enabled
  * For ordinary mounts, it's a link to a block device like `/dev/cdrom/`
  * For NFS mount, it's `<host>:<dir>`
  * For filesystem with no storage, a simple string \(like `proc`\)
  * `LABEL=<label>` or `UUID=<UUID>` can be given instead of the device name, it's more reliable
  * `PARTLABEL=<partlabel>` or `PARTUUID=<partuuid>` Can be used for partitions
* The mount point is a path, for swap file it should be `none`
* Type of filesystem, comma-separated list
  * `swap` Used for swap files
  * `none` Used for bind or move
  * subtype are supported, use a suffix `.subtype` \(for example `fuse.sshfs`\)
* Mount options, comma-separated list
  * Contains at least the type of mount \(`ro` or `rw`\)
* `fs_freq` Used by dump, default to 0 if not present
* `fs_passno` Used by `fsck` to determine the order of filesystem checks at boot time
  * The root filesystem should have a passno of `1`
  * Others should have `2`
  * Default to 0 if not present 

## Command line parameters

### General

| Little name | Long Name | Description |
| :--- | :--- | :--- |
| `-B <old_dir> <new_dir>` | `--bind <old_dir> <new_dir>` | Mount an already mounted filesystem somewhere else  \(so that its content is available in both places\). \(It can also be a part of the filesystem\) |
| `-R <old_dir> <new_dir>` | `--rbind <old_dir> <new_dir>` | Same but recursive with sub mount |
| `-m <old_dir> <new_dir>` | `--move <old_dir> <new_dir>` | Move a mounted filesystem elsewhere |
| `-t <fstype>` | `--type <fstype>` | Indicate the filesystem type \(`ext2`, `ext3`, `nfs`, etc\). If `-t` is not given, or the `auto` option is set, mount will try to guess the type |
| \`\` | `--source <device>` | Explicitly define the device |
|   | `--target <dir>` | Explicitly define the dir \(mountpoint\) |
| `-v` |  | Verbose |
| `-f` | `--fake` | Simulate a mount, use with `-v` to debug |

### Auto

* `-a` or `--all` Mount all filesystems mentioned in `/etc/fstab` \(that have the `auto` option set\)
  * `-F` or `--fork` Used with `-a`, fork mount for each device. More rapid, but the order of mounts is undefined
  * `-O <options>` or `--test-opts <options>` Limits the set of filesystem to which `-a` is applied

### Options

* `-o <option1,option2>`  Specify options to use
* `--options-mode <mode>` Specify how to combine options form `/etc/fstab/` and the command line. These are available :
  * `prepend` \(Default\) fstab options are evaluated first
  * `append` fstab options are evaluated after
  * `ignore` fstab options are ignored
  * `replace` fstab options are replaced
  * If conflict arise, the last option wins

## Propagation

* Mounts can have different propagation flags :
  * `shared` Create mirrors of the mount, change will propagate through them
  * `slave` Receive propagation from it's master but not vice-versa
  * `private` No propagation ability
  * `unbindable` Private mount that cannot be cloned through `bind`
  * Syntax is `mount --make-* <mountpoint>`
    * The star can be replaced by the name above \(aka `--make-shared)`
    * Or the name above preceded by an r \(aka `--make-rshared)` to make it recursive
  * Mount doesn't read `fstab` when a `--make-*` operation is done, and no other operation can be done simultaneously 
  * Propagation flags can also be set in `/etc/fstab`

## List mount

* Using `mount` alone will list all mounted filesystem, but it's only kept for backward compatibility. Use`findmnt`
* Currently mounted filesystem are listed in `/proc/mounts`

## Sources

* man pages

