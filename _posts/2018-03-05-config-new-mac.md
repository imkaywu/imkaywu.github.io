---
title: Configure a new Mac
---

## Install Xcode

Once the Xcode is installed, open a terminal, run **xcode-select --install**, and click the install button to install the required command line developer tools. If a message tells you that the software cannot be installed because it is not currently available from the Software Update Server. This usually means you already have the latest version installed.

This will install git command line.

## Install MacPort

Visit [https://www.macports.org/install.php](https://www.macports.org/install.php).

> I've switched to HomeBrew

## Install HomeBrew

Download and install [HomeBrew](https://brew.sh/)

## Config Git

```
git config --global user.name "YourName"
git config --global user.email "YourEmail"
```

## Configure Vim

- Download my .vimrc from [dotfiles](https://github.com/imkaywu/dotfiles/blob/main/vimrc).
- Install [powerline fonts](https://github.com/powerline/fonts) so that
  special character in airline can be displayed properly
- Install ctags for generating tags using GutenTag
- Install ripgrep for fuzzy search in FZF

## Configure Tmux

- Download my .tmux.conf file from [dotfiles](https://github.com/imkaywu/dotfiles/blob/main/tmux.conf)

## Other apps

- [ReadKit](https://apps.apple.com/us/app/readkit-read-later-rss/id1615798039): RSS reader
- [MarkMark](https://apps.apple.com/us/app/markmark/id6475077023): mark interest webpages
- [Battery Monitor: Health, Info](https://apps.apple.com/us/app/battery-monitor-health-info/id836505650)
- [Aseprite]()
- [Godot]()
