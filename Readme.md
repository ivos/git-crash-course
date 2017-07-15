# Git crash course

This is a practical, no-BS crash course of Git, the most-used version control system.

A version control system (VCS) manages a tree of files within a directory, versioning all changes to them,
so that the complete history is available:
once a version of a file is committed to a VCS it will never be lost and you can always get back to it.
This history kept by the VCS is called a repository (repo for short).

The course is organized by roles in which people might be using Git.
The chapters are ordered by increasing complexity and responsibility of the role in the Git workflow.

1. [Concepts](#1-concepts)
2. [Installation](#2-installation)
3. [Accessing Git repo](#3-accessing-git-repo)
4. [Browsing Git repo](#4-browsing-git-repo)
5. Contributing to Git repo
6. Managing Git repo
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
and contains the specification of changes to be made to the files
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
that is not yet integrated into develop with `feature`,
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
The usual read/write connection uses the SSH protocol,
read/only connections are usually defined with HTTP protocol.
You can use file protocol e.g. to keep a backup copy of the repo on a flash disk.

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
e.g. you can put a primary remote on Github (and name it `github`)
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

### 2.2 Create an SSH key pair

Git repos are often accessed via SSH protocol.
You first have to generate an SSH key pair (public and private key) to be able to use such a remote Git repo.

Follow
[the instructions here](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)
to generate the key pair.
The instructions are specifically for GitHub, but the generation of the keys is generic.


## 3. Accessing Git repo

A Git repo will be published on a **URL**.
It might provide a single URL (usually in SSH-form) or it might provide multiple URLs (usually HTTP and SSH).
The URL you choose will limit the operations you will be later able to perform with the repo.
Always **prefer SSH-form URL** over HTTP, as SSH usually allows read/write access,
while HTTP usually allows just a read-only access.

An SSH-form URL looks like `git@github.com:ivos/git-crash-course.git`,
while an HTTP-form URL looks like `https://github.com/ivos/git-crash-course.git`.

To use the SSH-form URL, you first have to **install your public key** on the server providing the Git repo.
Public Git services usually have a how-to guide
([Github how-to guide](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)).
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

List tags

    git tag

Switch to a tag

    git checkout my-tag

Review history of commits

    git log
