# Tor

## Généralités

* Tor : The Onion Router, implementation of onion routing
* Open Source
* Représente une suite "d'outils" :
  * Tor Browser : Un fork de Firefox qui règle certains de ces problèmes de sécu et permet d'utiliser facilement Tor
  * Tor Network : Le nom donné à l'ensemble des relays \(et utilisateurs ?\)
* Son but est de fournir plus d'anonymat sur internet, en permettant de cacher qui accède à quel site 

## Tor Network

### Principe général

* Le client a une liste de "directory" qui eux-mêmes contiennent une liste de relays
* Processus:
  * Le client définit un chemin utilisant 3 de ces relays
  * Il utilise une clé _éphémère_ pour chacun d'eux, encryptant ainsi 3 fois la requête initiale
  * Le 1er nœud reçoit le paquet \(aussi appelé _cell_, qui ont une taille définie\), le décrypte au moyen de sa clé et l’envoie au prochain. Etant donné que le reste du paquet est encrypté avec les clés des 2 autres relays, il n'a aucun moyen de deviner le chemin complet ou le message
  * Pareil pour le second
  * Le troisième est _l'exit node_ qui lui, en décryptant le paquet pourra avoir accès à la requête initale et à son contenu. Il est donc impératif de passer via HTTPS. Si on tente d'accéder à un site du clearweb, la requête contiendra l'adresse IP du site final.
  * Pour le retour, l'exit node encrypte la réponse puis le passe au second relay. Celui-ci ainsi que le premier font de même.
  * Le client est le seul à avoir les 3 clés et donc pouvoir récupérer le message
* Avec ce système, aucun des nœuds ne connait le circuit complet
* Celui-ci est modifié toutes les 10min
* Vu que les cells font toutes la même taille on ne peut pas deviner si l'on est le 1er ou le 2nd noeud.
* Les échanges à l'intérieur du réseau sont des paquets TCP et non IP, entre autre afin de donner moins de détails.
* A cause des relays et de la taille des cells, il est plus lent que si l'on accédait à internet normalement.

### Directory list

* Fournissent des listes signées de relays avec leur config
* Ont eux-même une propre clé
* Les adresses des directory \(il n'y en a pas des masses\) ainsi que leur clé publique sont hardcodées dans le logiciel

### Relays

* Ils sont setup par des volontaires
* Ils ont des clés, et elles sont demandés à chaque étape du circuit pour vérifier que le relay par lequel on passe est bien celui que l'on veut \(ça empêche le spoofing\)
* Publique \(sauf bridges\)
* Il en manque, le ratio utilisateur/relay est pas ouf
  * Mais il faut une bonne connexion pour pouvoir en proposer
  * Et si on veut proposer un _exit node_ \(les plus demandés\), il y a des risques pénaux, etc

### Bridges

* Les relays étant généralement publique, ils peuvent être bloqué par des ISP/pays qui souhaitent interdire Tor.
* Pour remédier à cela, des relays spéciaux et non public, appelé Bridges, sont utilisés

### Exit Node

* Ceux-ci relient le réseau de Tor à l'extérieur, et peuvent voir le contenu des requêtes, permettant donc un man-in-the-middle si l'hôte le souhaite.
* C'est aussi les plus compliqué a hoster au niveau pénal vu que ce sont eux qui font effectivement des requêtes vers l'extérieur.

### Guard

* Un attaquant observant le traffic entre nous-1er noeud et dernier noeud-site final, pourrait comparer les timings des requêtes/réponses et autres empreinte pour déduire qui nous sommes.
* Afin de limiter cela, et car il n'est possible d'être 100% que les relays sont bien intentionnés, on peut en spécifier certains comme étant des "guards"
* Ceux-ci seront principalement \(exclusivement ?\) utilisés comme noeud d'entrée. Ainsi, s'ils sont mal intentionnés, ils pourront savoir qui nous sommes mais pas déduire où nous allons

### Onion services

* .onion site, aussi appelé "hidden services"
* Représentés par des adresses de 16 ou 56 caractères, générées aléatoirement à partir d'une clé publique
* Ces sites ne fonctionnent que via Tor, ils n'ont pas d'adresse IP
  * .onion n'est pas reconnu par les DNS
* Ces adresses représentent des "introduction points" qui servent de point de rdv entre nous et le serveur du site
  * Les 2 faisant donc 3 sauts dans le réseau pour l'atteindre
  * Cela permet de ne jamais sortir du network
  * Dans certains des articles que j'ai lu, l'introduction point n'est utilisé que pour choisir un autre point qui servira de rdv aux 2, mais l'idée reste la même
* Vu qu'ils ne sont pas indexé, on ne peut pas trouver des .onions par des recherches, il faut que quelqu'un nous donne un lien
* [dark.fail](https://dark.fail/) \(darkfailllnkf4vf.onion\) répertorie une bonne partie des sites les plus connus ainsi que leur miroirs. Il permet aussi de vérifier l'intégrité de ceux-ci.

### Misc

* Le network est peu adapté pour du peer-to-peer. Evitez de le faire avec, ça surcharge le réseau. \(Ainsi que des trucs de streamings, etc, mais particulièrement les torrents\)
* Le logiciel en lui-même est signé, et c'est important de vérifier qu'on a effectivement le bon vu qu'entre autre les directory sont définit dans le code.

## Sources

* Tor's [website](https://www.torproject.org/) & [documentation](https://2019.www.torproject.org/docs/documentation.html.en)
* Misc research

