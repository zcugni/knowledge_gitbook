# Test

## Misc knowleadge

* Usually, to fingerprint we compare to a db of signature \(of headers, errors, etc\)

## Example

* Start broad to get an idea of attack vectors and then go deeper on specific areas
* An example of the process : 
  * Do a rapid nmap \(only the principle ports\)
  * If there's a web techno, do the first 3 steps
  * Run a second nmap when the first one is finished \(with all the port this time\)
  * Follow the instructions regarding each techno

## Social Info

* Use the whois database/cmd to gain info on the owner of the domain
* I don't really do that but it could be useful :
  * Gain info on the business and it's employees through the crunchbase website, social medias, etc
  * Identify email pattern

## IP Space and domains

* Define the range of IP and domains in scope
* To enumerate sub domain, brute-force with plausible list \(see Tools\)

## Nmap

The next 3 steps can be done through `nmap` :

* **Host discovery** - Identifying the live hosts within an IP range
  * You do that through a _ping sweep_ \(aka ping all the addresses\)
  * The alternative to `nmap` is `fping`
* **Fingerprint the OS** - Identify which OS is running
  * You can also do it offline with a capture of the traffic with [https://lcamtuf.coredump.cx/p0f3/](https://lcamtuf.coredump.cx/p0f3/)
* **Port Scanning** - Identify open ports and what's listening on them
  * An alternative to `nmap` is `masscan` \(more efficient but less precise\)
  * Start with a fast scan \(only the principal ports\) and after that run a full one \(all the ports\) so you can start working on the first result while the complete one proceeds.



## Web

* Test `index.html`, `index.php`, etc to define the language used
* Check `robots.txt` & `sitemap.xml`
* Run [gobuster](https://zcugni.gitbook.io/notes/tools/hack-tools#forced-browsing) 
  * First with extensions \(at least `txt`\)
  * Then recursively on interesting directories
  * Eventually with `-f` for the `/` \(also recursively\)
  * This should be done while you research other stuff
* Check the source code for :
  * Automatically generated content \(by framework, etc\)
  * Interesting comments
  * The code in itself
* Check how links are composed, if it's wrongly configured it can be a bit weird \(for example : [http://index.php/rest\_of\_link](http://index.php/rest_of_link)\)
* For every input :
  * Check reaction to `'` & `"` \(depending on reaction, check basic sqli injection\)
  * Check basic XSS \(script & img, note what type of protection is in place\)
  * If it's in php and some input are reflected, try to do `<?php phpinfo(); ?>`
* For every url :
  * Check if anything is reflected on the page \(and if `'` & `"` break things\)
  * Try accessing page out of orders \(for example directly an /edit/\)
  * Changing GET request for POST and vice-versa

## For each tech

* Get the version
  * Search "enumarate &lt;tech&gt;" or "scan &lt;tech&gt;" on google to see if there's some tools already
  * If it's open source, check the github 
* Test searchsploit, metasploit & google "exploit tech"

## Fingerprint

* Web server :
  * Check the _Server_ header in the response header
  * If it's obfuscated, check :
    * The order of the headers
    * The response to a malformed request
  * Use `httprint`
* Server-side language :
  * Try `index.html`, `index.php`, etc
  * Test methods of concatenation
  * Check errors & request headers
* Web application framework :
  * Check the response header
  * If obfuscated, check :
    * Cookie's names
    * Certain files/directories
    * Comment
    * Html meta tag
    * etc
* BDD
  * Check the errors
  * The concatenation techniques
  * The comments









## Fingerprint base de données

On peut déterminer le _database management system_ \(DBMS\) par différents moyens :

* Les erreurs 
* Les techniques de concaténation
* Les commentaires

### Erreurs

> You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ‘\’’ at line 1 MySql
>
> ORA-00933: SQL command not properly ended Oracle
>
> Microsoft SQL Native Client error ‘80040e14’ Unclosed quotation mark after the character string MS SQL Server
>
> Query failed: ERROR: syntax error at or near “’” at character 56 in /www/site/test.php on line 121. PostGre SQL

### Technique de concaténation

| DB Name | Contatenation |  |  |
| :--- | :--- | :--- | :--- |
| MySql | `‘test’ + ‘ing’` |  |  |
| SQL Server | `‘test’ ‘ing’` |  |  |
| Oracle | \`‘test’ |  | ’ing’\` |
| PostgreSQL | \`‘test’ |  | ’ing’\` |

### Commentaires

MySQL propose des commentaires qui sont ignorés par les autres DBMS mais pas par lui : `/*! and 1=0 */`. S’il réagit c’est donc que c’est un serveur MySQL.

### MySQl Information Gathering

* Version :
  * `@@version`
  * `VERSION()`
* Database name : `DATABASE()`
* La vue INFORMATION\_SCHEMA nous donne ce genre d’info :

![](../.gitbook/assets/information_schema.png)

## Error Response

On peut récupérer pas mal d'information dans les erreurs ou les réponses du serveur donc c'est intéressant de brute-force des noms de fichier et de tenter des connexions erronées à des db.

* Test network problems leading to the application being unable to access the database server
* Test authentication failure due to missing credentials

## Mapping

Le mapping consiste à énumérer dans un fichier toutes les informations qu’on peut trouver sur certains aspects du site, afin d’en comprendre le fonctionnement et de pouvoir y trouver une faille.

### Application Entry Points

Detail GET & POST request :

* Their parameters \(and hidden fields for POST\)
* If the request is authenticated or not \(i guess sent with cookies ?\)
* If it is done through SSL \(https\)
* Custom headers added

And their response :

* Check if cookies are set or modified
* Any redirects \(3xx\) or 400, 403, & 500 error codes
* Headers being passed

### Data/execution flow

3 approches possibles :

* Tous les chemins
* Suivre le flow des données \(et noter leur changement d’état\)
* Plusieurs instances concurrentes accédant aux même données

### Architecture

Partir d’un postulat simple et le contester au fur et à mesure. C’est lié au proxy, firewall, etc, donc je vais résumer ce qui est conseillé mais je comprends pas tout.

* Network scans on the web server to see if the ports are being filtered
* Network packet test for firewalls
* Analysis of web server banner for reverse proxy
* Checking if answers are what we expect \(might not be if a reverse proxy/firewall is present.. i believe\).
* Testing the response time to see if the request are cached by a reverse proxy
* Network load balancers are detected by comparing multiple request’s response to see if they go to different servers. \(For example the Date may change or the load balancer may add cookies/headers\)

### Cookie

* Quand sont-ils créés, utilisés et modifiés ? Que se passe-il si on accède à des pages sans ces cookies/ avec des valeurs différentes ?
* Lesquels ont la directive `Secure` ? Certains devraient-ils l’avoir mais ne l’ont pas ?
* Ont-ils la directive `HttpOnly` ?
* Sont-ils persistent et quels sont leur temps d'expirations ?
* Pour quels domaines/chemins sont-ils définis ?
* Quel paramètre de `Cache-Control` est utilisé \(pour HTTP/1.1 & HTTP 1.0\) ?
* Sont-ils encodés en Hex, Base64, MD5, etc ? Quel set de caractère est utilisé ?
* Ont-ils plusieurs partie distinctes ? L’une est-elle variable et l’autre changeante ? Changent-elles en fonction du temps, de l’adresse ip, du compte, etc ?
* Peuvent-ils être déduit par des procédés cryptographique ou par un brute-force raisonnable ?
* Peut-on prédire le prochain cookie si on a le procédé et le précédent cookie ?

## File discovery

Trouver tous les fichiers sensibles auxquels on peut avoir accès \(cela est fortement lié au _Forced Browsing_ de [Broken Access Control](https://zcugni.gitbook.io/notes/pen-test/broken-access-control-and-file-inclusion)\).

* Depending of the extension, a file might be either served \(shown\) or executed by the server \(without being shown\). However, by renaming/copying it, developers might involuntarily change this behavior \(`login.asp` =&gt; `login.asp.old`\).
* Guess unreferenced/old files name by looking at the published files name schema for logic. \(Aka, if edituser.asp exist, adduser.asp & viewuser.asp might as well, same for an app/user directory and an app/admin one\).

Interesting extension are :

* .zip, .tar, .gz, .tgz, .rar, ... : compressed files
* .txt, .pdf, .doc, .rtf, .xls, .ppt, ... : Office documents 
* .bak, .old and other extensions indicative of backup files \(for example: ~ for Emacs backup files\)
* etc

### Dorking / Indexed Files / Web Archives

Files might be wrongly indexed by browsers if the robot.txt file or html meta tag are not used correctly. We can find them by using _dorks_. \(I'm not sure of the exact definition but _dorking_ means using search query with special operators in browser, for example `:site` or `:cache`\).

Dorking can also be used in github to search for public sensible data. Some tools automate this but they can only search for users attached to the organization, it's a good idea to search for other employee on linkedin and check their github account.

De la même façon, les archives du web ont des fois des fichiers sensibles.

### Google Hacking

`site:` `intitle:` `inurl:` `filetype:` \(extensions\) AND, OR, \|, & `-` NOT

## General tips

* Look at comment/source code
* Look at `robot.txt` file

## Source

* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)

