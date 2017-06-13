# Browsing git repo

## Installing git

### Linux

Redhat-based systems:

    sudo yum install git gitk git-gui

Debian-based systems:

    sudo apt-get install git gitk git-gui

Verify git is installed:

    git --version

You should see something like `git version 1.8.3.1`.

### Windows

Go to [https://git-for-windows.github.io/](https://git-for-windows.github.io/),
download and run the installer.

Verify git is installed:

    git --version

You should see something like `git version 1.8.3.1`.

## Creating an SSH key pair

Git repos are accessed via SSH protocol.
You first have to generate an SSH key pair (public and private key)
to be able to use a remote git repo.

Follow the instructions
[here](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)
to generate the key pair.
The instructions are specifically for GitHub,
but the generation of the keys is generic.

## Accessing git repo

