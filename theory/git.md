---
description: 'Disclaimer : Content is good but format isn''t up to standard'
---

# Git

## Disclaimer 

* I did read the book but it was before i got into the habit of taking real notes, so this is really lackluster. I'll read it again one day

## Generalities

* Git is one of the most popular **version-control system**, before it, there was :
  * RCS : The data was kept in a local database, which made collaboration difficult
  * CVS, SubVersion, Perfoce : Centralized systems with a shared database
    * Offline access not possible
    * It's risky if there's a problem with the server/db
* Git was created for work on the Linux Kernel
  * Before, modification to it were applied as patches
  * Then, they worked with a proprietary software, but their relation with the company deteriorated and they created git
* Its goals are to be :
  * Fast
  * Simple
  * Efficient for parallel work
  * Distributed
  * Capable of supporting huge project
* General principle :
  * Each client has its **local** database, so the majority of the work is done locally
    * You must connect to the server only to upload & download modification made by others
  * Compared to other version control system, git doesn't save the differences between each version
    * It keeps a **snapshot** of the state of each file in each version
    * If the file doesn't change, the reference to the "old" snapshot is used
  * All files are checksummed with **Sha-1**

## Process

* Modified files can be added to the index \(or **staging area**\)
  * When you commit, only files in the staging area are push to the database
* There's 4 different possible state for files :
  * Untracked : Modification aren't tracked
  * Unmodified : Tracked, but no different to the version of the local db
  * Modified : Tracked, and different to the version of the local db
  * Staged : Indexed, so the modification will be commited to the local db on the next commit
* If a file is staged and then modified, is must be staged again to commit the new modifications on the next commit

## Branch

* A branch is a pointer to a specific commit
* `HEAD` contains a pointer to the current branch
  * When you move HEAD, the files are modified to correspond to the version of the pointed commit
  * The branch pointed by HEAD is **checkout**
* A branch being tracked by another one is an **upstream branch**
  * A tracking branch will by default `pull` from upstream one
* When you create a new commit, the current branch is automatically advanced to point it

![](../.gitbook/assets/branche_1.png)

## Merge / Rebase

* There's two options to combine 2 commits into one :
  * Merge : Chose which one to keep
  * Rebase : Apply one the other
* Usually : "Don't rebase commits that exist outside your repository and oh which others might have based their work"
* For both, you may need to resolve conflicts that can't be dealt with automatically
* If you merge 2 branches where one is ahead of the other, git will do a **fast forward merge** :
  * The pointer of the "late" branch is advanced to the other one
* With this situation :

![](../.gitbook/assets/merge1%20%281%29.png)

* Merge will do that :

![](../.gitbook/assets/merge2.png)

* Running  `git rebase master` from `experiment` will do that :

![](../.gitbook/assets/rebase.png)

## Source

* Git's [book](https://git-scm.com/book/fr/v2)

