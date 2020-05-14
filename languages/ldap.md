---
description: >-
  Disclaimer : Not complete. Also, I've just read the doc and not use it. C'est
  une base pour quand je tomberais dessus, auquel cas je
  compléterais/corrigerais cette entrée
---

# LDAP

## Généralités

* _Lightweight Directory Access Protocol_ \(LDAP\)
* Repose sur TCP/IP
* Protocole permettant l'interrogation et la modification des services d'annuaires
* Evolué en une norme pour les systèmes d'annuaires, incluant :
  * Un modèle de données
  * Un modèle de nommage : définit la façon dont l'information est stockée et organisée
  * Un modèle fonctionnel basé sur le protocole : définit les services fournis par l'annuaire \(recherche, ajout, etc\)
  * Un modèle de sécurité : définit les droits d'accès aux ressources
  * Un modèle de réplication
* Asynchrone & sans transaction, donc ça peut être problématique en fonction des requêtes
* Les droits sont définis dans un fichier de configuration spécifique à chaque serveur.

## Entrée

* Structure en arborescence nommée _Directory Information Tree_ \(DIT\)
* Chaque noeud est appelé une entrée : _Directory Service Entry_ \(DSE\)
* Elles sont identifiées par leur _Distinguished Name_ \(DN\) qui décrit leur emplacement dans l'arbre : `uuid=Plop,ou=users,dc=example,dc=com`.
* Le _Relative Distuiguished Name_ \(RDN\) représente l'entrée elle-même, sans son chemin. \(Dans l'exemple ci-dessus c'est `uuid=Plop`\)
* La racine, appelée _Domain Components_ \(DC\) vient du nom du domaine du serveur LDAP \(`dc=example,dc=com`\)
* Chaque entrée contient un ensemble de paires clés/valeurs : _attributs_
* Chaque entrée appartient à au moins une `objectClass` qui décrit tous ces attributs.

## Classe

* Identifiée par un _Object Identifier_ \(OID\)
* Les OID \(des classes mais aussi des types & syntaxes\) sont déposées auprès de l'IANA et donc officiels
* 3 types de classes : 
  * Structurelle : Classe "normale", chaque élément doit appartenir à au moins l'une d'entre elle
  * Auxiliaire : Complète une classe structurelle, ne peux pas être utilisée seule
  * Abstraite : Ne peut pas être utilisée telle quelle, sert uniquement à être étendue par d'autre classe.
* Elles héritent toutes d'une autre classe, celle de base étant _top_
* Elles sont rassemblée dans des _schémas_
* 2 spéciales :
  * _referral_ avec l'attribut _ref_ qui permet de pointer vers une entrée d'un autre serveur ldap __
  * _alias_ avec l'attribut _aliasedObjectName_ qui permet de créer un alias pour un DN

{% hint style="info" %}
Dans ma note originale, je sais pas si je voulais dire 2 classes spéciales ou 2 propriétés spéciales
{% endhint %}

| Propriétés | Description |
| :--- | :--- |
| NAME |  |
| STRUCTURAL/ABSTRACT/AUXILIARY | Le type de classe |
| DESC | La description |
| OBSOLETE |  |
| AUX | Listes les classes qu'elle peut compléter \(probalement unique aux auxiliaire\) |
| MUST | Attributs obligatoire |
| MAY | Attributs optionnels |
| NOT | Attributs interdits |
| SUP | Classe parente |

## Attributs

* Possède un nom, un type & une ou plusieurs valeurs.
* Un attribut n'ayant pas de valeur est simplement absent
* Aussi identifié par un OID

| Propriétés | Description |
| :--- | :--- |
| COLLECTIVE |  |
| DESC | Description |
| EQUALITY | De quelle façon doit être gérée l'égalité |
| NAME |  |
| NO-USER-MODIFICATION |  |
| OBSOLETE |  |
| ORDERING |  |
| SINGLE-VALUE |  |
| SUBSTR |  |
| SUP | Définit le supertype \(obligatoire\) |
| SYNTAX | OID de la syntaxe et nombre maxi de caractère entre {} |
| USAGE |  |

Si aucune syntaxe n'est définie, c'est celle du type parent qui sera utilisée. Je comprends pas du tout comment fonctionne la syntaxe. Pour l'égalité, etc, je sais pas si c'est des fonctions pré-définis ou pas.

```text
attributetype ( 
2.5.4.20 
NAME 'telephoneNumber'
DESC 'An integer uniquely identifying a user in a domain'
EQUALITY telephoneNumberMatch
SUBSTR telephoneNumberSubstringMatch
SYNTAX 1.3.6.1.4.1.1466.115.121.1.50 )
```

## Différence avec une bdd

* Un attribut peut avoir plusieurs valeurs
* Plus consultés que mis à jour \(sa structure est donc pensée différemment\)
* Méthode de consultation standardisée \(pour une bd, le SQL est certes standard mais chaque DBMS rajoute une couche différente\)
* LDAP pré-définit le modèle des données

## LDIF - _LDAP Data Interchange Format_

* Format standardisé permettant de représenter les données d'un annuaire LDAP
* Utilisé pour l'importation/exportation ainsi que pour représenter les opérations \(ajout, suppression modification\) effectuées.

