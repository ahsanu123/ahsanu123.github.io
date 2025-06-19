---
date: 2025-06-19
title: Setup Basic Qt6 Development Environment with QML LSP in Arch Linux
tags: qt6, qml, arch-linux, qt6-lsp, qml-language-server, qt6-setup, neovim, lazyvim, qt6-qml, linux-development, cplusplus, lsp, nvim, qt-tutorial, embedded-linux
---

![image](https://github.com/user-attachments/assets/f66edddb-fb2f-49e4-8ef5-f017d03f9410)

# Install Qt6 in Arch Linux

> [!NOTE]
> this small tutorial assume you has install base-devel, cmake, and ninja

first you need an sudo access, to install basic development package you need install following package

```shell

$ pacman -Ss qt6- | rg -i installed

extra/qt6-base 6.9.1-1 (qt6) [installed]
extra/qt6-declarative 6.9.1-1 (qt6) [installed]
extra/qt6-languageserver 6.9.1-1 (qt6) [installed]
extra/qt6-svg 6.9.1-1 (qt6) [installed]
extra/qt6-translations 6.9.1-1 (qt6) [installed]

```

- run `sudo pacman -S qt6-base qt6-declarative qt6-languageserver qt6-svg qt6-translations --noconfirm`
- wait until complete
- next, to check if qt6 installed, we will compile basic qt example
- run `git clone https://github.com/qt/qtdoc.git`
- then cd to qtdoc example coffe, `cd qtdoc/examples/demos/coffee`
- run `cmake -S . -B build`, you can change `build` to another name (its build output)
- build example with `cmake --build build`
- next you can run the example with `./build/coffeemachine`
- if all working, you will see this

# Setup LSP with Neovim (LazyVim config)

- next to setup lsp we need to check installed `qmlls` in our `/usr/bin/qmlls6` (the name may difference based on version)
- you need change `cmd` argument from `nvim-lspconfig` based on your installed `qmlls` version inside `init.lua`

```shell

 File: init.lua

   1   │ -- bootstrap lazy.nvim, LazyVim and your plugins
   2   │ require("config.lazy")
   3 + │ 
   4 + │ vim.lsp.enable("qmlls")
   5 + │ vim.lsp.config("qmlls", {
   6 + │   cmd = { "qmlls6" },
   7 + │ })

```

- then hopefully lsp will work :)

![image](https://github.com/user-attachments/assets/6059c854-a685-4bde-bf8c-45b85a6c7f52)
