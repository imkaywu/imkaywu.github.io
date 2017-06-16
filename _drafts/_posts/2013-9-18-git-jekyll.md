---
title: Installation and configuration of Git and Jekyll
categories:
  - Dev
tags:
  - Jekyll
  - Git
---

This blog goes through the topics of installing Git and Jekyll, structure of GitPage. Jekyll is the static web engine I use for my blog. The following is test on Fedora 20.

## Git

### Git Installation

```bash
sudo yum install git
```

### Git Configuration

Git User Name:

```bash
git config --global user.name "YourName"
```

Git Email Address:

```bash
git config --global user.email "YourEmail"
```

It is recommended to use an SSH connection when interacting with Github. The detail can be found in [this post](https://help.github.com/articles/generating-ssh-keys).

### Check for SSH keys

First check if existing SSH keys are on your computer.

```bash
$ cd ~/.ssh
$ ls -al
```

Check the directory listing to see if there are files named `id_rsa.pub` and `id_dsa.pub`. If you don't have eithor of these files go to **Step 2**. Otherwise, go to **Step 3**.

### Generate a new SSH key

When you're asked to "enter a file in which to save the key", the default settings are preferred.

```bash
$ ssh-keygen -t rsa -C "your_email@example.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/you/.ssh/id_rsa)
```

Next, enter a passphrase. See [Working with SSH key passphrases](https://help.github.com/articles/working-with-ssh-key-passphrases) for good and secure passphrase.

```bash
Enter passphrase (empty for no passphrase): [Type a passphrase]
Enter same passphrase again: [Type passphrase again]
```

which should give you something like this.

```bash
Your identification has been saved in /home/you/.ssh/id_rsa.
Your public key has been saved in /home/you/.ssh/id_rsa.pub.
The key fingerprint is:
01:0f:f4:3b:ca:85:d6:17:a1:7d:f0:68:9d:f0:a2:db your_email@example.com
```

Then add your new key to the ssh-agent.

```bash
$ eval `ssh-agent -s`
Agent pid [some number]
$ ssh-add ~/.ssh/id_rsa
```

### Add you SSH key to GitHub

Copy the content of the file `~/.ssh/id_rsa.pub` and add it to GitHub.

```bash
Account settings => SSH Keys => Add SSH key => Descriptive label and key => Add key
```

### Test

Try SSHing to GitHub. You'll be asked to enter the passphrase to authenticate this action.

```bash
$ ssh - T git@github.com
```

It's possible to see this error.

```bash
...
Agent admitted failure to sign using the key
debug1:No more authentication methods to try
Permission denied(publickey)
```

This is a known problem with certain Linux distribution. See [this article](https://help.github.com/articles/error-agent-admitted-failure-to-sign) for possible resolution.

You may see this warning.

```bash
The authenticity of host 'github.com (207.97.227.239)' can't be established.
RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
Are you sure you want to continue connecting (yes/no)?
```

**This is supposed to happen.** Verify that the fingerprint in your terminal matches the one we've provided up above, and then type "yes".

```bash
Hi [username]! You've successfully authenticated, but GitHub does not provide shell access.
```

## Jekyll

The best way to install Jekyll is using this command `gem install jekyll` as it'll automatically install all related software for you. In order to do that, Ruby environment and rubygems are required.

### Ruby

```bash
$ sudo yum install ruby-devel
```

But this command may not install the latest version, so I recommend installing from source code. Remember to have `gcc` pre-installed.

### Rubygems

```bash
$ sudo yum install rubygems
$ gem update --system    # may need sudo, update to latest version
```

### Jekyll

```bash
$ gem install jekyll    # using sudo can have a different effect
```

I encountered an issue of `LoadError` caused by `JSON` or `JSON-pure` and a `Could not find a JavaScript runtime. (ExecJS::RuntimeUnavailable)` error caused by Nodejs.

When you install jekyll with or without `sudo`, you get a totally different list of local gems, as can be shown using `[sudo] gem list -all`.

## Structure of Jekyll

### _config.yml

* replace `[YOUR DOMAIN]` with your website’s domain name after **url**, without a trailing slash  
* no need to surround a string with quotation marks unless it contains specific characters, like the colon `:` in the URL.

### _includes

* files shared between posts and pages.  
* Files and folders with underscores prepending the name will not be copied to the final, generated website.

### _layout

* Layout files for the posts.

### _posts

* Blog posts written using MarkDown.

### _site

* In the root directory of your project, enter `jekyll serve` or `jekyll build` in the Terminal, the `_site` directory will be created.
