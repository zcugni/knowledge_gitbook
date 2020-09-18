# Git Commands

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
