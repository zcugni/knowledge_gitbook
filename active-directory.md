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
* Abbreviated AD

## Terminology

### Objects

* The base entity
* Define by a group of attributes that describe a resource
* Identified by an ID
* Can be a leaf or a container
* Default types :
  * **Organizational Unit** \(OU\) : A container object 
  * **Users** 
  * **Computer**
  * **Groups**
    * Security group : used to grant or deny access to resources
    * Distribution groups : used for email 
  * **Contacts** : used for email 
  * **Shared Folder**
  * **Shared Printer**

### **Domain**

* A hierarchical group of objects
* Authentication and policies are done and set at this level
* A DNS name identifies each domain

### Tree

* A hierarchical group of domains that shares the same namespace

### Forest

* A group of trees
* Shares a schema & a configuration container

### FSMO

* Functions of a domain controller \(represented as roles\)
* They're all installed on the first domain controller of a new forest, but you can then move them
* Available roles :
  * **Schema Master** : A forest wide role that handles all the changes to the Active Directory schema 
  * **Domain Naming Master** : A forest wide role that handles domain names \(add & remove operation for example\)
  * **PDC Emulator** : Handles password changes, user lockouts, group policy and is the time server for the clients
  * **RID Master** : When objects are created, they're assigned a unique SID and a relative id \(RID\). This role ensures that they're no duplicate SID, and no duplicate RID within a same domain 
  * **Infrastructure master** : A domain wide role used to reference objects in other domains

### Global Catalog \(GC\) server

* Contains a full replica of all objects
* Used to perform forest wide searches
* By default the first domain controller in a domain is designated as the GC server  

### Jet Database Engine

* The Active Directory database is based on Microsoft’s Jet Blue engine
* It utilizes the Extensible Storage Engine \(ESE\)
* The database is a single file named `ntds.dit`
* By default it's stored in the `%SYSTEMROOT%\NTDS` folder and each domain controller 

### Read-Only Domain Controller \(RODC\) server

* Holds a read-only copy of the AD database
* Use for branch office location or location with poor security

### Schema

* Defines every object class \(and their attributes\) that can be used in a forest

### SYSVOL

* A shared folder on each DC
* The default location is `%SYSTEMROOT%\SYSVOL\sysvol`
* Contains :
  * Group Policy Objects
  * Folders
  * Scripts
  * Junction Points

### Object Name Attributes

* **userPrincipalName** \(UPN\) : Login name \(in the `user@domain` format\)
* **objectGUID** : Uniquely identify a user account
* **sAmAccountName** : Old format for logon 
* **objectSID** : Security identifier \(SID\) of the user,  used by the server to identify a user and their group membership to authorize access to resources 
* **sIDHistory** : Contains previous SIDs of the user, only needed if the user was moved to another domain
* **Relative Distinguished Name** \(RDN\) : A part of the DN
* **Distinguished Name** \(DN\) – Full path to the object, made of :
  * CN – common name \(lowest in the hierarchy\)
  * OU – organizational unit
  * DC – domain component \(highest in the hierarchy\) 

### Groups

* Two types :
  * **Distribution** : Used for email 
  * **Security** : Used for permissions
* There's 3 scopes for groups :
  * **Universal** : Any object 
  * **Global** : Can contain objects from the domain and be used in any tree or forest 
  * **Domain Local** : Can contain objects from any domain but can only be applied to the domain it was created in

## AD services

#### Active Directory Web Services \(ADWS\)

This service was introduced in Windows Server 2008 R2. It is automatically installed with ADDS or ADLDS role and is configured to run automatically. This service provides remote management of any local directory services.

## Others

* **AD DS** : The server that runs the Active Directory Domain Service role
* **Domain controller** : another server that runs the Active Directory Domain Service role
  * It's advised to run multiples for redundancy
* **Active Directory Web Services** \(ADWS\) : Provides remote management of local directory services \(installed by default\)
* **Domain** : A hierarchical structure of objects
  * Provides authentication to access it's resources
  *  * A hierarchical structure for users, groups, computers and other objects
    * Security services that provide authentication and authorization to resources in the domain and other domains
    * Policies that are applied to users and computers
    * A DNS name to identify the domain.

## Sources

* [docs.microsoft.com](https://docs.microsoft.com/en-us/archive/blogs/ashwinexchange/understanding-active-directory-for-beginners-part-1)
* [activedirectorypro.com](https://activedirectorypro.com/glossary/)



* to check : [https://www.windows-active-directory.com/](https://www.windows-active-directory.com/)



