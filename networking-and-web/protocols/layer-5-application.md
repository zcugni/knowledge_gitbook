# Misc

## Logical Ports

* TCP & UDP use _**logical ports**_ ****to multiplex connections between machines, permitting one host to serve different services to multiple hosts
* A service **binds** itself to a port
* A port is a number between 0 & 65'535
* TCP & UDP ports aren't the same \(but the range is\)
* Each internet service has a standard port defined by the _Internet Assigned Numbers Authority_ \(IANA\)
  * Ports between 49'152 & 65'53 aren't attributed to anything
* A connection is defined by the IP address + port of the source and the IP address + port of the destination
  * In IPv4 it's written `ip:port`
  * In IPv6 `[ip]:port`

### Services File

* `/etc/services/` On Unix
* `C:\\Windows\System32\drivers\etc\services` On windows
* List services commonly used on the machine and ports normally used for them
  * Some programs use this file to see on what port they should bind on
  * `tcpdump` Use it to look up which services are running and on what
* 5 fields by line :
  * Name of the protocol
  * Port number
  * Transport protocol \(separated from the port number by a /\)
  * Other name of the service \(optional\)
  * `# Comments`
* You're not obligated to follow it and not doing it might be a bypass for badly configured firewalls
* However some programs/host won't accept certain port and you might get strange result by using non standard port

## Sockets

* Communication endpoint for processes
  * Abstraction that takes care of the detail of the transport layer
    * **Raw sockets** let you deal with it yourself, but they demand root access and are inconsistent across systems
  * They're kind of like pipes, but more complex
  * Processes \(for example a web server\)  open a socket on the port they listen to
    * One process can open any number of sockets as long as the OS doesn't object
    * An _open_ or _listening_ socket is waiting for a connection
    * It can accept any numbers of connections so long as all the clients have unique source IP addresses & ports
* 4 types, 2 most common :
  * **Stream** socket for TCP/IP
  * **Datagram** socket for UDP
* Sockets are identified via socket descriptors \(which are actually **file descriptor**\)
* Both Windows & Linux have **local sockets** : system entities on the filesystem or memory that accept connections from other programs
* One of the socket protocol is _Inter-Process Communication_ \(IPC\) which is contained entirely in memory

## Root User

* Most Unix systems only allows the root account to open TCP & UDP ports below 1024
  * Because those are normally assigned to the most popular & important Internet services
* Unprivileged users can run servers on higher port numbers
* If an intruder takes control of a software listening on a port, it gains the privilege of the user running the process -&gt; **Don't run processes listening to port as root**
* There's a trick to start as root and drop the privilege afterwards, etc, pto prevent that
* Windows doesn't reserved port for admin \(but there's a registry setting to enable that\) so you should never run a process as admin

## Cookies

* Set by the server via the `Set-Cookie` header in the response :

  `Set-Cookie: name=value; expires=Mon, 23-Mar-2020 20:35:24 GMT; Max-Age=7200; path=/; secure`

* Available attributes :
  * `Secure` Will only be transmitted with https request
  * `HttpOnly` Can't be retrieved in js \(not supported by every browser\)
  * `Domain` Define the domain for which the cookies will be sent
    * By default, it's the host name of the server generating the cookie
    * Transmitted for the domain and it's sub-domains
  * `Path` Define the precise path for which the cookies is sent
  * `expires` By default, cookies are only valid for the current session of the browser and are deleted when it closes \(i think\)
    * With this flag, you can define an expiration date

## Session

* Session keeps the state & information of the client server-side
* This allows you to avoids transmitting all data through cookies
* When you login \(for example\), your're only given a session id cookie, which is subsequently used to retrieve your info \(instead of having a cookie for each info\)
* Session id can also be sent via GET parameters

## Local Storage

* Also called **Web Storage** or **Offline Storage**
* Let you store data as key-value pair on the client
  * Linked to a particular domain, it follows SOP
* The storage capacity is bigger than that of cookies, and data aren't sent to the server
* The `localStorage` is persistent, while the `sessionStorage` is emptied when you close a window
  * Both are accessible in js via `setItem` & `getItem`

## URL

### Terminology

* **Uniform Resource Identifier** _\(URI\)_ Identifies a resource \(like a name\)
* **Uniform Resource Locator** _\(URL\)_ Locate a resource \(like an address\)
* **Uniform Resource Name** _\(URN\)_ Identifies a resource uniquely' \(like the ISBN of a book\)
* URL & URN are URIs, but the opposite isn't always true

### Anatomy

![](../../.gitbook/assets/url_anatomy.png)

* If the scheme is `file://` it reference a local resource
* There can be multiple parameters in the **Query String**, separated by `&`
* You can specify the username & password before the domain :`https://username:password@example.com`
* The **Fragment Identifier** is a page anchor

### Encoding

* Some chars have special meaning and needs to be encoded to be use normally 

| Char | Encoding | Signification |
| :--- | :--- | :--- |
|  | `%20` or `+` | Space |
| `!` | `%21` |  |
| `#` | `%23` | Delimits the fragment identifier |
| `$` | `%24` |  |
| `%` | `%25` | Encode string |
| `&` | `%26` | Delimits query string's parameters |
| `'` | `%27` |  |
| `(` | `%28` |  |
| `)` | `%29` |  |
| `*` | `%2A` |  |
| `+` | `%2B` | Use to encode a space |
| `,` | `%2C` | Alternative to `;` |
| `/` | `%2F` | Delimits path segment |
| `:` | `%3A` | Delimits the scheme from the host and the host from the port |
| `;` | `%3B` |  |
| `=` | `%3D` | Delimits key-value pairs in the query string |
| `?` | `%3F` | Delimits the start of the query string |
| `@` | `%40` | Delimits user info from the host |
| `[` | `%5B` |  |
| `]` | `%5D` |  |

## DOM

From Real-World Bug Hunting: A Field Guide to Web Hacking

> The DOM  is an API for HTML & XML documents that allows developers to modify the structure, style, and content of a web page via Javascript

## Sources

* Url encoding : [W3C](https://www.w3schools.com/tags/ref_urlencode.ASP)
* _Networking for Systems Administrators_ by Michael W. Lucas
* _Hacking : The Art of Exploitation_ by John Erickson
* Pentesterlab
* Misc Research

