# SSH

## Introduction

* An OpenSSH client lets you connect to remote machine securely via the SSH protocol
* The cmd is called `ssh`

## Port Forward

* To redirect traffic arriving at the local port to the remote port,  use :  `ssh -L <local_port>:<remote_ip>:<remote_port> <user>@<ip>`
* To redirect traffic arriving at a remote port to a local one, use : `ssh -R <local_port>:<remote_ip>:<remote_port> <user>@<ip>`
* Sockets can also be used
* `-N` Should be used in conjunction with these commands, it prevents from executing a remote command.
  * I'm not sure if it's useful in both case or not

## Authentication

* 5 different methods can be used for authentication :
  * GSSAPI-based
  * Host-based
  * Public key
  * Challenge-response
  * Password
* "Public key" is the most widespread, so i'm gonna detail it.

### Public key

* Based on asymmetric cryptography
  * Each client has a public-private pair of keys.
  * The public one is given to others and used for encryption
  * The private one must be kept secret and is used for decryption
* To connect, your public key must be in the `.ssh/authorized_keys` file on the remote machine.
  * When a user connects, the program tells the server which pair of keys it want to use
  * It then proves that it has access to the private key
  * The server then verify that the corresponding public key is authorized

#### Generate key pair

* `ssh-keygen [-t rsa]`
  * \(`-t rsa` is default\)
  * Created inside `~/.ssh/`
  * The private key is `id_rsa` and the public `id_rsa.pub` 
    * If the RSA algorithm was used, but it's default
  * The passphrase lets you protect the private key even more, because even stolen it won't be usable if you don't know it.
* Add your public key into the `~/.ssh/authorized_keys` file of the server
  * You can use this command :`ssh-copy-id <user>@<ip>`

### Known hosts

* A trace of every host you connected to is kept inside `~/.ssh/knownn_hosts` and `/etc/ssh/ssh_known_hosts`
* If an host identification change, a warning is shown and password authentication disable to avoid spoofing.

## Config options

* There's 2 different files : `ssh_config` \(client config\) & `sshd_config` \(daemon config\)
  * The client lets you connect to other machine
  * While the daemon listen for connection on your machine
* There's 2 locations :  user specs in `~/.ssh/` and system in `/etc/ssh/`
  * Read in this order : `command line -> user spec -> system spec`
  * The first occurrence is used

### Client config

| Option | Description | Default |
| :--- | :--- | :--- |
| AddressFamily | `any`, `inet` \(ipv4 only\), `inet6` \(ipv6 only\) | `any` |
| BindInterface |  |  |
| ChallengeResponseAuthentication |  |  |
| CheckHostIp |  |  |
| Ciphers |  |  |
| Compression |  |  |
| ForwardAgent |  |  |
| GatewayPorts |  |  |
| HashKnowHosts |  |  |
| HostbasedAuthentication |  |  |
| LocalForward |  |  |
| LogLevel |  |  |
|  |  |  |

### Server config

* Don't forget to reload services after changes
  * `systemctl reload sshd`
  * `service ssh restart`

## Errors

Si je reçois un :

> Unable to negotiate with ... port ...: no matching key exchange method found. Their offer: gss-group1-sha1-toWM5Slw5Ew8Mqkay+al2g==,diffie-hellman-group-exchange-sha1,diffie-hellman-group1-sha1

Je peux spécifier l'algorithme à utiliser avec `-okexAlgorithms=+diffie-hellman-group1-sha1` 

## Sources

* [IppSec](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA)
* DigitalOcean's [How-Tos](https://www.digitalocean.com/docs/droplets/how-to/add-ssh-keys/)
* Misc research

