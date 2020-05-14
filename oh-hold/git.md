---
description: 'Disclaimer : Content is good but format isn''t up to standard'
---

# Git

## Évolutions des gestionnaires de contrôles

La conservation de l’historique de modification a évoluée de ces manières :

* RCS : Conservée dans une base de donnée locale à chaque ordi \(ce qui rends la collaboration difficile\).
* CVS, SubVersion, Perfoce : Système de contrôle centralisé, avec une base de données qui disptach les dernières version à chaque utilisateur. \(Risquer car si la bdd/le serveur meurt, on perd tout. Et accès hors-ligne impossible\).
* Git : Stocker la base individuellement sur chacun des ordis. \(Je reviens sur les avantages après\).

## Historique de Git

Git a été créé pour travailler sur le Kernel Linux. A l’époque, les changements sur celui-ci étaient passé en patch, ils ont ensuite commencé à utiliser un programme propriétaire, puis quand la relation avec cette entreprise s’est détériorer, ils ont eu besoin d’en créer un de toute pièce.

Les objectifs de Git étaient les suivants :

* Rapidité
* Design simple
* Pensé pour permettre du développement parallèle \(avec plusieurs branches\)
* Complètement distribué
* Capable de supporter de gros projet

Ces objectifs ont été respectés et atteints.

## Fonctionnement de Git général

### Diff VS Snapshot

Comparé à la majorité des gestionnaires de version, Git ne sauve pas les différences des fichiers à chaque version, mais plutôt l’état des fichiers en eux-même \(snapshot\). \(Quand un fichier ne change pas, il conserve simplement la référence au fichier d’origine\).

Autres gestionnaires :

![](../.gitbook/assets/autre_gestionnaire.png)

Git :

![](../.gitbook/assets/git.png)

### Presque tout est fait en local

Vu que la base de données est sur chaque ordi, la majorité des interactions sont faites en local. On peut commit nos changements, explorer les anciens fichiers, créer des branches, etc. On ne se connecte au serveur que pour mettre en ligne nos modification et récupérer celles des autres.

### Sha-1

Git checksummed tous les fichiers en utilise le hash Sha-1. Il est donc impossible pour un fichier d’être modifié sans qu’il ne soit au courant.

### Processus

Les fichiers modifiés peuvent être ajouté à “l’index” \(ou “staging area”\), et au moment de commit, seuls les fichiers indexé seront effectivement pusher sur la bdd.

Plus précisément, les fichiers peuvent être dans 4 états différents :

* Untracked : Les changements ne sont pas surveillés
* Unmodified : Le fichier est tracked mais corresponds à la version de la bdd \(locale\)
* Modified : Le fichier est tracked, et des changements ont été repérés
* Staged : Les changements du fichiers ont été indexés et seront rajouté à la bdd \(locale\) au prochain commit.

![](../.gitbook/assets/staged.png)

Si des modifications ont été staged \(avec git add\) et que l’on remodifie le fichier, il faut l’add à nouveau. Sinon, seules les premières modifications \(déjà staged\) seront prises en compte.

## Anatomy of a commit / history

![](../.gitbook/assets/anatomy_commit.png)

Quand on fait un commit, ça crée un objet de commit qui contient, entre autre, un pointeur vers “l'arbre” représentant le snapshot de ce qui est staged. Cet arbre est composé de pointeur liant un nom de fichier au checksum du-dit fichier. Les fichiers sont appelé “blob”.

L’objet commit contient aussi un pointeur vers son ou ses parents s’il y a. \(0 si c’est le premier, 1 en général ou plusieurs s’il vient d’un merge\).

## Branches

Une branche est simplement un pointeur vers un commit en particulier. Git garde dans HEAD un pointeur vers la branche courante.

![](../.gitbook/assets/branche_1.png)

Quand on crée un nouveau commit, la branche courante avance automatiquement pour le pointer :

![](../.gitbook/assets/branche_2.png)

Si on revient ensuite en arrière, qu’on change de branche, et qu’on commit de nouveaux changement, on aura créé 2 branches divergentes qui pourront être merge ou rebase :

![](../.gitbook/assets/branche_3.png)

Quand on déplace HEAD, les fichiers dans le dossier sont modifiés pour correspondre à la version du commit pointé.

La branche qui est pointée par HEAD est la branche qui est “checkout”.

Une “tracking branch” est une branche ayant une relation directe avec une autre, sa “upstream branch”. Quand on git pull depuis la tracking, git devine qu’on parle de l’upstream et de son serveur.

## Merge / Rebase

Quand deux commits ont un ancêtre commun mais diverge l’un de l’autre, on a deux options pour les ramener à un seul commit. Soit on les merge en choisissant quelles parties des deux on garde, soit on re-applique l’un des deux à l’autre.

Si on a cette situation de base :

![](../.gitbook/assets/merge1%20%281%29.png)

### Merge

Le merge donnera ça :

![](../.gitbook/assets/merge2.png)

Si on merge 2 branches qui ne divergent pas, mais donc l’autre est simplement en avance sur l’actuelle, on fait un “fast-forward merge”. Git se contente d’avancer le pointeur de la branche actuelle jusqu’au commit de l’autre.

### Rebase

Lancer `git rebase master` depuis “experiment” donne donnera ça :

![](../.gitbook/assets/rebase.png)

