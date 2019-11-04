---
title: How to debug rust
date: 2019-11-04 12:58:31
tags: Rust
---

# Windows

## Install `msys2`

Run `mingw64.exe`

-   edit source

    `msys64/etc/pacman.d/`

    ```
    mirrorlist.mingw32
    Server = http://mirrors.ustc.edu.cn/msys2/mingw/i686

    mirrorlist.mingw64
    Server = http://mirrors.ustc.edu.cn/msys2/mingw/x86_64

    mirrorlist.msys
    Server = http://mirrors.ustc.edu.cn/msys2/msys/$arch
    ```

-   run command

    ```sh
    pacman -S --noconfirm base-devel mingw64-x86_64-toolchain git
    ```

## Add `cargo` config

edit `{PATH}/.cargo/config`

add this:

```
[target.x86_64-pc-windows-gnu]
linker = "C:\\msys64\\mingw64\\bin\\gcc.exe"
ar = "C:\\msys2\\mingw64\\bin\\ar.exe"
```

## Add `PATH` environmental variable

`C:\msys64\usr\bin`

`C:\msys64\mingw64\bin`

## Install rust

use `stable-gnu`

```sh
rustup install stable-gnu
rustup default stable-gnu
```

## Install `CLion`

-   install `intellij-rust` and `toml` plugin

-   set toolchain

    `File -> Settings -> Build, Execution, Deployment -> Toolchains` add `mingw` toolchain

# macOS

Install `Command Line Tools` or `xcode`.

Install `rust`, `clion` and `intellij-rust` plugin.
