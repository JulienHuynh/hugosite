+++
title = 'Github pages & Hugo'
date = 2023-11-17T22:03:14+01:00
draft = false
featured_image= '/hugosite/images/github-logo.jpg'
tags = ["blog", "hugo", "github-pages", "tutorial"]
categories = ["Développement Web", "GitHub-Pages"]
author = 'Julien Huynh'
+++

## Résumé tutoriel du cours Github pages & Hugo
![Github logo](/hugosite/images/github-logo.jpg)

### 1) Installer Git et Hugo

Installation de Git : https://git-scm.com/downloads 

Installation de Hugo : https://gohugo.io/getting-started/installing/

Installation rapide pour macOS :
```
brew install hugo
```

### 2) Créer un nouveau site Hugo

```
hugo new site hugositename
cd hugositename
git init
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
echo "theme = 'ananke'" >> hugo.toml
hugo server
```

### 3) Créer un nouveau post

```
hugo new posts/mon-blog.md
```

### 4) Build et déployer sur Github pages

1. Créer un nouveau repo sur GitHub et push le projet Hugo dedans.

2. On crée une branche qui contiendra le code source et une autre qui contiendra le code build contenu dans le dossier public.

3. En allant dans « Settings » puis « pages » sur la page du repo :
On choisis la source qui correspondra au code build de notre branche public puis on déploie l’application avec Github pages.


Cette configuration nécessite de build l’application et de push les modifications à chaque fois sur cette branche à l’aide de la commande :
```
hugo -D
```

Pour automatiser cela, nous allons utiliser qu’une seule branche, GitHub actions va s’occuper de build le projet automatiquement grâce à un workflow GitHub, un fichier yaml qui contient les indications à suivre.

Voici le path à respecter :
.github/workflows/hugo.yaml

Le contenu du fichier yaml :
```
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
# Runs on pushes targeting the default branch
push:
branches:
- master

# Allows you to run this workflow manually from the Actions tab
workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
contents: read
pages: write
id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
group: "pages"
cancel-in-progress: false

# Default to bash
defaults:
run:
shell: bash

jobs:
# Build job
build:
runs-on: ubuntu-latest
env:
HUGO_VERSION: 0.120.2
steps:
- name: Install Hugo CLI
run: |
wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
&& sudo dpkg -i ${{ runner.temp }}/hugo.deb
- name: Install Dart Sass
run: sudo snap install dart-sass
- name: Checkout
uses: actions/checkout@v4
with:
submodules: recursive
fetch-depth: 0
- name: Setup Pages
id: pages
uses: actions/configure-pages@v3
- name: Install Node.js dependencies
run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
- name: Build with Hugo
env:
# For maximum backward compatibility with Hugo modules
HUGO_ENVIRONMENT: production
HUGO_ENV: production
run: |
hugo \
--gc \
--minify \
--baseURL "${{ steps.pages.outputs.base_url }}/"
- name: Upload artifact
uses: actions/upload-pages-artifact@v2
with:
path: ./public

# Deployment job
deploy:
environment:
name: github-pages
url: ${{ steps.deployment.outputs.page_url }}
runs-on: ubuntu-latest
needs: build
steps:
- name: Deploy to GitHub Pages
id: deployment
uses: actions/deploy-pages@v2
```

Nous retournons dans «Settings» puis «pages», et nous remplaçons la source par Github Actions

> Surcharger un thème
>
>> Nous avons installé un thème avec lequel son architecture devra être copié afin de pouvoir surcharger et donc remplacer du contenu.

