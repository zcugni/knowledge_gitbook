# Sudo, su, sudoers, etc

## Su

* Allow you to get an interactive shell or run a cmd as another user, given that you know it's password
* Won't change the current directory, but will update `$HOME` & `$SHELL` to that of the user
* If no user is specified, _root_ is assumed
* Without options : returns a _root interactive shell_
* Use :
  * `su [-l | --pty] [-c <cmd> |& -s <shell> |& -g <group>] [user]`
  * `su [--pty |& -c <cmd> |& -s <shell> |& -g <group> |& -p] [user]`

| Commands | Description |
| :--- | :--- |
| `-l` | Return a login shell |
| `-c <cmd>` | Pass the cmd to the shell |
| `--pty`  | Return a pseudo-terminal |
| `-s <shell>` | Use the specified shell instead of the target user's one |
| `-g <group>` | Specify the primary group, only for root |
| `-p` | Preserve env var \(ignore if `-l` is used\) |

## Sudo

* Allows you to execute a command as another user \(by default root\) without knowing it's password \(if it's been previously configured and if you got the authorization to do so\).
* By default, groups of the target user are also copied
* Congif file : `/etc/sudo.conf`
* Usage rules are configured with `sudoers`, see below
* When using sudo, you're usually ask for your password \(not the one of the user you'll run the cmd as\)
  * Credentials are cached by terminal for 15min
  * If a user not authorized in the policy tries to use sudo, a mail is sent \(except for `-l`\)
  * Successful & unsuccessful attempts are logged
  * It can also be configured to log all I/O of the cmds used through it, but it's not a default
* There's 2 methods to choose which env vars can be taken along : whitelists & blacklists
  * Whitelist is preferred, check the man
* Usage:
  * `sudo [-u <user> |& -g <group> |& -EPH] <cmd>`
  * `sudo <-i | -s> [-u <user> |& -g <group> |& -EPH] [cmd]`
  * `sudo -l [-U <user>]`
  * `sudo -e <file> [-u <user> |& -g <group>]`
  * `sudoedit <file> [-u <user> |& -g <group>]`
  * `sudo !!` : Run last cmd as root

| Options | Description |
| :--- | :--- |
| `-u <user>` | Runs the cmd as the specified user |
| `-g <group>` | Runs the cmd as the specified group \(so you're still the same user\) |
| `-l [-U <user>]` | List the allowed & forbidden cmd to use with sudo for the invoking user or the user specified |
| `-s [cmd]` | Either return the target user shell as an interactive shell or pass the cmd to it through it's `-c` option. |
| `-i [cmd]` | Same as `-s` but simulate a login shell |
| `-E` | Ask to keep current env var, might not be authorized |
| `-P` | Keep your own group list |
| `-H` | Set home to target user's home \(may be default\) |

* `-e` & `sudoedit` Edit a file through sudo
  * There's some restrictions to prevent security vuln
    * Symbolic links can't be edited
    * Files in a writable directory by the invoking user can't be edited \(except if we're root\)
    * Can't edit device special files
  * The editor is taken from the `SUDO_EDITOR` var
* group & user can be specified by name or number, prefix number with `#`
  * You may need to escape it. For example, for root do : `\#0`

## Sudoers

* The default _policy plugin_ defining sudo's rules of usage
* Written in `/etc/sudoers`
  * `/etc/sudo.conf` Specify which policy plugin to use
* Composed of _aliases_ & _user specifications_

### Alias

* `<Alias_Type> <NAME> = <value1, value2, ...>`
* You can declare multiple aliases with the same type in 1 line by separating them with `:` `<Alias_Type> <NAME1> = <value1, value2, ..> : <NAME2> = <value>`
* Available types :
  * _User\_Alias_ - List of users, given by :
    * Username
    * System groups \(prefixed by `%`\)
    * Netgroups \(prefixed by `+`\)
  * _Runas\_Alias_ - List of users \(given by UID, prefixed by `#`\)
  * _Host\_Alias_ - List of hostname, ip addresses, networks and netgroups \(prefixed by `+`\)
    * If no netmask is specified, the netmask of the hosts ethernet interface\(s\) will be used
  * _Cmd\_Alias / Cmnd\_Alias_ - List of cmds & dir \(will include all it's file but no sub dir\)
* Prefix an element with `!` to exclude it
* The alias `ALL` is built-in and can be used for each type
* Example :

```bash
User_Alias ADMINS = %admin
User_Alias USERS = tom, dick, harry
User_Alias WEBMASTERS = tom, mary
# Anybody in the USERS alias who isn't in WEBMASTERS or ADMINS aliases
User_Alias LIMITED_USERS = USERS, !WEBMASTERS, !ADMINS

Runas_Alias ROOT = #0

Host_Alias SERVERS = 192.168.0.1, 192.168.0.2, server1
Host_Alias NETWORK = 192.168.0.0/255.255.255.0
Host_Alias WORKSTATIONS = NETWORK, !SERVER

Cmd_Alias SHUTDOWN_CMDS = /sbin/poweroff, /sbin/reboot, /sbin/halt
Cmd_Alias PRINTING_CMDS = /usr/sbin/lpc, /usr/sbin/lprm
Cmd_Alias ADMIN_CMDS = /usr/sbin/passwd, /usr/sbin/useradd, /usr/sbin/userdel, /usr/sbin/usermod, /usr/sbin/visudo
```

### User specification

`<user_list> <host_list> = [(operator_list)] [tag_list] <cmd_list>`

* The user list describe to whom this rule applies
* The host list describe on which host it is applied
* The operator list describe who the user must run as to use the cmd \(by using `-u` or `-g`\)
  * Format :  `(user : group)` \(those can be lists\)
  * Optional, by default must be run by root
  * If group is empty, no group can be specified
  * If user is empty, it must be run by the invoking user and any of the given groups
  * If both are empty, it can only be run by the invoking user
* You can specify an operator by cmd `(user : group) cmd1, cmd2, (user : group) cmd3`
* The tag list are options, the most useful are  :
  * `EXEC` / `NOEXEC` Authorize or prevent the user from launching a shell from another cmd \(like in vim, aka shell escape\)
  * `PASSWD` / `NOPASSWD` Will need to authenticate or not
* Example :

```bash
# This lets the webmasters run all the web commands on the machine 
# "webserver" provided they give a password
WEBMASTERS webserver = WEB_CMDS

# This lets the admins run all the admin commands on the servers
ADMINS SERVERS = ADMIN_CMDS

# This lets all the USERS run admin commands on the workstations provided 
# they give the root password or and admin password (using "sudo -u <username>")
USERS WORKSTATIONS = (ADMINS) ADMIN_CMDS

# This lets "harry" shutdown his own machine without a password
harry harrys-machine = NOPASSWD: SHUTDOWN_CMDS

# And this lets everybody print without requiring a password
ALL ALL = (ALL) NOPASSWD: PRINTING_CMDS
```

## Sources

* man pages
* [https://help.ubuntu.com/community/Sudoers](https://help.ubuntu.com/community/Sudoers)

