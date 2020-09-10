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

## Misc

* 2 naming convention
  * UPN \(user@domain\)
  * The ldap one

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

a finir 

* **objectSID** – This attribute is the security identifier \(SID\) of the user. The SID is used by the server to identify a user and their group membership to authorize users access to domain resources. 
* **sIDHistory** – This attribute contains previous SIDs for the user object. This is only needed if a user has moved to another domain. 
* **Relative Distinguished Name \(RDN\)** – The RDN is the first component of the distinguished name. It is the name of the object in Active Directory relative to its location in the hierarchical structure of AD
* **Distinguished Name \(DN\)** – The DN attribute locates objects in the directory. This attribute is commonly used by services and applications to locate objects in Active Directory. a DN is made up of the following components: 
  * CN – common name
  * OU – organizational unit
  * DC – domain component

There are many classes and attributes unless your programming or troubleshooting some advanced issue it is not necessary to know everything about the schema. 



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



