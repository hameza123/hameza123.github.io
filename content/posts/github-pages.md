+++ 
draft = false
date = 2025-08-29T10:56:37+01:00
title = "Creating a Blog with GitHub Pages and Hugo"
description = "A step-by-step guide to creating a static blog using Hugo and deploying it to GitHub Pages"
slug = ""
authors = []
tags = ["hugo", "github-pages", "blog", "static-site", "tutorial"]
categories = ["web development"]
externalLink = "/posts/github-pages/"
series = []
+++

# Creating a Blog with GitHub Pages and Hugo

This markdown file includes:

1. **Front matter** with metadata (title, date, tags, etc.)
2. **Step-by-step instructions** with code blocks
3. **Proper formatting** with headers and lists
4. **Code examples** for different operating systems
5. **GitHub Actions workflow** for automatic deployment
6. **Conclusion** and additional resources

## Introduction

In this tutorial, I'll walk you through the process of creating a static blog using Hugo and deploying it to GitHub Pages. This combination provides a fast, free, and easy way to host your blog.

## Prerequisites

Before we begin, make sure you have:

- A GitHub account
- Git installed on your local machine
- Hugo extended installed 

## Step 1: Install Hugo

### On macOS (using Homebrew)
```bash
brew install hugo
```

### On On Windows (using Chocolatey)
```bash
choco install hugo
```

### On Linux
```bash
sudo apt-get install hugo
```

## Step 2: Create a New Hugo Site
```bash
hugo new site my-blog
cd my-blog
```

## Step 3: Add a Theme

Let's add a popular theme like Ananke:
```bash
git init
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
```
Add the theme to your config.toml:
```bash
theme = "ananke"
```


## Step 4: Create Your First Post
```bash
hugo new posts/my-first-post.md
```
This creates a new markdown file in content/posts/ with front matter:

```bash
---
title: "My First Post"
date: 2023-12-07T15:13:47Z
draft: true
---
```


## Step 5: Configure GitHub Pages

Create a new repository on GitHub named username.github.io (replace username with your GitHub username)

Initialize your Hugo site as a Git repository and connect it to GitHub:

```bash
git remote add origin https://github.com/username/username.github.io.git
git branch -M main
git push -u origin main
```

## Step 6: Deploy with GitHub Actions
Create a GitHub Actions workflow file at .github/workflows/gh-pages.yml:

```bash
name: GitHub Pages

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          submodules: true
          fetch-depth: 0

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

## Step 7: View Your Blog

Your blog will be available at https://username.github.io after the GitHub Action completes.


## Conclusion

You now have a fully functional blog hosted on GitHub Pages using Hugo! The setup might seem complex at first, but the benefits are worth it:

Free hosting with GitHub Pages

Blazing fast static site generation

Version control with Git

Easy content management with markdown

Happy blogging!


## Additional Resources:


Cloud Native Trainer with Mike : https://www.youtube.com/watch?v=zrmeOu8DYyw