Syntaxe :

* Commentaires uni-ligne : `#`
* Une entrée par paragraphe
* Un attribut par ligne

```text
dn: cn=John Doe, dc=example, dc=org
cn: John Doe
givenName: John
sn: Doe
telephoneNumber: +1 555 6789
telephoneNumber: +1 555 1234
mail: john@example.com
manager: cn=Barbara Doe, dc=exemple, dc=com
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: top
```

## Opérations

* Une requête contient un _Message ID_ l'identifiant
* La réponse contient
  * Le _Message ID_
  * Un code d'état \(succès, échec, etc\)
  * Code de retour de la commande \(pour search ?\)
  * Les données demandées si c'était une recherche
* Le serveur peut aussi envoyer des _Unsolicited Notifications_ qui ne sont pas des réponses à des requêtes, par exemple avant de coturer une connexion inactive.

De ce que j'ai pu voir, c'est directement les languages qui proposent des fonctions pour les accomplir, par exemple php ou openLDAP pour linux. Du coup, même si j'ai une description des opérations, j'ai pas d'exemple concret de comment les faire. C'est pas très claire.

## Connexion

#### Bind & Unbind

* Authentifie le client 
* Par convention, vérifie le mdp en le comparant avec l'attribut _userPassword_ de l'entrée correspondante
* Le mdp commence par le nom entre accolade de l'encodage `userPassword:{md5}ajgDMt3..`
* Passe par le port TCP 389 et transfert les données en claire, donc il faut sécuriser la connexion
* Le _Bind anonyme_ \(sans identifiant ni mdp\) génère une connexion anonyme. En fonction des droits, cela restreindra les opérations possibles.
* _Simple Authentication and Security Layer_ \(SASL\) Bind permet d'utiliser d'autre mécanismes d'authentification : Kerberos, Certificat Client, etc
* On ferme une connexion avec _unbind_

#### Sécurisation

1. Avec l'opération _StartTLS_ avant la connexion
2. En utilisant LDAPS \(LDAP over SSL\)
   * Utilise le port 636
   * Établit une connexion sécurisée sans utiliser l'opération _StartTLS_. 
   * Comparé à celle-ci, il est impossible de passé d'une connexion sécurisée à une non-sécurisée

## Search

* `baseObject` A partir de quelle node on fait la recherche
* `scope`
  * `sub` Récursivement depuis la base
  * `one` Un seul niveau inférieur
  * `base` Uniquement sur la base
* `filter` Critères de recherches
* `derefAliases` Suivre les alias des entrées
* `attributes` Liste des attributs à mettre dans la réponse
* `sizeLimit` Nb entry
* `timeLimit` En seconde
* `typesOnly` Ne renvoit que les types des attributs et non leur valeur

### Filtres

| Symbole | Signification |
| :--- | :--- |
| `:=` | Egalité |
| `~=` | Approximation |
| `>=` | Sup ou Egal |
| `<=` | Inf ou Egal |
| `!` | Négation |
| `&` | Et |
| `|` | Ou |
| `*` | Wildcard, utilisable que pour l'égalité |

Exemple :

* Test d'infériotié \(strict\) : `(&(X<=Y)(!(X=Y)))`
* `(&(objectclass=person)(|(givenName=Plop)(mail=pl*)))`
* Toute les personnes ayant leur nom renseignés dans la base : `&(&(objectclass=person)(telnum=*))`

Y'a aussi des filtres étendus mais j'ai pas compris.

### Compare

Prends en argument :

* Un DN
* Un nom d'attribut
* Une valeur d'attribut

Et vérifie si l'entrée correspondante contient bien un attribut ayant cette valeur.

## Add

```text
dn: <distinguished name>
changetype: add
objectclass: top
objectclass: <objectclassvalue>
<attrdesc>: <attrvalue>
<attrdesc>: <attrvalue>
```

## Modification

### Delete

```text
dn: <distinguished name>
changetype: delete
```

On ne peut supprimer un élément que s'il n'a pas de descendants.

### Modify

* Ajout d'une valeur à un attribut :

  ```text
  dn: <distinguished name>
  changetype: modify
  add: <attribut>
  <attribut>: <value>
  <attribut>: <value2>
  ```

* Suppression de valeur d'un attribut :

  ```text
  dn: <distinguished name>
  changetype: modify
  delete: attribut
  <attribut>: <value_to_delete1>
  <attribut>: <value_to_delete2>
  ```

Si on ne précise pas les attributs, ils seront tous supprimés.

* Modifier une valeur d'un attribut :

  ```text
  dn: <distinguished name>
  changetype: modify
  replace: attribut
  <attribut>: <new_value1>
  <attribut>: <new_value2>
  ```

### Modifier le DN

```text
dn: <distinguished name>
changetype: moddn
newrdn: <new_rdn>
deleteoldrdn: <1 ou 0>
newsuperior: <new_parent>
```

Pour modifier uniquement l'identifiant de l'élément, on ne précise pas le `newsuperior`.

### Abandon

Permet d'annuler une opération en lui fournissant son identifiant. Pas tout les serveurs ne la prennent en charge.

## Sources

Sorry, i don't remember. But i remember that it was complicated to find one good source so this summary is based on a lot of different articles/tutorials/etc.

