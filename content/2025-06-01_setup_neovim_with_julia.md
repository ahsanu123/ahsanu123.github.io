---
date: 2025-06-01
title: Setup Julia in Neovim 
tags: linux, setup, julia
---

![image](https://github.com/user-attachments/assets/3905b050-fb44-4c8f-bef6-4d85c808ebcd)


# 🔬 Setup Julia in Neovim

> short guide about setup [Julia](https://julialang.org/)

This Week I try to setup Julia Development in Neovim in linux/wsl, I use [lazyvim](https://www.lazyvim.org/) (read the installation on lazyvim doc).

after initial setup in lazyvim, you need install julia-lsp with `Mason` then search `julia-lsp`
it will install julia lsp on your `~/.local/share/nvim/mason/packages/julia-lsp`

- install juliaup (i use yay, pacman not provide it) `yay -S juliaup --noconfirm`
- then install latest stable version with `juliaup add <version>`, to list version run `juliaup list`
- after install, make latest stable version as juliaup default with `juliaup default <version>`
- next go to julia folder `cd ~/.julia/environments/v1.11/`
- it may contain `mainfest.toml and project.toml`, if not, try to create new project then add package in this folder  (as shown below)

```shell

[deps]
GLMakie = "e9467ef8-e4e7-5192-8a1a-b1aee30e663a"
Makie = "ee78f7c6-11fb-53f2-987a-cfe4a2b5a57a"
PackageCompiler = "9b87118b-4619-50d2-8e1e-99f35a4d4d9d"

```


- next run julia with `julia` then enter Pkg with `]`
- active current env with `activate .`
- and run `precompile`
- it will compile all needed package for your project
- then try to create or open project outside of `.julia`
- when you first open julia project it may run slow / timeout, because the server itself is not compiled yet, julia will compile (based on your system it may slow or fast), then after first compile it able to indexing your project
- in neovim you can see lsp log with `LspLog`

```shell

[ERROR][2025-06-01 19:42:29] ...p/_transport.lua:36	"rpc"	"/home/ah/.local/share/nvim/mason/bin/julia-lsp"	"stderr"	"[ Info: Starting LS with Julia 1.11.5\n"
[ERROR][2025-06-01 19:42:30] ...p/_transport.lua:36	"rpc"	"/home/ah/.local/share/nvim/mason/bin/julia-lsp"	"stderr"	"  Activating project at `~/.local/share/nvim/mason/packages/julia-lsp/libexec/extension/scripts/environments/languageserver/v1.11`\n"
[ERROR][2025-06-01 19:42:30] ...p/_transport.lua:36	"rpc"	"/home/ah/.local/share/nvim/mason/bin/julia-lsp"	"stderr"	"[ Info: Starting the Julia Language Server\n"
```



- if first trigger compiling not work from neovim (by opening julia file), try to trigger by run the server from mason folder `~/.local/share/nvim/mason/bin ` then `./julia-lsp ~/path/to/external/julia/project`




