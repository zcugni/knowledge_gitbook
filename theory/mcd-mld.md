---
description: >-
  Dictionnaire de données (Méthode jmerise). Disclaimer : old note not up to
  current standard
---

# MCD / MLD

## MDC - _Modèle Conceptuel de Données_ 

### Concepts Généraux

* Simplifier le plus possible !
* Eviter les doublons/la redondance
* Ne pas inclure des attributs qui pourraient être trouvés par calculs ainsi que ceux qui changent avec le temps
* Maximum d’homogénéité dans les noms

### Entités

* Les noms dans le texte
* Représentées par un rectangle avec un titre, puis des attributs
* Titre au pluriel

### Associations

* Les verbes dans le texte
* Représentées par un rond contenant un titre, et des attributs s’il y a lieu d’être
* Relient les entité

### Attributs

* Identifiant : unique, non susceptible au changement et facilement utilisable. Favoriser des identifiants courts et numériques
* Représentent le contenu d’une entité ou d’une association
* Au singulier

### Cardinalités

* Représentent la relation entre deux entités
* Soit 0,N ; 1,N ; 1,1 ou 0,1
* 1er nombre : minimum ; 2nd nombre : maximum
* En Jmerise, se lit du côté de la cardinalité à l’autre. En UML c’est l’opposé

### Optimisation

* Si une entité est entourée de cardinalité 1,1 ; on peut la transformer en association et simplifier le schéma
* Une entité avec une cardinalité 1,1 ou 0,1 aspire les attributs de l’association
* Si 2 entités sont reliées par une association 1,1 des 2 côtés -&gt; fusionner les entités

### Généralisation-Spécialisation \(Héritage\)

* Comme dans la programmation logiciel, on peut utiliser le principe d’héritage quand différentes tables ont les mêmes caractéristiques
* La table globale suit le principe de la généralisation, tandis que les autres celui de la spécialisation
* Ex: Une table “Personnes” globale puis 2 spécialisations “Professeur” et “Etudiant”. La table “Personnes” contiendra des données telles que le nom, prénom, etc. Bref tout ce qui est commun, tandis que l’autre ne contiendra que ce qui est spécifique à l’une ou l’autre des spécialisations, tel que le salaire par exemple
* Sur Jmerise, les héritages sont représentés par un petit triangle
* Type d’héritage:
  * XT - Toute occurrence de l’entité générique appartient uniquement à une spécialisation.
  * T - Toute occurrence de l’entité générique appartient au minimum à une spécialisation.
  * X - Toute occurrence de l’entité générique appartient à 0 ou 1 spécialisation.

## MLD-R - _Modèle Logique de Données Relationnel_ 

* On écrit les tables et leur contenu sur une ligne, de cette façon: `nomTable (identifiant, col1, col2, col3, …)`
* S’il y a des clés étrangères, elles sont précédées d’un ♯
* Pas de caractères accentués ni d’espace
* Pour l’école: nom de la table tout en majuscule

## Règle de passage d'MCD à MLD

1. Toute entité devient une table.
2. Une association dont les maximums sont 1,N se transforme en une clé étrangère d’ N dans la table du côté 1. \(Si l’association avait des attributs, ils iront aussi du côté 1\).
3. Une association dont les maximums sont N,N se transforme en une nouvelle  table prenant les 2 clés étrangères en identifiant.
4. Une association dont les maximums sont 1,1 peut soit se transformer en une fusion des 2 tables, soit en clé étrangère à mettre d’un côté ou de l’autre, ou encore des deux, en fonction de la situation.
5. Pour une association cyclique, on dédouble l’entité et on se retrouve ainsi avec les règles 2 ou 3. \(Par exemple, si on a une table ville, on en fait 2 table ville1 et ville2\).
6. Une association ternaire ou plus se transforme en une nouvelle table \(concaténation des identifiants\).
7. Pour les héritages, on crée:
   * Soit une seule table mélangeant entités globales et spécialisées et leurs attributs.
   * Soit une table par entité spécialisée ainsi que la globale et les éventuelles associations. \(Application de la règle n°4\)

## Formes Normales

* But: principalement, éviter la redondance
* Toute propriété est atomique/univaluée \(non décomposable\). Toute donnée est constante dans le temps.
* Elle est en 1FN et tous les attributs d’une entité dépendent de l’ensemble de la clé primaire \(au minimum\). _Remarque:_ Ce cas pose surtout problème quand on a des tables de liaisons avec 2 attributs ou plus comme clés primaires.
* Elle est en 2FN et tous les attributs d’une entité ne dépendent que de l’entièreté de la clé primaire \(et pas d’autre d’attributs en plus\).
* Pour optimiser les temps de traitements et éviter des jointures trop complexes on peut dénormaliser les tables. Plus d'info
  * [4 conseils et stratégies de dénormalisation](https://docs.oracle.com/cd/B14099_19/web.1012/b15901/tuning007.htm)
  * [Tom Kyte, guru de la dénormalisation](http://www.oracle.com/technetwork/issue-archive/2014/14-nov/o64asktom-2298498.html)
  * De plus, les concepteurs d’Oracle sont des maîtres dans le domaine et proposent de très bonnes formations.
* Dictionnaire de données : ils permettent de représenter une table sur un fichier excel \(par exemple\) avant d’en faire une base de données. C’est la dernière étape après le MCD et le MLD.
* Le tableau a généralement ces colonnes-là:
  * Table
  * Champ
  * Type
  * Taille
  * Clé Primaire
  * Clé étrangère
  * Unique
  * Auto-Incrémenté
  * Null
  * Commentaires
* On peut en rajouter plus selon le besoin. Le type correspond à celui utilisé par le langage/le logiciel de base de donnée concernée.

## Source

* One of the course in my computer science school

