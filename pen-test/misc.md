# Misc

## Tips & Tricks

* Keep in mind that even if you can't access an directory of a website \(ip/dev/ for example\), you might be able to access it's subdir/files \(ip/dev/backup for example\)
* when it ends with `==` it’s usually base64
* Ressource : [https://repo.zenk-security.com/](https://repo.zenk-security.com/)
* MySQL's LIKE comparaison isn't case sensitive
* MySQL's = comparaison isn't case sensitive & doesn't care about trailing spaces
* To send yourself a ping back, you can do this on the victim `ping -c 1 ip` and launch `tcpdump -i tun0 -n icmp` on your machine
* To send a command through cat, it's easier to prepare it with echo before `echo "ping -c 1 my_ip" | nc their_ip their_port` 
* A weird way to write a file is to `cat > fine_name << EoF`, then write your text, and write EoF at the end.

### Particularité firefox

* Si le client reçoit des données depuis une vue json, en l'accédant via firefox, il va tenté de l'interprêter et la formatter, et son parseur eut buggé sur un payload alors qu'il fonctionne. **Utiliser burp pour analyser les réponses**
* Si on dl un ficher dans le navigateur, firefox mettra comme date de dernière modification la date du jour, tandis que si on le dl avec wget, on récup la vraie

## Stuff to check

* php//filter/convert.64 ...
* Mime type details
* Read relevant RFC
* IMAP/SMTP \(owasp\)
* OS Command injection \(owasp\)
* HTTP smuggling and splitting \(owasp\)
* mutation xss ?
* Session Puzzling / Session variable overloading
* LDAP / ORM injection
* SSL part of owasp
* SVG, HTML 5 & CSS attacks
* Websocket part of owasp
* Mobile hack \([https://www.hacker101.com/videos](https://www.hacker101.com/videos)\)
* Mass assignement vulnerability \(the thing in pentester lab with user\[username\]\)

## Fun facts

[https://www.youtube.com/watch?v=0uejy9aCNbI&list=PLhixgUqwRTjx2BmNF5-GddyqZcizwLLGP&index=8](https://www.youtube.com/watch?v=0uejy9aCNbI&list=PLhixgUqwRTjx2BmNF5-GddyqZcizwLLGP&index=8)

