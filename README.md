bin-linux
=========

`bin-linux` is a collection of Bash scripts that should be on all Linux machines. This repo is often deployed in conjuction with [my dotfiles](https://github.com/DavidVogelxyz/dotfiles).

Usage
-----

Within the root of this project (`bin-linux`) is a directory named `bin-linux`. Create a symlink at `${HOME}/.local/bin/bin-linux` that points to the *subdirectory* named `bin-linux`.

Table of contents
-----------------

- [Usage](#usage)
- [Scripts](#scripts)
    - [ansible_encrypt](#ansible_encrypt)
    - [bip-39-passwords](#bip-39-passwords)
    - [bridged_networking](#bridged_networking)
    - [daily-note](#daily-note)
    - [dev-tmux](#dev-tmux)
    - [dotfile-deploy](#dotfile-deploy)
    - [git_compare_dates](#git_compare_dates)
    - [git_package_manager](#git_package_manager)
    - [gpg_encrypt_file](#gpg_encrypt_file)
    - [openssl-password-hash](#openssl-password-hash)
    - [show-ssh-agent](#show-ssh-agent)

Scripts
-------

### ansible_encrypt

Date created: 2025 Oct 22, Wed

`ansible_encrypt` was written to solve an issue with `ansible-vault` and newlines.

`ansible-vault` is an Ansible utility that allows a user to encrypt a string or file. When encrypting a string via the command line, there are different ways to terminate the program -- one way adds a newline to the string, and the other does not. When encrypting a file, `ansible-vault` appears to always add the newline, which can cause issues depending on what's being encrypted.

`ansible_encrypt` fixes this by enabling file encryption that does **NOT** append the newline. The script also provides the user a method to provide a "static salt", such that the user can re-encrypt the same file and obtain the same hash. This enables a user to encrypt a file to obtain the hash, and then decrypt the same file in order to guarantee that the output is what's expected. Then, the file can be encrypted again -- if the hashes agree, then the user has confirmation that Ansible will deploy the correct contents.

### bip-39-passwords

Date created: 2025 May 14, Wed

`bip-39-passwords` was written in order to create a utility that could create simple but effective passwords.

While there are many ways to [create random passwords on a Linux machine](), those methods create passwords that are relatively difficult for a human to remember and to type. In contrast, `bip-39-passwords` uses the Bitcoin seed word list to create variable-length passwords that can be extremely complex for a computer to crack, while also retaining a form of simplicity for the end user.

### bridged_networking

Date created: 2025 Dec 6, Sat

`bridged_networking` was written in order to solve an unique problem for systems with multiple Ethernet interfaces.

This script is useful in a very specific context: a machine that is a QEMU hypervisor and also has multiple Ethernet interfaces. In such a situation, a bridged network configuration may be employed so that the VMs are assigned their own IP addresses. However, if the machine has multiple Ethernet interfaces (`eth0` and `eth1`), a race condition may occur where the "wrong" interface is assigned to the interface that hosts the bridge.

`bridged_networking` is useful after boot, when the machine doesn't have a network connection due to this situation. It will loop through the interfaces, figure out which interface is connected to the network, and will restart the bridge on that interface.

### daily-note

Date created: 2025 Jun 29, Sun

`daily-note` was written in order to simplify the process of creating daily note files within a notes repository. While the script has default configurations, those defaults can be overridden by a configuration file.

The purpose of this script was primarily to standardize the note file template, but also to expedite the creation process.

A useful feature of this script is the ability to provide a "relative date" as an argument (ex. `daily-note "tomorrow"`), which correctly creates a new note file for whatever date is provided.

### dev-tmux

Date created: 2025 Sep 7, Sun

`dev-tmux` was written in order to simplify the process of starting a `tmux` "experience". While the script has default configurations, those defaults can be overridden by a configuration file.

Often, when starting `tmux` on any given machine, there are a set of sessions that should be started. Whether it's different Git repos, different directories, etc, each machine has a unique set of sessions that will be opened over the course of a work session. `dev-tmux` makes it easy for each machine to have a list of `tmux` sessions that should be available when `tmux` starts, and `dev-tmux` facilitates this.

In addition, `dev-tmux` assists with preserving the order of these sessions (which can be useful when switching sessions using the number keys). While `tmux-sessionizer` now has the ability to fuzzy find through open sessions (making switching even easier), `dev-tmux` helps to standardize session deployment. `dev-tmux` also allows for any session from the list to be specified, such that the user is placed into that session once all sessions have been started.

### dotfile-deploy

Date created: 2025 Oct 17, Fri

`dotfile-deploy` was written to simplify and standardize the deployment of configuration related Git repositories across machines. While the script has default configurations, those defaults can be overridden by a configuration file.

This script is relatively simple -- it takes a list of Git URLs, along with the "clone path", "symlink source", and "symlink destination", and deploys them on a mchine. If the state of the machine is identical to what's specified by the list, nothing happens. If the machine doesn't already have directories or symlinks at those paths, it creates them. If a directory or symlink already exists at that path, but it's not "correct", the script will back up what's there and replace it according to the contents of the list.

### git_compare_dates

Date created: 2025 Sep 8, Mon

`git_compare_dates` was written in order to simplify the process of checking author dates against commit dates.

As an example, if a set of commits is squashed during development, the unified commit will have the author date of the "first commit" (the commit into which all others are squashed). However, the commit date of that squashed commit will be the timestamp when the squash occurred. This causes a discrepancy that different platforms (GitHub vs GitLab, etc) will handle different -- some platforms will refer to the author date, while other refer to the commit date.

`git_compare_dates` makes it easy to see for which commits this is the case, and makes it easy to identify the commit from which to start an interactive rebase. If done correctly (`git rebase --committer-date-is-author-date -i`), the interactive rebase will "fix" this. Then, `git_compare_dates` can be run again to confirm that the two dates agree.

### git_package_manager

Date created: 2025 Sep 14, Sun

`git_package_manager` was written in order to make it easy to manage multiple Git repositories that are being used actively on a system.

On a machine where Git repos are all stored in the same directory (ex. `~/.local/src`), `git_package_manager` can quickly iterate through the repos in that directory, and fetch and pull any updates. This script can be modified to point to a "development directory"; however, it's best suited for a directory where repo changes are rarely made.

As an example, if `~/.local/src` is being utilized to store a bunch of repos with scripts in them, then one call to `git_package_manager` can facilitate the update of many repos, ensuring that the scripts within are always up-to-date.

### gpg_encrypt_file

Date created: 2026 Jan 9, Fri

`gpg_encrypt_file` was written in order simplify the process of encrypting a file with GPG.

The purpose of this script to make encrypting a file using GPG more user-friendly. `gpg_encrypt_file` leverages `fzf`, a fuzzy finder, to simplify selecting both the GPG recipient and the file to encrypt.

### openssl-password-hash

Date created: 2025 Jul 13, Sun

`openssl-password-hash` was written in order to simplify the process of creating the password hash that would be included in the `/etc/shadow` file.

The purpose of this script is to facilitate password hashing for the purposes of updating password via an Ansible playbook.

### show-ssh-agent

Date created: 2025 Sep 27, Sat

`show-ssh-agent` was written in order to facilitate "connecting to an already existing `ssh-agent` socket" after establishing a `ssh` connection. An important note is that this script is intended for use with `tmux`, and especially with `tmux-sessionizer`.

To explain the use case, imagine a machine (`my-hostname`). A user logs in, and starts adding keys to the `ssh-agent`. After a bit, the user may want to open `tmux` with `dev-tmux`, or may use `tmux-sessionizer` to open new sessions. Regardless, if the `ssh-agent` socket was opened with the initial login shell, all of those `tmux` sessions will have access to the same `ssh-agent` socket (and, therefore, the same SSH keys).

Now, if the user attempts to login to `my-hostname` through a `ssh` connection, the `ssh` session will likely create its own `ssh-agent` socket (or, none at all), and will not have access to SSH keys that were already added on `my-hostname`. While the user *can* attach to one of the already running `tmux` sessions and have access to the same `ssh-agent` socket, any `tmux` session created through the `ssh` connection will ***not*** connect to the agent.

`show-ssh-agent` fixes this by enabling access to the original `ssh-agent` socket, such that all new `tmux` sessions created through the `ssh` connection connect properly to the original socket. To do this:

1. Connect via `ssh`.
2. Attach to a `tmux` session created locally on `my-hostname`.
3. Run `show-ssh-agent` to print the socket path.
4. Detach from that `tmux` session.
5. Outside of `tmux`, set `SSH_AUTH_SOCK` to the same socket path.
6. Reattach to `tmux`.
