# Burp Proxy

## Intro

* Suite d'outils permettant de simplifier plusieurs aspects du pen testing.
* Les tabs du haut représentent les outils disponibles 
* Les tabs inférieur représentent leurs options.

## Setup

* Paramétré dans Burp dans `Proxy -> Options`
* Il faut aussi récupérer le certificat [http://burp/](http://burp/).

### Avec Firefox

* Firefox permet de paramétrer un proxy uniquement pour lui et non tout le système, ce qui est pratique
* Dans firefox, aller dans `Options -> Paramètres (tout en bas) -> "Configuration manuelle du proxy" -> localhost on port 8080 (si ça corresponds à nos params) -> Cocher "Utilise ce proxy pour tout les protocoles"`
* Importer le certificat via `Options -> Vie privée et sécurité -> Certificats -> Afficher les certificats -> Importer -> Coher "Confirmer cet AC pour identifier les sites web"`

### Avec Google

Utiliser l'extension _Fox Proxy_

## Utilisation

By default it's in the **intercept** mode, ce qui va arrêter le traffic. Pour changer ça aller dans :`Proxy -> Intercept -> Intercept is on/off`

### Filter tab

The filter tab is present in nearly every part of the burp suite, and let's you filter the request/response you want to see on multiple options, including the scope \(explain in the target part\)

### Target \(Ctrl + Shit + T\)

Show a tree view of everything you browsed through the proxy.

Specify scope in "Target -&gt; Scope", it can be a keyword, an ip range, an host, regex, etc. We can also add a browsed entry to the scope by right clicking it.

### Proxy \(Ctrl + Shift + P\)

The **Intercept** tab does exactly that to every request, it's on by default. You can modify request on the fly with it.

The **HTTP History** tab shows the history of all requests made and responses received. You can highlight request in different color \(and filter by that as well as comment them.

In the **Options** tab :

* The **Match & Replace** section let's you  modify all traffic going through the proxy in one go.
* In the **Response Modification** section you can, among other things, "Unhide hidden fields" and disable javascript validation \(to check if it's only a client side verification\)

### Intruder \(Ctrl + Shift + I\)

Permet de brute force / fuzzer comme le ferait un script mais plus simplement.

La tab **Position** permet de sélectionner toutes les parties de la requête que l'on veut tester ainsi que le type d'attaque.

La tab **Payload** permet de définir pour chaque position \(qu'on sélectionne avec la "Payload Set"\) les payload que l'ont veut tester. Il y en a plusieurs type, de la simple liste \(burp en propose par défaut\), à l'incrémentation de nombre/date, fichier, etc.

Dans la tab des **Options** **Grep Match** permet d'ajouter une check box à la liste des résultats indiquant si une string particulière se trouvait dans la réponse par exemple. **Grep Extract** peut être similairement utile.

Une fois tout paramétré, on peut lancer l'attaque avec le bouton **Start Attack** dans la tab des **Payload**, cela va ouvrir une nouvelle fenêtre avec les résultats.

### Repeater \(Ctrl + Shift + R\)

Permet de répéter une requête en l'a modifiant

### Decoder

Permet d'encoder/décoder du text en base64, hex, hash, etc.

### Scanner

Pro version only tool, finds vulnerabilities & information. Be cautious while using it, it can mess up an application if you just launch it on everything.

### Sequencer

Si un cookie est set à une certaine valeur à chaque requête, le sequencer permet de générer des centaines de cette requête, de récupérer le cookie à chaque fois et d'analyser la façon dont il change.

## Pluggins

De nombreux pluggins existent, notamment ces deux :

* Havertor : Bien plus puissant que Docder
* Logger++ : Bien utile \(doit être la dernière extension de la liste\)

