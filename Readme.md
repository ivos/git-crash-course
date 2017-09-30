# Git crash course

This is a practical, no-BS crash course of Git, the most-used version control system.

The course is organized by roles in which people might be using Git.
The chapters are ordered by increasing complexity and responsibility of the role in the Git workflow.

1. [Concepts](#1-concepts)
2. [Installation](#2-installation)
3. [Accessing Git repo](#3-accessing-git-repo)
4. [Browsing Git repo](#4-browsing-git-repo)
5. [Contributing to Git repo](#5-contributing-to-git-repo)
6. [Managing Git repo](#6-managing-git-repo)
7. Creating Git repo

## 1. Concepts

### 1.1 Git repo and commits

A version control system (VCS) manages a tree of files within a directory, 
keeping track of all changes to them, so that the complete history is available: 
once a version of a file is stored to a VCS, it will never be lost and you can always get back to it.
This history kept by the VCS is called a **repository** (repo for short).

A Git repo starts with an initial commit.
The first commit adds some files to empty repo and marks the first version of the repo.
Every subsequent **commit** is then based on one or more previous commits
and contains the specification of changes to be made to all the files
to get from the previous version of the repo to the new one.
Each of these changes can be one of:
- addition of a new file,
- deletion of an existing file,
- modification of an existing file.

The modification change specifies rows to be deleted
and rows to be added within the file and optionally renaming of the file.

Each commit is identified by a SHA **hash** which is a hash of the contents of the commit,
like the identifiers of the previous commits this one is based on and the specification of the changes.

A commit has a **commit message** describing the content and purpose of the commit.
It is written by the author of the commit and enables a quick overview of the history of the commits.

A Git repo is actually in its core just a set of linked commits.
It can be visualized as an oriented graph in which the nodes correspond to commits
and the edges represent the “is based on” relationship between commits.
Any commit can become a base for any number of other commits
and any commit can be based on one or more other commits, so the graph is not a tree.

> **Commit**: specification of changes to get from one version of the repo to the next one.

### 1.2 Branch

We can refer to a commit by its hash, e.g. `9e7d056`,
but that would be impractical for any common purposes.
We can instead give a name to a specific commit and then refer to it by this name.
Such a name is called a **branch**.
So branch is just a user-defined name pointing to a given commit.
You can manipulate your branches as you wish,
creating and deleting them and moving them from one commit to another.

There is usually a branch called `master` which usually points to the last release version.
There is usually a `develop` branch pointing to the current development version.

Branch names can be **namespaced** using a slash `/` separator.
You can e.g. namespace all the branches containing work-in-progress
that is not yet integrated into `develop` with "feature",
so `feature/edit-customer` branch may be used to implement new functionality to edit a customer.
Notice also that branch names are in kebab case, i.e. all lowercase with  words separated by dashes.

> **Branch**: name of a commit.

### 1.3 Remote

You can create multiple copies of a repo,
located in a different directory, drive or other computer altogether.
You can then maintain all the copies of the repo synchronized using Git remotes.
A **remote** is defined within a copy of the repo
and represents a named connection to another copy of the same repo.
The connection is defined by a URL, the protocol can be SSH, HTTP or file.

While the **HTTP**-based connection is the most versatile,
usually being able to pass through corporate security walls,
it means you have to authenticate (with a username and password)
every time you communicate with that remote
(which might be quite often in your daily routine),
which renders it a bit impractical for substantial work.

The **SSH**-based connection requires some upfront work to set up the SSH key
(see _Installation_ and _Accessing Git repo_ below),
however the operating system or IDE is then usually able to keep the SSH key in use
and therefore lets you communicate with the remote without any hassle.

You can use also the **file** protocol e.g. to keep a backup copy of the repo on a flash disk.

So Alice and Bob can each have their own copy of the repo.
Alice can define a remote pointing to Bob’s copy and vice versa,
e.g. by sharing each other’s disk and using the file protocol.
Then they can both work on their own copy and still consolidate and synchronize each other's changes.

This approach would however soon become unusable with more members joining the team.
That's why a central copy is usually set up on a shared computer (server)
with SSH-based access configured for all the team members.
Then both Alice and Bob (and any other team member) can define the very same remote pointing to the server
and synchronize their repo to the server’s repo
which would perform as a sort of a “main” repo into which all the changes need to be integrated.
Such a central, **server-based repo** is usually named `origin`.

You can however have multiple remotes and name each one of them as you please,
e.g. you can put a primary remote on GitHub (and name it `github`)
and a secondary remote on Bitbucket (and name it `bitbucket`).

> **Remote**: named connection to another copy of the repo.

### 1.4 Remote branch

When you synchronize with a remote your repo receives the commits and also the branches from the remote repo.
These remote branches are namespaced with the name of the remote repo,
so e.g. `origin/master` is a **remote branch** pointing to the same commit
as the `master` branch points to on the `origin` remote.
Similarly, `origin/feature/edit-customer` tells you
where is the `feature/edit-customer` branch on the remote named `origin`.

You cannot manipulate remote branches.
You can however setup a local branch to **track** a corresponding remote branch,
so that e.g. when `origin/develop` branch moves ahead by three new commits
you can easily advance your local `develop` branch in the same way.

> **Remote branch**: virtual branch showing the position of the branch on the remote.

### 1.5 Tag

A **tag** is a name assigned to a commit, usually to mark a release.
Tags work the same as branches but are treated separately because they are used for different purposes.
While branches are used for ongoing work and are ever-changing,
tags are assigned once and usually never changed afterwards.

> **Tag**: fixed name of a commit.


## 2. Installation

### 2.1 Install Git

#### 2.1.1 Linux

Redhat-based systems:

    sudo yum install git gitk git-gui

Debian-based systems:

    sudo apt-get install git gitk git-gui

Verify Git is installed:

    git --version

You should see something like `git version 1.8.3.1`.

#### 2.1.2 Windows

Go to [https://git-for-windows.github.io/](https://git-for-windows.github.io/),
download and run the installer.

Verify Git is installed:

    git --version

You should see something like `git version 1.8.3.1`.

### 2.2 Global Git setup

Set up your identity.
This will be used by Git to identify the author of your future commits.

    git config --global user.name "My Name"
    git config --global user.email my.email@example.com

Verify your global setup

    git config --global -l

### 2.3 Create an SSH key pair

Git repos are often accessed via SSH protocol.
You first have to generate an SSH key pair (public and private key) to be able to use such a remote Git repo.

Follow
[the instructions here](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)
to generate the key pair.
The instructions are specifically for GitHub, but the generation of the keys is generic.

### 2.4 Getting help

#### 2.4.1 System man pages

Each Git command has its manual page explaining its syntax and all the options.
The man page name consists of `git-` prefix followed by the Git command.

So e.g. to invoke man page for the `git status` command

    man git-status

#### 2.4.2 Web resources

* Git home page: [https://git-scm.com/](https://git-scm.com/)
* Git reference manual (same as man pages): [https://git-scm.com/docs](https://git-scm.com/docs)
* Pro Git (a rather advanced book on Git):
    [https://git-scm.com/book/en/v2](https://git-scm.com/book/en/v2)
* List of other Git resources:
    [https://git-scm.com/documentation/external-links](https://git-scm.com/documentation/external-links)


## 3. Accessing Git repo

A Git repo will be published on a **URL**.
It might provide a single URL (usually in SSH-form) or it might provide multiple URLs (usually HTTP and SSH).
For any serious work always **prefer SSH-form URL** over HTTP,
as SSH usually allows for hassle-free communication with the remote,
while HTTP usually requires repeated authentication
(at least on the console, an IDE may support caching the credentials).

An SSH-form URL looks like `git@github.com:ivos/git-crash-course.git`,
while an HTTP-form URL looks like `https://github.com/ivos/git-crash-course.git`.

To use the SSH-form URL, you first have to **install your public key** on the server providing the Git repo.
Public Git services usually have a how-to guide
([GitHub how-to guide](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)).
For in-house repos ask your server admin to add your public key to the Git server.

Once you have the URL of the repo (and the SSH key installed for SSH-form URL)
go to a directory where you keep your Git repos (create a new one if you don't have it yet)
and **clone** the repo.
Cloning will create a new subdirectory named by the repo itself.
You can then `cd` into it:

    cd my/repos/dir
    git clone https://github.com/ivos/git-crash-course.git
    cd git-crash-course


## 4. Browsing Git repo

Find out which branch you are interested in.
If you want to see the current development status it will usually be `develop`.
If you want a particular release version, look for the corresponding tag.
If you have no idea, look around the repo (see _Inspecting commit history_ below)
and then choose the branch you want to see.

Then make sure you have a local branch tracking the corresponding remote branch
and switch to it (see _Working with branches_ below).

### 4.1 Working with branches

Show current branch

    git status

If the current branch is tracking a remote branch, their relative position is also listed.

List all branches

    git branch -a

Create a new local branch tracking a given remote branch (`origin/my-branch`)

    git branch my-branch origin/my-branch

List local branches

    git branch

Current branch is marked by asterisk.

List all branches showing all details (including branch tracking)

    git branch -a -vv

Switch to another local branch

    git checkout my-branch

If the local branch you want to checkout does not exist
it will be created automatically from `origin/my-branch`
(tracking it) and then you will be switched to this newly created local branch.
So the `checkout` command can be used as a shortcut for the two actions.

### 4.2 Working with tags

List tags

    git tag

Switch to a tag

    git checkout my-tag

After you are finished inspecting the tag
remember to switch back to a proper branch (e.g. `develop`).

### 4.3 Inspecting commit history

Show commit history in a graphical tool

    gitk

> Customize `gitk` to show all branches:
> create a new view (View -> New view), name it `All branches`,
> check `Remember this view`,
> check `All refs`, `All branches`, `All tags`, `All remote tracking branches`
> and apply the new view.

You can also use an IDE (Eclipse, IDEA, etc.) to show graphical commit log.

Alternatively, you can use the following commands to review the log in shell.

Quick overview of last commits on all branches

    git log --all --graph --oneline

Quick overview of last commits on the current branch

    git log --graph --oneline

Detailed review history of commits

    git log --all --graph

The Git `log` command is actually quite versatile (see its man page).
It allows for cool things like listing what I have done today

    git log --since=00:00:00 --all --no-merges --oneline --reverse --author=ivo.maixner@gmail.com

Or listing the most active repo users within the last 3 weeks

    git shortlog -sn --since='3 weeks'

### 4.4 Updating local repo

When you are interested in a particular branch (let's assume it is `develop`)
and this branch advances while you are reviewing it,
you can update the local repo by fetching new changes from the remote repo
and moving your local branch ahead in line with the corresponding remote branch.

#### 4.4.1 Fetching new changes from remote repo

Fetch new changes from remote repos

    git fetch --prune

The `--prune` option will remove any unused local branches
that have been deleted remotely in the meantime.

The `fetch` command will bring all new commits from the remote repo into the local one
and advance all remote branches to match those in the remote repo.
The local branches will remain untouched, however.
So now you have to advance your local `develop`
to match the corresponding remote branch `origin/develop`.

#### 4.4.2 Fast-forwarding local branches

Standing in the local `develop`, issue the `status` command

    git status

You should see

    # On branch develop
    # Your branch is behind 'origin/develop' by 1 commit, and can be fast-forwarded.
    #   (use "git pull" to update your local branch)
    #
    nothing to commit, working directory clean

Make sure Git tells you that the local branch "_can be fast-forwarded_" to the remote branch.
That means the remote branch has some new commits on top of your local branch
and you can simply advance the local branch by merging in the remote branch.

Merge the remote `origin/develop` into the local `develop`

    git merge origin/develop

You will see something like

    Updating 84b36a0..6036730
    Fast-forward
     1 file changed, 1 insertion(+)

Now your local `develop` has been advanced to the very same commit as the remote `origin/develop`,
which effectively means that the files in the local repo directory were updated
with changes from the remote repo.

#### 4.4.3 Hard-resetting local branches

When `git status` tells you that the local and remote branches "_have diverged_", like

    # On branch feature/edit-customer
    # Your branch and 'origin/feature/edit-customer' have diverged,
    # and have 1 and 1 different commit each, respectively.

it means that someone else have moved the remote branch onto a commit
that is not based on the last commit your local branch points to.
This usually never happens with main branches like `master` and `develop`,
but it may frequently happen with feature branches.

In such a case _never_ simply merge the remote branch as above.
You have to hard-reset the local branch to the commit of the remote branch

    git reset --hard origin/feature/edit-customer

Be sure you have **no uncommitted changes** in the files though,
as they might be lost after the hard-reset.

## 5. Contributing to Git repo

Find out **the branch** to which you are supposed to contribute to.
It will usually be `develop` (or `master`).
Let's assume it is `develop`.

### 5.1 Forking out a new feature branch

First make sure you are **on the local branch** `develop` (tracking the remote one)
and that it is **up-to-date** (see above),
this might save you some work with merging later.

Create a new feature branch
(let's assume you want to add new functionality to update a customer)
and switch to it

    git branch feature/update-customer
    git checkout feature/update-customer

**Push** the branch to `origin` and setup tracking

    git push -u origin feature/update-customer

This makes the local branch track the remote branch
so that from now on you can do just `git push` to push the local branch to the remote repo
and Git will know to which remote branch you are pushing.

### 5.2 Committing your changes

After you have made your changes to the local files you want to commit them.
Always **review** all your changes before you commit them.
Then you **stage** (a subset of) your changes to be committed.
Staging a change marks it to be committed by the next commit you perform.
**Staging area** (also called **index**) is a temporary buffer
that holds all the changes to be committed in the next commit.
Once you have staged all the changes you want to commit,
you perform the actual **commit**.
Any changes not staged will still remain outstanding in the local files
and you commit them in a separate commit (or revert them).

The easiest way to make a commit is to use an IDE (Eclipse, IDEA)
which let's you review the changes in a comprehensible way
and commit them.

Another option is `git gui`, a graphical tool provided by Git itself.
It lets you review the changes to be committed,
stage a subset of them and commit it (and also perform many more tasks).
Git GUI let's you even stage a subset of changes within an individual file,
which is a rather unique and sometimes quite handy feature.

You can also commit on the command line.

Show the overview of changed files

    git status

At this point Git says there are "_changes not staged for commit_" (for modified and deleted files)
or "_untracked files_" (for newly created files).

Show the details of changes within the files (modified lines)

    git diff

Stage a file named `test.txt` for commit

    git add -all test.txt

Show the overview of "_changes to be committed_"

    git status

Un-stage a file (the changes to the file will remain, they will just not be committed now)

    git reset HEAD test.txt

Once you have staged all the changes you want to commit now,
make the commit, passing in the commit message

    git commit -m "Add update customer."

You have created a new commit locally.
Now you need to **push** the commit to the remote repo
to backup your work and share it with other team members.

Provided you have setup the local branch as tracking its remote branch
(see above)

    git push

### 5.3 Writing commit messages

Find out what the rules are for the Git repo you are contributing to,
either explicitly or implicitly by looking at messages of other commits.

If you are not sure, consider using the following:

- Write a whole sentence (first letter upper-case, end with a dot)
  starting with a verb in infinitive.
- Use a verb describing the nature of the change,
  like _Add_, _Modify_, _Remove_, _Refactor_, etc.
- Be specific.
  Avoid general statements like "_Modify customer functionality._"
  and use e.g. "_Add validate customer name length._" instead.
- Do not go overboard.
  Remember the purpose of the commit message.
  It is not intended to detail every bit changed within the commit,
  it should rather summarize the meaning and purpose of the commit.
- Limit the length of the sentence.
  If there is more to say, make an empty row
  and continue with paragraphs below it.
- If the commit resolves an issue from an issue tracking system,
  include its number at the beginning of the message,
  usually preceded with a hash tag.

### 5.4 What and when to commit

Keep Git **history clean**.
Make each commit a meaningful unit.
A commit should have a **single purpose** and should resolve this purpose **completely**.

- Isolate non-related changes into different commits.
- Include the change as a whole into the commit.
  When you modify system's functionality also change the tests within that commit.
- Each commit should take the repo content from one consistent state to another.
  Tests should be passing after each commit.

**Backup** you work.
Make (and push) at least one commit each day
to prevent losing your whole day-worth work
and also to publish to other team members what you have done.

This may conflict with the completeness rule above.
In such a case, 
make the commit on a feature branch,
mark it explicitly as _WIP_ (work-in-progress)
and _amend_ it (see below) the next day into a new commit that is complete.

Given the general rules above,
you should always consider if the effort to fulfill them
is worth the benefit of having a clean Git history.

### 5.5 Preparing a merge request

Once you have made all the commits,
you want to merge your feature branch into `develop`.
Before you issue a request for your branch to be merged,
you should make sure it is based onto the current state of `develop`.

Imagine you have forked your feature branch and made your commits `A`, `B` and `C`

    K--L  develop
        \
         A--B--C  feature/update-customer

In the meantime however, someone else have advanced `develop` with other new commits `M`, `N`

    K--L--M--N  develop
        \
         A--B--C  feature/update-customer

Now you need to **rebase** your commits on top of current `develop`.
This means that instead of your original commits `A`, `B`, `C` based on commit `L`
you need to have new commits, let's call them `A1`, `B1`, `C1`,
that will be based on commit `N`.

    K--L--M--N  develop
       |      \
       |       A1--B1--C1  feature/update-customer
        \
         A--B--C

The original commits `A`, `B`, `C` will remain in your local repo after the rebase,
but as no branch will be pointing to them, they will not be visible.

The contents of the new commits `A1`, `B1`, `C1` will be similar to that of the original ones,
but depending on what has been changed in commits `M`, `N` may not be exactly the same.

A decent IDE (IDEA, Eclipse) has a comprehensive and usable functionality to do the rebase
easily in a graphical environment.

To rebase on the command line, follow along.

Make sure you have no uncommitted changes

    git status

Fetch new changes from the remote
and fast-forward local `develop` (assuming advancing `develop` is always fast-forward)

    git fetch --prune
    git checkout develop
    git merge origin/develop

Return back to the feature branch

    git checkout feature/update-customer

Initiate the rebase

    git rebase develop

Git tries to create the `A1`, `B1`, `C1` commits automatically for you.
Many times it succeeds and the rebase completes immediately.
When there are conflicting changes that Git cannot resolve by itself however,
Git will announce the failure and asks you to resolve them manually.
If you see something like

    ...
    CONFLICT (content): Merge conflict in test.txt
    Failed to merge in the changes.
    ...

you know the automatic rebase failed and you have to resolve the conflicts yourself.

To get overview of rebase progress

    git status

You will see an "_Unmerged paths:_" section listing the files with conflicts.

Resolve all files one by one by editing them in your editor.
Each one contains sections highlighting the conflicts like this

    ...some previous lines...
    <<<<<<< HEAD
    Here go the lines from the develop branch...
    =======
    Here go the lines from your branch...
    >>>>>>> Add update customer.
    ...some following lines...

Edit each such conflict section, remove the marker lines
and merge the lines from both branches
so that the resulting contents of the file is what you want to be there after the rebase.

    ...some previous lines...
    Here are the lines from the develop branch with my changes merged in.
    ...some following lines...

Then add each manually merged file to the staging area

    git add --all test.txt

After you have merged and staged all conflicting files, show the overview again

    git status

You should see "_all conflicts fixed_" message displayed.
Now you can continue with the rebase

    git rebase --continue

Depending on the nature of conflicts
and the particular commits made on your feature branch and on `develop`
you may have to resolve conflicts repeatedly.
This is ok, it's just the way it works.

Finally, when the rebase has successfully completed, push your changes upstream

    git push

Now your feature branch is ready to be merged to `develop`.

### 5.6 Modifying commits

#### 5.6.1 Amending last commit

After you have made a commit, you might want to modify it.
Maybe you want to add more changes, fix some issues in the files you have committed,
finalize a previous work-in-progress commit or just re-phrase the commit message.

Stage all changes you want to be part of the last commit.

Create a new commit by **amending** the last one

    git commit --amend -m "Amended commit message."

You can also use `git gui` and check the "_Amend last commit_" option.

If you have had already pushed the original commit,
you are now left with commit history like this

    A--B--C  remotes/origin/feature/update-customer
        \
         C1  feature/update-customer

`C` is the original commit (before amend)
and `C1` is the amended commit.
The local feature branch points to the amended commit `C1`,
but the remote branch still points to the original `C` commit.

When you now run

    git status

you will see something like

    # On branch feature/update-customer
    # Your branch and 'origin/feature/update-customer' have diverged,
    # and have 1 and 1 different commit each, respectively.

An attempt to push your feature branch

    git push

will now be rejected as "_non-fast-forward_"

     ! [rejected]   feature/update-customer -> feature/update-customer (non-fast-forward)
    error: failed to push some refs to ...remote repo URL...
    hint: Updates were rejected because the tip of your current branch is behind
    hint: its remote counterpart. ...

To push your modified commits, you have to **force** the push.

#### 5.6.2 Force-pushing modified commits

Any non-fast-forward changes in you local feature branch commit history
are by default rejected on push.
This is a security feature to prevent modifying commit history in remote repo
by accident.

A remote repo is usually shared by multiple team members
and others may have already based their work on the remote commits.
When you modify them you effectively invalidate all such derived commits
and force their authors to rebase their work later.

Be sure, therefore, that you really want to change history of a remote branch.

To **force-push** a feature branch with modified commits

    git push --force origin feature/update-customer

Always specify the remote and the branch to be force-pushed explicitly
to prevent any mishaps.

Force-push re-assigns the remote branch to the same commit as the tracking local branch.
If the original commit is not pointed to by any other branch,
it becomes invisible.

#### 5.6.3 Squashing multiple commits into one

While preparing a merge request you might re-consider your commits.
The most common case is when you want to **squash** multiple commits into one.
You can achieve this by doing the rebase of the feature branch on top of `develop` in an **interactive** mode.

You can perform interactive rebase in an IDE (e.g. IDEA).

Alternatively, you can also do it on the command line.

Start as in _Preparing a merge request_ above,
but initiate the rebase in the interactive mode

    git rebase --interactive develop

A _vi_ editor is opened and the commits in the feature branch are listed

    pick cb3d8b0 Add update customer.
    pick 4fbddf4 Add validate customer name in update customer.
    pick 46dffb4 Add validate customer phone in update customer.

Each line presents a commit (with a hash and commit message)
preceded by a command.
The command is by default pre-filled as `pick` which means
_keep the commit as is_.

You can then modify commands before the individual commits
to specify the actions Git should take.
One of the commands is `squash` (can be abbreviated as `s`)
which means _squash the commit to the previous one_.

Let's say you want to squash the last two of the three commits above.
You modify the last command from `pick` to `s` (= `squash`)

    pick cb3d8b0 Add update customer.
    pick 4fbddf4 Add validate customer name in update customer.
    s 46dffb4 Add validate customer phone in update customer.

(Press <kbd>i</kbd> to start editing,
delete `pick` and type <kbd>s</kbd> instead,
press <kbd>ESC</kbd> to stop editing
and type <kbd>ZZ</kbd> to save changes.)

Now you have to prepare the commit message for the new squashed commit.

    # This is a combination of 2 commits.
    # The first commit's message is:
    
    Add validate customer name in update customer.
    
    # This is the 2nd commit message:
    
    Add validate customer phone in update customer.
    
    # Please enter the commit message for your changes. ...

(Scroll to both lines and press <kbd>dd</kbd> to delete each.
Press <kbd>i</kbd> to start editing,
type in the new commit message,
press <kbd>ESC</kbd> to stop editing
and type <kbd>ZZ</kbd> to save changes.)

    # This is a combination of 2 commits.
    
    Add validate customer in update.
    
    # Please enter the commit message for your changes. ...

The two commits have now been squashed into one.
Now you have to force-push the branch (see above) because you have modified the commits.

### 5.7 (Resets)

TODO

## 6. Managing Git repo

### 6.1 Merging for linear history

Keep Git history **linear**, never merge branches when more than one has its own commits.

    A--B--C--D  branch-1
        \
         K--L--M  branch-2

In the above example, never merge branch `branch-2` directly into `branch-1` like this:

    A--B--C--D---R  branch-1    DON'T DO THIS!
        \       /
         K--L--M

Commit `R` above merged two branches with parallel commits.
**DON'T DO THIS.**
Once you start down this path, you end up in a **merge hell**,
where branches split and merge in such a complicated pattern
that no-one will ever know which commit is included in which branch anymore.

Always **rebase** the other branch on top of the current branch before merging it in.

    A--B--C--D  branch-1
       |     |
       |     K1--L1--M1  branch-2
        \
         K--L--M

Once you rebase `branch-2` on top of `branch-1`,
you have two options how to proceed with the merge.

You can either do a straightforward merge (and delete `branch-2`):

    git merge branch-2

and end up with:

    A--B--C--D--K1--L1--M1  branch-1

Or you can do a "_no-fast-forward_" merge:

    git merge --no-ff branch-2

that will keep the original branching visible in the history:

    A--B--C--D------------R1  branch-1
              \          /
               K1--L1--M1

Commit `R1` message now says

    Merge branch 'branch-2' into 'branch-1'

and although it merges 2 branches, the history is still **linear**,
because there are no commits on `branch-1` in parallel to the 3 commits from `branch-2`
and all the commits form a single linear, unambiguous sequence of commits
`A`, `B`, `C`, `D`, `K1`, `L1`, `M1`, `R1`.

### 6.2 Repo layout

    O--O------O  master, v-1.2.3
        \    / \
         O--O   O--O--O---------O------O--O  develop
                      |\       / \    /|   \
                      | O--O--O   O--O |    O  feature/feature-3
                      |                 \
                      |                  O--O--O  feature/feature-1
                       \
                        O--O--O  feature/feature-2

#### Branch `master`

**Disable force-pushing** and **branch delete** on `master`.
This will make `master` a _stable_ branch,
once a commit is pushed there it can never be changed again.
Others may then safely base their work on any commit in `master`
and never be forced to change it in future due to changes in `master`.

On GitHub, for example, you can set `master` as one of the "_protected_" branches
in the project's Settings.

Keep `master` at **the last release** of your project.
Branch `master` should ideally always point to the current (last) release
(together with the last Git tag)
and so should always be advanced when a new release is created.

#### Branch `develop`

Create a `develop` branch to hold the current development status.
Branch `develop` will therefore contain changes introduced after the last release
(i.e. unreleased changes).

**Disable force-pushing** and **branch delete** on `develop` as well.

#### Feature branches

Mark feature branches with `feature/` namespace
so that they are easily distinguishable.

### 6.3 Merging features into `develop`

Before merging a feature branch into `develop`:
- Always require the feature branch commit history to be linear.
- Always require the feature branch to be rebased on top of current `develop`,
  or do the rebase yourself, to keep history of `develop` linear.
- Merge feature branches using "_no-fast-forward_" mode (see above)
  to preserve branching information in the Git history.

### 6.4 Releasing

- Perform releases either directly on `develop`
or on a specific `release/` branch.
- Create a tag for the release version.
- If you used a release branch, merge it into `develop`.
- Advance `master` onto the release tag.
