---
title: Failed to delete using Trash in vscode
date: 2018-06-18 09:31:23
tags:
- VS Code
- Linux
---

# Update on 2018-07-25

## Environment

OS: Arch linux
Desktop: i3 4.15.0.1
VScode version: 1.25.1

<!--more-->

## Problem

VScode base on electron, electron will call `gvfs-trash` to move file to trash.

If you type `gvfs-trash` in commandline , you may got this problem:

```sh
This tool has been deprecated, use 'gio trash' instead.
See 'gio help trash' for more info.
```

## Solve

add this to you environment:

```sh
export ELECTRON_TRASH=gio
```

## Refs

[Linux: Unable to move file to trash upon delete (#13189)](https://github.com/Microsoft/vscode/issues/13189)

--------------------------------------

# Old record

> Following method not work in i3

## Environment

Desktop :Gnome 3.28.2
VScode version: 1.24.1

## Problem

When I want to delete file from explorer in vscode, following Error occurs:

`Failed to delete using the Trash. Do you want to permanently delete instead?`

## Solve

Maybe `gvfs-trash` not work properly. It has been deprecated in this gnome version.
So open shell, install another tool for help.

```sh
## need python installed
sudo easy_install trash-cli
```

## Refs

[Failed to delete using Trash in Ubuntu 18.04 (#49675)](https://github.com/Microsoft/vscode/issues/49675)
[Moving files to trash always fails in Kubuntu 16.04 (#22820)](https://github.com/Microsoft/vscode/issues/22820#issuecomment-288239512)
