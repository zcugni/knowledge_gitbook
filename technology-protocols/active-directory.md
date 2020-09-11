# Active Directory

## Generalities

* Directory Service used on Windows, lots of functionalities :  
  * Store/organize a network resources \(computers, users, printers, services, etc\)
  * Authenticate user on a network
  * Enforce policies
  * Manage updates
  * etc..
* Use [Kerberos ](https://zcugni.gitbook.io/notes/kerberos)for authentication
* Use [LDAP ](https://zcugni.gitbook.io/notes/languages/ldap)as it's access protocol
* Needs a DNS to work
  * SVR records are used to locate domain controllers
* Abbreviated AD

## Objects

* The base entity, represent a resource

### Types

These are some of the default types :

* **Organizational Uni**t \(OU\) : A container object 
* **Users** 
* **Computers**
* **Groups**
  * 2 sorts :
    * Security : Used for permissions
    * Distribution : Used for email 
  * 3 scopes :
    * Universal : Any object 
    * Global : Can contain objects from the domain and be used in any tree or forest 
    * Domain Local : Can contain objects from any domain but can only be applied to the domain it was created in
* **Contacts** : used for email 
* **Shared Folder**
* **Shared Printer**
* **Connection** : Specifies which domain controllers replicate with each other, how often and their naming contexts
* **Subnets**
* **Site :** A collection of subnet
* **Site link** : Configure which sites are connected to each other 
* **Site link Bridge :** Represent the connection in itself \(between sites\)

### **Attributes**

* Some general attributes :
  * **Relative Distinguished Name** \(RDN\) : A part of the DN
  * **Distinguished Name** \(DN\) – Full path to the object, made of :
    * CN – common name \(lowest in the hierarchy\)
    * OU – organizational unit
    * DC – domain component \(highest in the hierarchy\) 
* User related attributes :
  * **userPrincipalName** \(UPN\) : Login name \(in the `user@domain` format\)
    * **sAmAccountName** : Old format for logon 
  * **objectGUID** : Unique ID
  * **objectSID** : Security identifier \(SID\),  determine authorization for a user or a group to access resources
    * **sIDHistory** : Contains previous SIDs of the user, only needed if the user was moved to another domain

## Structure

* Hierarchical
* A group of objects is a _domain_
  * Authentication and policies are done and set at this level
  * A DNS name identifies each domain
* A _tree_ is a group of domains that shares the same namespace
* A _forest_ is a group of trees that shares a schema & a configuration container
  * The first domain deployed in a forest is the _root domain, it doesn't change_
  * It contains the Enterprise Admins and Schema Admins groups

## AD services

### Domain Service

* Abbreviated AD DS
* The main role, that deals with the principle functionalities
* Additional server with this role are named **domain controller**
* It's main functions are \(represented as roles\) :
  * **Schema Master** : A forest wide role that handles all the changes to the schema 
  * **Domain Naming Master** : A forest wide role that handles domain names \(add & remove operation for example\)
  * **PDC Emulator** : Handles password changes, user lockouts, group policy and is the time server for the clients
  * **RID Master** : When objects are created, they're assigned a unique SID and a relative id \(RID\). This role ensures that there's no duplicate SID, and no duplicate RID within a same domain 
  * **Infrastructure master** : A domain wide role used to reference objects in other domains
  * They're all installed on the first domain controller of a new forest, but you can then move them
* When you install this service, it'll automatically create the DNS SRV records in those folders :
  * `Forward Lookup Zones/<domain_name>/_msdcs/dc/_sites/Default-First-Site-Name/_tcp`
  * `Forward Lookup Zones/<domain_name>/_msdcs/dc/_tcp`
* Replication of domain controller : 
  * The **Knowledge Consistency Checker** \(KCC\) is a process that runs on all dc and generates a replication topology based off the sites, subnets and site link objects
  * Intra-Site Replication : Replication between dc within a same site 
  * Inter-Site Replication : Replication between dc within separates site 

### Others

* AD Web Services \(ADWS\)
  * Automatically installed with ADDS or ADLDS role and configured to run automatically
  * Provides remote management of any local directory services
* AD Certificate Services \(AD CS\)
  * A  server role that allows you to build a public key infrastructure \(PKI\) and provide digital certificates
* AD Federation Services \(AD FS\)
  * Lets you sign in to other services \(like office\) through AD
* AD Lightweight Directory Services \(AD LDS\)
  * Provides directory services using the LDAP protocol without the need to deploy domain controllers. 
* AD Rights Management Services \(AD RMS\)
  * Protects documents by defining who can open, modify, print, forward, etc documents 

## Other terms

### Schema

* Defines every object class \(and their attributes\) that can be used in a forest

### Global Catalog \(GC\) server

* Contains a full replica of all objects
* Used to perform forest wide searches
* By default the first domain controller in a domain is designated as the GC server  

### Jet Database Engine

* The database is based on the Jet Blue engine
* It utilizes the Extensible Storage Engine \(ESE\)
* The database is a single file named `ntds.dit`
* By default it's stored in the `%SYSTEMROOT%\NTDS` folder and each domain controller 

### Read-Only Domain Controller \(RODC\) server

* Holds a read-only copy of the database
* Used for branch office location or location with poor security

### SYSVOL

* A shared folder on each DC
* The default location is `%SYSTEMROOT%\SYSVOL\sysvol`
* Contains :
  * Group Policy Objects
  * Folders
  * Scripts
  * Junction Points

## Sources

* [docs.microsoft.com](https://docs.microsoft.com/en-us/archive/blogs/ashwinexchange/understanding-active-directory-for-beginners-part-1)
* [activedirectorypro.com](https://activedirectorypro.com/glossary/)



