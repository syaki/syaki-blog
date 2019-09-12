---
title: How to use Xi-editor?
date: 2019-05-08 14:38:05
---

{% asset_img xi-mac.png xi-mac %}

# What is Xi-editor?

**The xi-editor project is an attempt to build a high quality text editor, using modern software engineering techniques.**

# Install it on macOS

## Requirements

* Xcode 10.2

* Rust

## Installing

### Clone the repository:

```bash
git clone --recurse-submodules https://github.com/xi-editor/xi-mac
cd xi-mac
```

### Build and Open:

```bash
xcodebuild
```

之后会在`XiEditor.app`文件会在`./build/Release/`文件夹下。

### Move to Applications Folder:

将其拖入Application文件夹。

```bash
cp -r Build/Release/XiEditor.app /Applications
```

# CLI

## installing

1. Open XiEditor

2. XiEditor > Install Command Line Tool

# Install theme

## Download theme file

[dracula for sublime](https://github.com/dracula/sublime/archive/master.zip)

## Move to :

找到`/Users/username/Library/Application\ Support/XiEditor/themes/`

将`Dracula.tmTheme`拖入

## Change theme

A theme can be selected from the Debug > Theme menu. There is not yet a mechanism for including custom themes.

# Configuration

the general preferences are located at ~/Library/Application Support/XiEditor/preferences.xiconfig. This file can be opened from File > Preferences (⌘ + ,).

# Install plugin

## Download plugin

[Word Count Plugin](https://github.com/scholtzan/xi-word-count.git) for Xi-editor

## Installation

将插件解压后拖入`/Users/huixie/Library/Application\ Support/XiEditor/plugins/`

将之前下载的xi-mac文件夹也拖入其中

进入插件的文件夹

```bash
make install
```

之后打开 Xi-editor，Debug > Plugin 启用

{% asset_img word_count_plugin.png word_count_plugin %}

## Other plugin

Search [xi-editor plugin](https://github.com/search?q=xi-editor+plugin) in Github.
