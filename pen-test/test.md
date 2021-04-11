# Methodology / Recon

* To avoid IP bans, use a VPS \(virtual private server\) that allows security testing from its server
* * * Start broad to get an idea of attack vectors and then go deeper on specific areas

## Social Info

* Use the `whois` database/cmd to gain info on the owner of the domain
* I don't really do that but it could be useful :
  * Gain info on the business and it's employees through the crunchbase website, social medias, etc
  * Identify email pattern

## IP Space and domains

* Define the range of IP and domains in scope
* To enumerate sub domain, brute-force with plausible list \(see Tools\)

## Nmap

The next 3 steps can be done through [`nmap`](https://zcugni.gitbook.io/notes/tools/linux-bash-command/net-tools-commands/nmap) :

* **Host discovery** - Identifying the live hosts within an IP range
  * You do that through a _ping sweep_ \(aka ping all the addresses\)
  * The alternative to nmap is `fping`
* **Fingerprint the OS** - Identify which OS is running
  * You can also do it offline with a capture of the traffic with [https://lcamtuf.coredump.cx/p0f3/](https://lcamtuf.coredump.cx/p0f3/)
* **Port Scanning** - Identify open ports and what's listening on them
  * An alternative to nmap is `masscan` \(more efficient but less precise\)
  * Start with a fast scan \(only the principal ports\) and after that run a full one \(all the ports\) so you can start working on the first result while the complete one proceeds

## For each port/tech

* From the results of the port scan, for each specific tech, do the following : 
  * Get the version
    * Search "enumarate &lt;tech&gt;" or "scan &lt;tech&gt;" on google to see if there's some tools already
    * If it's open source, check the github 
  * Test searchsploit, metasploit & google "exploit tech"

## Web

* If there's a website, do the following things
* Don't necessarily do them in this order or entirely, if you see a promising lead, go for it
  * However at least launch gobuster before searching for something else since it take some time

### File discovery

* This is linked to the _Forced Browsing_ chapter __of __[Broken Access Control](https://zcugni.gitbook.io/notes/pen-test/broken-access-control-and-file-inclusion)
* Check `robots.txt` & `sitemap.xml`
* Try to identify the server-side language by trying to access `index.html`, `index.php`, etc
* Run [gobuster](https://zcugni.gitbook.io/notes/tools/hack-tools#forced-browsing) 
  * First with extensions \(at least `txt`\) and if possible with the server-side language one
  * Then recursively on interesting directories
  * If you really don't find anything interesting try \(also recursively\) :
    * With `-f` for the `/` 
    * Other extensions :
      * Compressed files : `.zip`, `.tar`, `.gz`, `.tgz`, `.rar` 
      * Document : `.pdf`, `.doc`, `.rtf`, `.xls`, `.ppt` 
      * Backup files : `.bak`, `.old`
  * **This should run while you research other stuff**
  * You might want to create a custom list of file names based on the name logic of the application
    * For example, if there's an ****_edituser.asp_, search for an _adduser.asp_
* Some files which should be executed by the server \(and not shown\), might be visible if they're renamed \(for example _login.asp =&gt; login.asp.old\)_
* Files might be wrongly indexed by browsers if the `robots.txt` file or html meta tags aren't used correctly
  * **Dorking** means using special search operators on browser \(like `:site` or `:cache`\) to find sensible data
    * For google, it's called _Google Hacking_
    * It can also be done in github
* Check web archives for sensible data that aren't available anymore but were at a time

### Fingerprint

* This is the process of identifying what is running/used
  * For example what web-server/server-side language/framework, etc is used 
* To do that, we compare response, errors, etc to db of signatures
* Generally you can look at :
  * Response headers
  * Order of headers
  * Error messages
  * Concatenation syntax
  * html meta tag
  * File/directories name
  * Cookie's name
  * Comments
  * etc
* There's often tool available to do that
* For a web app, fingerprint :
  * The web server with `httprint`
  * The server-side language
  * The framework
  * The BDD

### Map

* Take note of different aspect of the application to detect vulnerable part and get an idea of the flow of data

#### Get & POST request

* Detail the request : 
  * Parameters \(and hidden fields for POST\)
  * Authenticated or not
  * Through SSL \(https\) or not
  * Custom headers added
* And their response :
  * Cookies set or modified
  * Any redirects \(3xx\) or 400, 403, & 500 error codes
  * Headers being passed

#### Cookie

* Place of creation, use & modification
  * Try to go to these pages without them or with different values
* `Secure` or `HttpOnly` flags set
  * For HTTP/1.1 & HTTP 1.0 `Cache-Control`option
* Persistency & expiration time
* Encoding & character set
* Constant value or variable \(might be both, some part constant and some part variable\)
  * If it changes, try to find the cause \(time, ip address, account, etc\)
* Predictability \(deducing the next one from the previous one\)
* Possibility to brute-force/decrypt

### Misc

* Check the source code for :
  * Automatically generated content \(by framework, etc\). See above
  * Interesting comments
  * The code in itself
* Check how links are composed, if it's wrongly configured it can be a bit weird \(for example : [http://index.php/rest\_of\_link](http://index.php/rest_of_link)\)

### Vulnerable input

* For every input :
  * Check reaction to `'` & `"` \(depending on reaction, check basic sqli injection\)
  * Check basic XSS \(script & img, note what type of protection is in place\)
* For every url :
  * Check if anything is reflected on the page \(and if `'` & `"` break things\)
  * Try accessing page out of orders \(for example directly an /edit/\)
  * Changing GET request for POST and vice-versa

## Network Architecture

* Try to understand the network architecture through :
  * Port scan \(to see what's filtered\)
  * Network packet test for firewalls
  * Analysis of web server banner for reverse proxy
  * Checking if answers are what we expect \(might not be if a reverse proxy/firewall is present\)
  * Testing the response time to see if the request are cached by a reverse proxy
  * Network load balancers are detected by comparing multiple request’s response to see if they go to different servers
    * The date may change or the load balancer may add cookies/headers

## Source

* [Owasp testing guide v4](https://owasp.org/www-project-web-security-testing-guide/assets/archive/OWASP_Testing_Guide_v4.pdf)