\(Il faut quand même merge master après coup pour le fast-forward sur experiment\).

La règle général qu’il donne est

> Do not rebase commits that exist outside your repository and people may have based work on them.

S’il y a un conflit, il nous demandera de le gérer comme pour un merge.

### Cherry-pick

Un Cherry pick est comme un rebase d’un seul commit

### Philosophie

Pour ce qui est de choisir entre les 2, le but n’est pas le même. Avec merge, on veut garder une trace exacte de l’histoire du projet et du processus de modification, tandis qu’avec rebase, on veut plutôt avoir une vue globale et claire de comment le projet à été construit.

## Gitignore

Les fichiers du gitignore ne seront pas traqué. Lui-même par contre doit être commit avec le reste afin qu’il soit récupérable par d’autres. Ainsi, si on a des fichiers persos qu’on veut ignorer, il vaut mieux les préciser dans : `.git/info/exclude`

## Git Norme

* Capitalized, short \(50 chars or less\) summary
* More detailed explanatory text, if necessary. Wrap it to about 72 characters or so.
* In some contexts, the first line is treated as the subject of an email and the rest of the text as the body.
* The blank line separating the summary from the body is critical \(unless you omit the body entirely\). Tools like rebase can get confused if you run the two together.
* Write your commit message in the imperative: "Fix bug" and not "Fixed bug" or "Fixes bug." This convention matches up with commit messages generated by commands like git merge and git revert.
* Further paragraphs come after blank lines.
* Bullet points are okay
* Typically a hyphen or asterisk is used for the bullet, followed by a single space, with blank lines in between, but conventions vary here
* Use a hanging indent

## Alias Commun

```text
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
$ git config --global alias.unstage 'reset HEAD --'
$ git config --global alias.last 'log -1 HEAD'
```

## Commandes

* `git help`
* `git config`
* `git status` Shows changed files
* `git add` Stage changes
* `git log` Show details of every commits and pointers of branches
* `git rm <file>` Untrack & delete the file
  * `--cached` Untrack the file
* `git cherry-pick <commit_sha>` Rebase uniquement ce commit
* `git fetch`
  * `<remote>` Télécharge les données dans nos dossier locaux \(mais ne merge pas automatiquement\).
  * `--all` Récupère depuis tous les remotes
  * Si j'ai `git push -f` et que depuis un autre ordi je veux récupérer ce qui a été fait, mais sans merge, simplement en remplaçant mes fichiers, il faut faire `git fetch` puis`git reset --hard remote/branch`
* `git merge <branch>` Merge la branche mentionnée dans l’actuelle.
* `git rebase`
  * `<branch>` Rebase \(applique\) la branche actuelle sur la branche mentionnée
  * `<base_branch> <to_rebase_branch>` Rebase \(applique\) la seconde branche à la première
  * `git rebase -i HEAD~3` Rebase interactif pour les 3 derniers commits
* `git commit`Push change to \(local\) database.
  * `-m` Ajoute le titre directement
  * `--amend` Remplace le précédent commit par celui-ci \(lle précédent est entièrement perdu\)
* `git pull` Fetch et merge automatiquement les données de la branche upstream \(mais vaut mieux explicitement utiliser ces commandes\)

### git diff

* `--staged` Show exact diff on files not yet staged
* `--check` Check les whitespaces
* `<commit_sha>~ <commit_sha>` Show diff with this commit

### git remote

* `git remote` Liste tous les serveurs remote
  * `-v` 
  * `rename <nom>`
* `git remote add <name> <link>`
* `git remote prune <remote_name>` 

### git push

* `git push <remote>`
  * `<branch>` Push sur la branche du même nom dans remote
  * `<my_branch:remote_branch>` Push notre branche sur une branche spécifique du remote
  * `<tagname>` Push un tag
  * `--delete <branch>`
  * `--tags`  Push tous les tags

### git tag

* Un tag est une phrase attachée à une version spécifique \(par exemple “v1.0”, “v2.0”\)
* 2 types :
  * Léger : Un pointeur avec un nom vers un commit précis
  * “annotés” : Plus complet, contiennent le nom, email et message de la personne les ayant créés.
    * Ils peuvent aussi être checksum et sont ajouté à la bdd.
* Options :
  * `-a <short_name> -m <”message”>` Crée un tag annoté avec un message
  * `-d <tagname>` Supprime le tag
* Pour un ancien commit rajouter son checksum à la fin de la commande

### git branch

* Liste toutes les branches \(la checkout actuelle est notée avec une \*\)
* `<branch>` Crée une nouvelle branche
* `-d <branch>` Supprime la branche 
* `-v` Liste le dernier commit de chaque branche 
* `-vv` Liste quelle branche traque qui et si elles sont en avances/en retard \(état du last fetch\) 
* `-u <remote><branch>` Se mets à traquer la branche désignée
* `--merged` Liste des branches déjà merge 
* `--no-merge` Liste des branches pas encore merge

### git checkout

* `<branch>` Déplace HEAD sur la branche en question
* `-b <branch>` Crée une nouvelle branche et fait pointer HEAD dessus
* `-b <branch> <remote:branch>` Crée une nouvelle branche traquant une autre de remote
* `--track <remote>/<branch>` Pareil mais sur branche courante \(à vérifier\)

## Source

* Git's [book](https://git-scm.com/book/fr/v2)

