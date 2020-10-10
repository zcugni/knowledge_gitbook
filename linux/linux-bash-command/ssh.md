# SSH

## Introduction

* An OpenSSH client lets you connect to remote machine securely via the SSH protocol
* The cmd is called `ssh [user]@<hostname | ip> [-p <port>] [-i private_key_file]`

## Authentication

* 5 different methods can be used for authentication :
  * GSSAPI-based
  * Host-based
  * Public key
  * Challenge-response
  * Password
* "Public key" is the most widespread, so i'm gonna detail it

### Public key

* Based on asymmetric cryptography
  * Each client has a public-private pair of keys
  * The public one is given to others and used for encryption
  * The private one must be kept secret and is used for decryption
* To generate a key pair, use :`ssh-keygen [-t rsa]`
  * `-t rsa` is the default
  * Created inside `~/.ssh/`
  * The private key is named `id_rsa` and the public `id_rsa.pub` \(if the RSA algorithm was used, but it's default\)
  * The passphrase lets you protect the private key even more, because even stolen it won't be usable if you don't know it
* To connect, your public key must be in the `.ssh/authorized_keys` file on the remote machine
  * When a user connects, the program tells the server which pair of keys it want to use
  * It then proves that it has access to the private key
  * The server then verify that the corresponding public key is authorized
* To add your public key into the `~/.ssh/authorized_keys` file of the server, use : `ssh-copy-id <user>@<ip>`

### Known hosts

* A trace of every host you connected to is kept inside `~/.ssh/knownn_hosts` and `/etc/ssh/ssh_known_hosts`
* If an host identification change, a warning is shown and password authentication disabled to avoid spoofing

## Port Forward

* To redirect traffic arriving at a local port to a remote port,  use :  `ssh -L <local_port>:<remote_ip>:<remote_port> <user>@<ip>`
* To redirect traffic arriving at a remote port to a local one, use : `ssh -R <local_port>:<remote_ip>:<remote_port> <user>@<ip>`
* Sockets can also be used
* `-N` Should be used in conjunction with these commands, it prevents from executing a remote command
  * I'm not sure if it's useful in both case or not

## Config options

* There's 2 different files : `ssh_config` \(client config\) & `sshd_config` \(daemon config\)
  * The client lets you connect to other machine
  * While the daemon listen for connection on your machine
* There's 2 locations :  user specs in `~/.ssh/` and system in `/etc/ssh/`
  * Read in this order : `command line -> user spec -> system spec`
  * The first occurrence is used
* Don't forget to reload services after changes :
  * `systemctl reload sshd`
  * `service ssh restart`

{% hint style="info" %}
There's a lot of options so i won't detail them all. I should read it completely one day \(`man ssh_config` & `man sshd_config`\),  in the mean time i'll detail the few interesting options for security.
{% endhint %}

### ssh config

* `ForwardAgent`  See note

### sshd config

* `PermitEmptyPasswords` ****When password authentication is allowed, it specifies whether the server allows login with an empty password. The default is no.
* `PermitRootLogin`Specifies whether root can log in, default is no. Possible values:
  * `yes`
  * `prohibit-password`Only with keys
  * `forced-commands-only` Only with keys and if a cmd has been specified
  * `no`
* `AuthorizedKeysFile` ****Specifies the files that are checked for authorized keys
* `AllowAgentForwarding` ****See note

### Both

* `PasswordAuthentication` ****Specifies whether password authentication is allowed. The default is no.
* `PubkeyAuthentication` ****Specifies whether public key authentication is allowed. The default is yes.

**Note** 

Agent Forwarding is risky but a bit complex. I'll definitely check it one day, but not now. Read this : [https://book.hacktricks.xyz/linux-unix/privilege-escalation/ssh-forward-agent-exploitation](https://book.hacktricks.xyz/linux-unix/privilege-escalation/ssh-forward-agent-exploitation)

## Errors

If you get an :

> Unable to negotiate with ... port ...: no matching key exchange method found. Their offer: gss-group1-sha1-toWM5Slw5Ew8Mqkay+al2g==,diffie-hellman-group-exchange-sha1,diffie-hellman-group1-sha1

You can specify the algorithm with `-okexAlgorithms=+diffie-hellman-group1-sha1` 

## Sources

* [IppSec](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA)
* DigitalOcean's [How-Tos](https://www.digitalocean.com/docs/droplets/how-to/add-ssh-keys/)
* Man pages
* Misc research

