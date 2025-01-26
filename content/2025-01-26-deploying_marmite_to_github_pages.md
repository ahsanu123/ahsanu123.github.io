---
date: 2025-01-26
title: Deploying Marmite Site To Github Pages With Github Action
tags: linux, howto, deployment
---
# 💤 Introduction

[on marmite documentation page](https://rochacbruno.github.io/marmite/hosting.html), the author already  explain how to 
deploy generated marmite's site into github pages, but the guide is 
using `cargo` to install marmite, which mean cargo will compile it 
with github action, its ok, but taking longer than method we will using.


## 🐜 Steps 

this github action file is copied from the jekyll action. basically we will  use 
compiled release from Marmite [github repo](https://github.com/rochacbruno/marmite/releases/tag/0.2.3) 
we download it use `wget`, then extract it with `tar`, then running to build our site, and done!!.

- first make folder `.github/workflows` inside your Marmite root folder, `mkdir -p .github/workflows`
- change directory into new created folder, `cd .github/workflows`
- then make yaml file to describe our github action, `touch main.yml`
- open with your favorite text editor(I  will use vim), `vim main.yml`
- paste code below

```yaml
name: Marmite Github Page Deployment Action

on:
  push:
    branches: [main]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout 🛎
        uses: actions/checkout@v4

      - name: Download Marmite from Marmite Repos
        run: wget https://github.com/rochacbruno/marmite/releases/download/0.2.3/marmite-0.2.3-x86_64-unknown-linux-gnu.tar.gz

      - name: Extract Marmite with tar
        run: tar -xf marmite-0.2.3-x86_64-unknown-linux-gnu.tar.gz

      - name: Build site 🏗️
        run: ./marmite . site --debug

      - name: Setup Pages
        uses: actions/configure-pages@v5

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: 'site'

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```
- as time of writing latest marmite version was `0.2.3`, feel free to change into latest version if there are available.

- save `main.yml` file
- then push into your remote main branch
- then check if your command running on action tab inside your github repository.

## 🍃 References 

- [Jekyll Github Action](https://github.com/actions/jekyll-build-pages)
- [Official Marmite Hosting Guide](https://rochacbruno.github.io/marmite/hosting.html)
