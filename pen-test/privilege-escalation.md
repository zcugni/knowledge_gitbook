# Linux Privilege Escalation

## Disclaimer / Explanation

I know linpeas & linenum are good priv esc scripts, but as I'm new to that, all this information is kind of overwhelming. I prefer to do it step by step myself.

So i'm going to describe what to check for step by step here. This is **heavily** taken from [hacktricks](https://book.hacktricks.xyz/) \(linpeas's author\), but i'm modifying some stuff and adding what I learned in hack the box.

It is still under development.

## Kernel exploits

* Check the kernel version with `cat /proc/version` or `uname -a`
* Use google and searchsploit to see if it's vulnerable
* There's also tools and compiled exploit available at [https://book.hacktricks.xyz/linux-unix/privilege-escalation\#kernel-exploits](https://book.hacktricks.xyz/linux-unix/privilege-escalation#kernel-exploits)

## Gain info on user

```bash
id || (whoami && groups) 2> /dev/null # Me & my groups
cat /etc/passwd | cut -d: -f1 # All users
cat /etc/passwd | grep "sh$" # Users with console
awk -F: '($3 == "0") {print}' /etc/passwd # Superusers
w # Currently logged in users
last | tail # Login history
```

## Sudo

### Exploits

* According to searchsploit, check if it's a vulnerable version of sudo :

```bash
sudo -V | grep "Sudo ver" | grep "1.6.8p9\|1.6.9p18\|1.8.14\|1.8.20\|1.6.9p21\|1.7.2p4\|1\.8\.[0123]$\|1\.3\.[^1]\|1\.4\.\d*\|1\.5\.\d*\|1\.6\.\d*\|1.5$\|1.6$"
```

### Rights

* Check what you can run with `sudo -l` :
  * If it's too permissive, you may be able to priv esc just by using one of the authorized cmds
  * There's also a lot of shell escapes, check [gtfobins](https://gtfobins.github.io/)

## SUID

* Check if any program has the suid bit set when they shouldn't, you may be able to hijack a cmd :

```bash
find / -perm -4000 2> /dev/null #Find all SUID binaries
find / -perm -2000 2> /dev/null # Find all SGID binaries
```

## Hijack $PATH

* Given that : 
  * A cmd is called without an absolute path to it's binary
  * You can write in some folder referenced by `$PATH`
  * This folder is referenced earlier than the folder of the cmd's binary
* You can create a backdoor by writing a file named like the binary

### Systemd

* With the same logic, you can hijack 3 types of systemd files :
  * `.service`, which execute cmds
  * `.timer`, which execute services
  * `.socket`, whichreacts to connection

## Processes

* Check which process are running and if they have more rights than they should :

```bash
ps aux
top # Will update continuously
```

* There's also tools like [pspy](https://github.com/DominicBreuker/pspy.git)

### Process memory

* Some services save **credentials in clear text inside the memory**
* For example, if you have access to the memory of an FTP service you could do that :

```bash
gdb -p <FTP_PROCESS_PID>
(gdb) info proc mappings
(gdb) q
(gdb) dump memory /tmp/mem_ftp <START_HEAD> <END_HEAD>
(gdb) q
strings /tmp/mem_ftp #User and password
```

## Cron

* Check crontab to see if any of those are used : 
  * Relative paths \(same logic as with `$PATH`\)
  * Symbolic links
  * Wildcards, you can do these types of things with them :

```bash
# Line present in the root cron : 
rsync -a *.sh rsync://host.back/src/rbd 
# Create a file called "-e sh myscript.sh" to have the script execute our file
```

## Read sensible data

