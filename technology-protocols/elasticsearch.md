---
description: >-
  Disclaimer : je suis juste tombée dessus dans une boxe mais j'ai pas tout
  étudié
---

# ElasticSearch

## Généralités

* Une sorte de db avec une API Rest qui réponds en JSON.
* Souvent couplée à une interface Kibana \(node.js\)
* Souvent couplé à Logstash qui permet de collecter des données de différentes sources \(log, db, etc\) et les envoyer à Elasticsearch

## Endpoint

Y'a [plusieurs](https://www.elastic.co/guide/en/elasticsearch/reference/current/rest-apis.html) endpoint de l'api qu'on peut appeler

### Search

Une sorte de select des données

Exemple : `http://ip/_search` renvoi ce genre de json :

```text
{                          
  "took" : 27,
  "timed_out" : false,
  "_shards" : {
    "total" : 11,
    "successful" : 11,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 2184, 
    "max_score" : 5.9335938,
    "hits" : [
      {
        ...
      },
      {
        ...
      },
      ....
    ]
  }
}
```

* Pour que les résultats soit joliment présenté au lieu d'être sur une seule ligne, rajouter "pretty" : `http://ip/_search?pretty` \(Il peut être écrit n'importe où après le ?\)
* Par défaut, il ne montre que les 10 premiers résultats, donc si on veut récupérer tout les résultats \(ici 2184\), il faut faire `http://ip/_search?pretty&size=2184`
* On peut chercher un terme en particulier avec `http://ip/_search?pretty&q=terme`
* Pour chercher uniquement dans une table donnée, faire ainsi : `http://ip/table_name/_search?pretty&q=terme`

### Cat

* Donne des infos sur la bd
* `http://ip/_cat/indices` Permet d'obtenir le nom des "tables"
* Rajouter `?v` pour avoir le nom des colonnes dans le résultat \(aka, résultat plus lisible\)

## Source

* Elastic Search's Rest Api's [doc](https://www.elastic.co/guide/en/elasticsearch/reference/current/rest-apis.html)

