# Bitcoin

## Principe

* Monnaie digitale & décentralisée
* Au lieu d'avoir un institut unique vérifiant la véracité des transactions, l'historique de toutes les transactions est noté dans une "db" commune, dont chaque utilisateur à une copie
* Chaque utilisateur peut rajouter des transactions à cet historique
  * Pour cela, il utilise sa clé privée pour générer une signature à partir de la transaction
  * Sa clé publique permet de vérifier que c'est bel et bien lui qui l'a écrite
  * Chaque utilisateur broadcast ensuite ces changements
  * On ne peut pas écrire une transaction pour laquelle on n'a pas de fond
* L'historique est en fait une chaine de blocs de 2400 transactions chacun
  * Pour qu'un bloc soit valide, il doit être composé du hash du bloc précédent, de la liste des transactions et d'un "Proof of Work"
  * Celui-ci est un nombre qui ajouté au bloc permet d'obtenir un certain hash \(via SHA256\), par exemple un hash commençant par 30 zéros
  * Cela demande évidemment de la puissance et du temps \(vu que c'est plus ou moins du bruteforce\), et le goal à atteindre \(le nombre de zéros nécessaires\), varie en fonction du temps pour qu'il y est un nouveau bloc validé chaque 10min environ
* Les mineurs s'occupent de récupérer les transactions qui sont broadcast, d'en faire des blocs et de chercher ce nombre leur permettant de les valider
  * Ils broadcastent ensuite le nouveau bloc
  * Si un utilisateur reçoit 2 chaines de blocs contradictoire, il attends de voir laquelle grandit la plus vite et considère que c'est la valide
  * Cela vaut donc la peine d'attendre quelques nouveaux blocs avant de partir du principe qu'une transaction est effectivement prise en compte
  * On parle de "mineur", car un nombre arbitraire de bitcoin est créé "de toute pièce" et offerts au mineur pour chacun de ces blocs validés
  * Ce nombre diminue par contre au fil du temps pour qu'il n'y ai jamais plus de 21 millions de bitcoin
  * Les blocs étant petit, le temps pour les valider long et les transactions nombreuses, les utilisateurs donnent une "taxe" supplémentaire aux mineurs pour être choisit et insérer dans le prochain bloc, donc ceux-ci continueront de gagner de l'argent malgré tout

## Wallets

* Chaque transaction est associée à une adresse
* Chaque porte-monnaie peut contenir plusieurs adresses, il est même conseillé d'en générer une nouvelle à chaque fois afin qu'on ne puisse pas relié les transactions les unes aux autres
* Vu que les transactions sont à jamais notées dans l'historique, si on peut être relié à une adresse, tout ce que nous avons fait avec elle peut être facilement récupérer
* Certaines entreprises proposent de perdre nos transactions au milieu d'autre en en générant des similaires pour une petite charge \(en très gros\), ce qui peut un peu aider mais ne règle pas à 100% le problème.

## Sources

* Bitcoin website
* 3Blue1Brown's [video](https://www.youtube.com/watch?v=bBC-nXj3Ng4)

## To check

* [Original Bitcoin paper](https://bitcoin.org/bitcoin.pdf) 
* [Block explorer](https://blockexplorer.com/)
* [Blog post](https://goo.gl/BW1RV3) by Michael Nielsen \(Particularly good for understanding the details of what transactions look like\)
* Video by [CuriousInventor](https://youtu.be/Lx9zgZCMqXE)
* Video by [Anders Brownworth](https://youtu.be/_160oMzblY8)
* [Ethereum white paper](https://goo.gl/XXZddT)