* Password :
  * Read`/etc/passwd/` to gain an idea of users
  * Try to crack the passwords in `/etc/shadow` if you have access
  * Use [LaZagne](https://github.com/AlessandroZ/LaZagne) to extract password from files
* Check for interesting files in :
  * `/tmp/`
  * `/var/tmp/`
  * `/var/mail`
  * `/var/spool/mail`
  * `/etc/exports`
* Shell :
  * Read history if you can : `find / -name "*_history"`
  * Check config in `.profile` & `.bashrc`
* Web files : 

```bash
ls -alhR /var/www/ 2>/dev/null
ls -alhR /srv/www/htdocs/ 2>/dev/null
ls -alhR /usr/local/www/apache22/data/
ls -alhR /opt/lampp/htdocs/ 2>/dev/null
```

* Backups :`find / -type f ( -name "backup" -o -name ".bak" -o -name ".bck" -o -name "*.bk" ) 2>/dev/nulll` \(hacktricks has a version that searches only in some folders, use it if it's too long\)
* Others :
  * `.sudo_as_admin_successful`
  * `httpd.conf`
  * `.plan`
  * `htpasswd`
  * `.git-credentials`
  * `.rhosts`
  * `hosts.equiv`
* Read `env`

## Open Shell Session

Check if there's any interesting [tmux](https://app.gitbook.com/@zcugni/s/notes/~/drafts/-ME8c67PWhoFo8pMESCD/tools/linux-bash-command/tmux) or [screen](https://zcugni.gitbook.io/notes/tools/linux-bash-command#screen-multi-plexer) session that you can connect to.

## Software exploit

```bash
dpkg -l #Debian
rpm -qa #Centos
```

If you have SSH access you can also use **openVAS** to check for vulnerable software.

## Hack the box

J'utilise évidemment les scripts, mais je voulais noter ici les éléments spécifiques à chercher pour résoudre certaines boxes.

* **Ports** ouverts sur localhost ? \(Port forward si besoin\)
* Obtenir la liste de tout les fichiers own par root mais n'appartenant pas au groupe de root \(script custom\) : `find / -user root ! -group root -ls 2> /dev/null`
  * Si on arrive à devenir un des membres d'un de ces groupes et si un de ces fichiers nous permet de lancer une commande, celle-ci sera lancée en tant que root 
* Si on arrive sur un **serveur web,** chercher le fichier de config de la db et tenter de se co pour ensuite explorer les tables d'utilisateurs & co
* Si un script change les droits d'un fichier, si on fait un **lien symbolique** entre ce nom de fichier et un fichier qui nous intéresse, cela changera aussi les droits du fichier qui nous intéresse.

## To add

### Big UID

Some Linux versions were affected by a bug that allow users with **UID &gt; INT\_MAX** to escalate privileges. More info: [here](https://gitlab.freedesktop.org/polkit/polkit/issues/74),  [here](https://github.com/mirchr/security-research/blob/master/vulnerabilities/CVE-2018-19788.sh) and [here](https://twitter.com/paragonsec/status/1071152249529884674).  
**Exploit it** using: **`systemd-run -t /bin/bash`**

### Capabilities

It seem a bit advance & specific, but might be interesting. Check [https://book.hacktricks.xyz/linux-unix/privilege-escalation\#capabilities](https://book.hacktricks.xyz/linux-unix/privilege-escalation#capabilities)

### Dbus

Read [https://book.hacktricks.xyz/linux-unix/privilege-escalation\#d-bus](https://book.hacktricks.xyz/linux-unix/privilege-escalation#d-bus)

### Exploit groups

* [pkexec](https://book.hacktricks.xyz/linux-unix/privilege-escalation/interesting-groups-linux-pe#pe-method-1)
* [disk group](https://book.hacktricks.xyz/linux-unix/privilege-escalation/interesting-groups-linux-pe#disk-group) \(Also in Hack the Box's [Falalel](https://www.youtube.com/watch?v=CUbWpteTfio)\)
* [video group](https://book.hacktricks.xyz/linux-unix/privilege-escalation/interesting-groups-linux-pe#video-group)
* I won't detail here vulns linked to docker or lxc groups, i'll write a page about them if i use them one day.

{% hint style="info" %}
Ajouter la partie de hack the box, ou un groupe à plus de droits que nous donc on commence par l'obtenir.
{% endhint %}

### Lib

* [https://book.hacktricks.xyz/linux-unix/privilege-escalation\#ld\_preload](https://book.hacktricks.xyz/linux-unix/privilege-escalation#ld_preload) [https://book.hacktricks.xyz/linux-unix/privilege-escalation\#suid-binary-so-injection](https://book.hacktricks.xyz/linux-unix/privilege-escalation#suid-binary-so-injection) 
* [https://book.hacktricks.xyz/linux-unix/privilege-escalation\#etc-ld-so-conf-d](https://book.hacktricks.xyz/linux-unix/privilege-escalation#etc-ld-so-conf-d)

### Writable files

* Linpeas :

```bash
find / '(' -type f -or -type d ')' '(' '(' -user $USER ')' -or '(' -perm -o=w ')' ')' 2>/dev/null | grep -v '/proc/' | grep -v $HOME | sort | uniq #Find files owned by the user or writable by anybody
for g in `groups`; do find \( -type f -or -type d \) -group $g -perm -g=w 2>/dev/null | grep -v '/proc/' | grep -v $HOME; done #Find files writable by any group of the user
```

* Read [python library hijacking](https://book.hacktricks.xyz/linux-unix/privilege-escalation#python-library-hijacking)
* Read [logrotate exploitation](https://book.hacktricks.xyz/linux-unix/privilege-escalation#logrotate-exploitation)

## Sources

* [Hack The Box](https://www.hackthebox.eu/)'s Exercices
* [Ippsec](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA)'s videos
* [Hacktricks](https://book.hacktricks.xyz/)

