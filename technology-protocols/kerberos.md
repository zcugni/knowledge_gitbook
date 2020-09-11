# Kerberos

## Generalities

* Kerberos is a widely use authentication protocol
* It follows the Single Sign-On principle \(SSO\) \(with one authentication, all the services are available\)
* Kerberos use a system of tickets and a trusted third party
* It use symmetric key for encryption
* Used by default in Windows

## Entities

* The client
* The _Key Distribution Center_ \(KDC\), divided in 2 servers :
  * The _Authentication Server_ \(AS\)
  * The _Ticket Granting Server_ \(TGS\)
* The server with the protected resources/services

## Tickets & key

* There's 2 types of ticket :
  * The _Ticket Granting Ticket_ \(TGT\), which grant access to the TGS
  * Services tickets, which grant access to services
* There's 3 different secret keys  :
  * The password of the user
    * Use to encrypt communication between the client and the AS
    * Since the AS has access to the user database it can retrieve it based on the user ID
  * A session key created by the AS
    * Used to encrypt communication between the client and the TGS
  * One shared between the TGS and the server \(used for service tickets\)
* Tickets use timestamps and won't be valid if the time seem incorrect

## Process

* The client connects to the AS with a user id and a password \(there's more data in the request that will be encrypted\)
* If the credentials are valid, the AS gives back \(encrypted\)
  * A TGT
  * The session key used as a secret key to encrypt communication with the TGS
* The client use it's TGT to ask the TGS for a service ticket \(the request is encrypted\)
* The TGS gives back a service ticket \(encrypted\)
  * The request is encrypted with the key of the user, but the ticket in itself is encrypted with the key of the server/service, the user don't understand it
  * The TGS doesn't check that the user has the right to use the service, it's the service itself that will check that later. As such, a user can ask for services he doesn't have the right to use.
* The client connect to the service with the ticket

## Sources

* A [medium](https://medium.com/identity-beyond-borders/kerberos-explained-3bc2ddb7b0eb) article
* [devensys.com](https://blog.devensys.com/kerberos-principe-de-fonctionnement/) \(in french\)
* [doubleoctopus.com](https://doubleoctopus.com/security-wiki/authentication/ticket-granting-tickets/)
* [varonis.fr](https://blog.varonis.fr/explication-de-lauthentification-kerberos/) \(in french\)
* [beta.hackndo.com](https://beta.hackndo.com/service-principal-name-spn/) \(in french\)





