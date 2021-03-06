# Git Commands

## Naming convention

* Capitalized, short \(50 chars or less\) summary
* More detailed explanatory text, if necessary. Wrap it to about 72 characters or so
* In some contexts, the first line is treated as the subject of an email and the rest of the text as the body
* The blank line separating the summary from the body is critical \(unless you omit the body entirely\)
  * Tools like rebase can get confused if you run the two together
* Write your commit message in the imperative: "Fix bug" and not "Fixed bug" or "Fixes bug."
  * This convention matches up with commit messages generated by commands like git merge and git revert
* Further paragraphs come after blank lines
* Bullet points are okay
  * Typically a hyphen or asterisk is used for the bullet, followed by a single space, with blank lines in between, but conventions vary here
* Use a hanging indent

## Commun aliases

```text
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
$ git config --global alias.unstage 'reset HEAD --'
$ git config --global alias.last 'log -1 HEAD'
```

## Commands

* `git help`
* `git config`
* `git status` Shows changed files
* `git add` Stages changes
* `git log` Shows details of every commits and pointers of branches
* `git rm <file>` Untrack & delete the file
  * `--cached` Untrack the file
* `git cherry-pick <commit_sha>` Rebase only this commit
* `git fetch`
  * `<remote>` Download data in local directories \(but don't automatically merge\)
  * `--all` Retrieve from all remotes
  * If you `git push -f` and you want to retrieve what you did from another computer, but without the merge, by simply replacing the files, use `git fetch` then `git reset --hard <remote>/<branch>`
* `git merge <branch>` Merge the branch into the current one
* `git rebase`
  * `<branch>` Rebase \(apply\) the current branch onto the specified one
  * `<base_branch> <to_rebase_branch>` Rebase \(apply\) the second branch to the first
  * `git rebase -i HEAD~<nb>` Interactive rebase for the last nb commits
* `git commit`Push change to the local database
  * `-m <title>` 
  * `--amend` Remplace the previous commit by this one \(the previous one is entirely lost\)
* `git pull` Automatically fetch & merge the data of the upstream branch \(but you should do it explicitly\)

### git diff

* `--staged` Show exact diff on files not yet staged
* `--check` Check whitespaces
* `<commit_sha>~ <commit_sha>` Show diff with this commit

### git remote

* `git remote` List all remote servers
  * `-v` 
  * `rename <name>`
* `git remote add <name> <link>`
* `git remote prune <remote_name>` 

### git push

* `git push <remote>`
  * `<branch>` Push to a branch of the same name on the remote
  * `<my_branch:remote_branch>` Push to a specific branch on the remote 
  * `<tagname>` Push a tag
  * `--delete <branch>`
  * `--tags`  Push all tags

### git tag

* A tag is a sentence tied to a specific version \(for example : “v1.0”, “v2.0”\)
* 2 types :
  * Light : A pointer with a name to a specific commit
  * Annotated : More complete, has the name, email & message of the person who created it
    * Can be checksum and added to the db
* Options :
  * `-a <short_name> -m <”message”>` Create an annotated tag
  * `-d <tagname>` Delete the tag
* For an old commit, add it's checksum to the end of the cmd

### git branch

* List all branches \(the current checkout one is noted with an `*`\)
* Options :
  * `<name>` Create a new branch
  * `-d <branch_name>` Delete the branch
  * `-v` List the last commit of each branch 
  * `-vv` List which branch track what and if they're in advance or not \(status of the last fetch\) 
  * `-u <remote><branch>` Track the specified branch
  * `--merged` List already merged branches
  * `--no-merge` List not merged branches

### git checkout

* Options :
  * `<branch_name>` Move the HEAD on the specified branch
  * `-b <name>` Create a new branch and point HEAD to it
  * `-b <name> <remote:branch>` Create a new branch tracking the specified one
  * `--track <remote>/<branch>` Same but on the current branch \(check that\)

## Source

* Git's [book](https://git-scm.com/book/fr/v2)

