# SSH

## Enable/disable password auth

* Change `PasswordAuthentication` inside `/etc/ssh/sshd_config` \(Caution, it's sshd\_config, not ssh\_config\)
* Then reload services :
  * `systemctl reload sshd`
  * `service ssh restart`

## Generate key pair

* Générer une paire public-privé \(on the client\) : `ssh-keygen -t rsa` \(`-t rsa` est optionnel en vrai, c'est le setting par défaut\)
  * C'est bien de les laisser dans le dossier par défaut `/home/USER/.ssh/`
  * La clé privée se nomme `id_rsa` et la publique `id_rsa.pub`
  * Le passphrase permet de protéger la clé privé plus fortement, même obtenue elle ne sera pas utilisable. Mais il faut l'écrire à chaque co du coup
* Il faut ensuite ajouté sa publique key dans le fichier `/home/USER/.ssh/authorized_keys` du serveur
  * La méthode la plus simple pour le faire est d'utiliser cette commande `ssh-copy-id user@ip`

## Port Forward

* We can forward port with ssh like this `ssh -L my_port:127.0.0.1:their_port user@ip -N`
  * Ça va rediriger le traffic sur mon port sur le port équivalent de la machine et vice-versa
  * Le `-N` est apparemment important mais j'ai pas trop compris pourquoi

## Errors

Si je reçois un :

> Unable to negotiate with ... port ...: no matching key exchange method found. Their offer: gss-group1-sha1-toWM5Slw5Ew8Mqkay+al2g==,diffie-hellman-group-exchange-sha1,diffie-hellman-group1-sha1

Je peux spécifier l'algorithme à utiliser avec `-okexAlgorithms=+diffie-hellman-group1-sha1` 

