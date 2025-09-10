+++ 
draft = false
date = 2025-08-29T10:56:37+01:00
title = "Creating a Blog with GitHub Pages and Hugo Template"
description = "A step-by-step guide to creating a static blog using Hugo and deploying it to GitHub Pages"
slug = ""
authors = []
tags = ["hugo", "github-pages", "blog", "static-site", "tutorial"]
categories = ["web development"]
series = []
+++

# Creating a Blog with GitHub Pages and Hugo

This comprehensive tutorial exists to fill a gap. During my own journey of building a blog with Hugo and GitHub Pages, I discovered that most available guides were either outdated, fragmented, or skipped crucial steps.

This markdown file includes:

1. **introduction**  
2. **Prerequisites**  
3. **Steps**  
4. **Conclusion** 
4. **Additional Resources** 

## Introduction

In this tutorial, I'll walk you through the process of creating a static blog using Hugo and deploying it to GitHub Pages. This combination provides a fast, free, and easy way to host your blog.

## Prerequisites

Before we begin, make sure you have:

- A GitHub account
- Git installed on your local machine
- Hugo extended installed 

## Step 1: Install Hugo

[Hugo](https://themes.gohugo.io/) is a static site generator written in Go. It's the engine that will take your Markdown files and turn them into a full, fast website.

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

Verify Your Installation
```bash
hugo version
```

You should see output similar to this:

```bash
hugo v0.120.4+extended linux/amd64 BuildDate=unknown

```


## Step 2: Install Git
Git is the version control system you need to manage your code and push it to GitHub.

Choose the instructions for your operating system:

### On macOS (using Homebrew)
```bash
brew install git
git --version
```

### On Windows 
Download the installer: Go to the official [Git website](https://git-scm.com/download/win) 

Once installed, you can check if it worked by opening Command Prompt or PowerShell and typing:
```bash
git --version
```

### On Linux
The easiest way is to use the built-in package manager:
- Update your package list:
- Install Git:
- Verify the installation

```bash
sudo apt update
sudo apt install git
git --version
```

## Step 3: Create a New Hugo Site
Now that you have Hugo and Git installed, it's time to create the foundation of your blog.

1. The hugo new site Command

	Open your terminal (Command Prompt, PowerShell, or Git Bash on Windows; Terminal on macOS/Linux).
	Navigate to the directory where you want to create your blog project. For example, your Documents folder or a dedicated Projects folder.
	Once you're in the desired parent directory, run the following command:
	```bash
	hugo new site my-blog
	```

		- hugo: This calls the Hugo program.
		- new site: This is the command to create a new website framework.
		- my-blog: This is the name of your project folder. You can change this to anything you like (e.g., blog, my-hugo-site).

	What this command does:
	Hugo will create a new folder with the name you provided (my-awesome-blog) and generate all the necessary directories and configuration files inside it for a basic Hugo website.

2. Navigate into Your New Site
	After the command finishes, you need to move into your project's directory. Use the cd (change directory) command:
	```bash
	cd my-blog
	```
	You are now "inside" your Hugo project. Any commands you run from here will affect this project.

3. Understand the Generated Folder Structure

	Let's see what Hugo created. You can list the contents of the directory with:

	On Linux/macOS/PowerShell: ls

	On Command Prompt: dir

	You will see a structure like this:

	```bash
	my-awesome-blog/
	├── archetypes/
	├── content/
	├── data/
	├── layouts/
	├── static/
	├── themes/
	└── hugo.toml
	```

	Here’s what each folder and file is for:

	- archetypes/: Contains template files for your content. When you create a new post, Hugo uses the default.md archetype to pre-populate the front matter (the metadata at the top of the file).

	- content/: This is the most important folder. This is where you will write all your blog posts and pages. Each .md (Markdown) file in here becomes a page on your site. Posts are typically placed in content/posts/.

	- data/: Used to store additional data for your site (like JSON or YAML files) that you can use in templates.

	- layouts/: Contains HTML templates that override the templates from your theme. You don't need to touch this right away.

	- static/: Holds all static files like images, CSS, JavaScript, PDFs, etc. Anything you put here is copied directly to the root of your published site. For example, static/images/logo.png will be available at yoursite.com/images/logo.png.

	- themes/: This is where you will install your chosen Hugo theme (we'll do this in the next step).

	- config.toml: The main configuration file for your site. This is where you set your site's title, description, base URL, theme, menus, and many other global settings. Hugo also supports config.yaml and config.json.

4. Initialize a Git Repository
	
	Before we go any further, it's crucial to turn this project folder into a Git repository. This allows you to track changes and eventually push your code to GitHub.

	Run this command from inside your my-awesome-blog directory:

	```bash
	git init
    ```

    Congratulations! You have successfully created the skeleton of your Hugo site. The next step is to give it a visual design by adding a theme.

    {{< notice note >}}
	Pro Tip: You can start Hugo's built-in development server to see your site at any time, even though it's blank right now. Run hugo server -D and then open your browser to http://localhost:1313. Use Ctrl+C to stop the server.
	{{< /notice >}}


## Step 4: Add a Theme

A theme controls the visual design and layout of your Hugo site. [Hugo](https://themes.gohugo.io/) doesn't have a default theme, so adding one is essential. We'll use a theme called [Ananke], [Demo](https://ananke-theme.netlify.app/)

We will add the Ananke theme from its GitHub repository. A submodule links the theme's code directly to your project without copying all its files into your main repository.

Run this command:
```bash
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
```

What this command does:

- git submodule add: Tells Git you are adding an external repository.

- https://...: The URL of the theme's Git repository.

- themes/ananke: This clones the theme into a folder named ananke inside your themes/ directory.

Now you need to configure your site to use the Ananke theme. This is done in the main configuration file. Add the theme to your hugo.toml:

```bash
theme = "ananke"
```

and modify the baseURL to:
```bash
baseURL = "https://username.github.io/"
```

It's time to start the Hugo development server to see your site with its new look.
 
- Run the server. The -D flag includes content marked as "draft".
	```bash
	hugo server -D
	```
- Open your web browser and go to the address provided, usually:
	```bash
	http://localhost:1313
	```
- You should now see a basic website with the Ananke theme applied! It will show a sample post and a basic structure.

To stop the server, go back to your terminal and press Ctrl + C.

Congratulations! Your Hugo site now has a beautiful design. The next step is to create your first post and start adding content.

## Step 5: Create Your First Post

Now for the most exciting part—creating your first blog post! In Hugo, all content is written in Markdown (.md) files and placed in the content directory.


Hugo includes a helpful command to generate new content files with the correct structure and pre-filled "front matter."

Make sure you are in the root directory of your Hugo project (e.g., my-awesome-blog). Then, run the following command:

```bash
hugo new posts/my-first-post.md
```
What this command does:

- hugo new: The command to create new content.
- posts/: This creates a new folder called posts inside your content directory. This is a common convention for organizing blog posts.
- my-first-post.md: This is the filename of your new post. You can name it anything you like, using hyphens for spaces.

After running the command, you will see a success message:
```bash
Content created: content/posts/my-first-post.md
```

This creates a new markdown file in content/posts/ with front matter:

```bash
---
title: "My First Post"
date: 2023-12-07T15:13:47Z
draft: true
---
```

Key fields in the front matter:

- title: The title of your blog post.
- date: The creation date and time. Hugo sets this automatically.
- draft: true: This means the post is a draft and will not be published when you build your site for production.

 {{< notice note >}}
 To publish your post, you must change draft: true to draft: false.
 {{< /notice  >}}
 
## Step 6: Configure GitHub Pages

This step involves creating a GitHub repository and configuring it to host your site. There are two main methods: using a special repository name (username.github.io) or using a project site from a gh-pages branch or docs folder. 

We will use the first method as it's the most straightforward for a personal or blog site. This method creates a site available at https://yourusername.github.io.


1. Go to [github](https://www.github.com) and make sure you are logged in.

2. Click the "+" icon in the top-right corner and select "New repository".

3. On the creation page, enter the following critical details:

- Repository name: yourusername.github.io (Replace yourusername with your actual GitHub username. This is case-sensitive!).

- Description: (Optional) e.g., "My personal blog built with Hugo".

- Visibility: Public (Private repos can use GitHub Pages but require a paid account).

- Initialize this repository with a README: You can leave this unchecked. We will push an existing repository.

4. Click "Create repository".

Now, you need to link the local Git repository on your computer to the new one you just created on GitHub.

1. Go to GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)

2. Click "Generate new token" → "Generate new token (classic)"

3. Give it a descriptive name (e.g., "My Hugo Blog Deployment")

4. Select these scopes:
	- repo
	- workflow  

5. Click "Generate token"

6. Copy the token immediately - you won't be able to see it again!

In your terminal, navigate to your Hugo site's root directory and Run the following commands, replacing yourusername with your GitHub username.

```bash
# Add the remote GitHub repository as the origin
git remote add origin https://[YOUR_TOKEN]@github.com/[YOUR_USERNAME]/[YOUR_REPOSITORY_NAME].git

# Rename the default branch to 'main' (if it isn't already)
git branch -M main

# Stage all your existing files for the first commit
git add .

# Create your first commit with a message
git commit -m "Initial commit Hugo site with theme"

git push -u origin main
```

After the push completes, refresh your repository page on GitHub. You should see all your Hugo files there! 

Seeing all your Hugo files in the GitHub repository with no errors in the Actions tab means you've successfully completed the most difficult parts of the setup.

## Step 7: Deploy  with GitHub Actions

This is the most crucial step. GitHub Actions will automatically build your Hugo site and deploy it to GitHub Pages every time you push a new change to your main branch.

you need to tell GitHub to use the result of this action as your site's source:

1. Navigate to your GitHub repository in your web browser, click on the "Settings" tab in the top navigation menu.

2. In the left sidebar, click on "Actions", General and change the section "Workflow permissions" to "Read and write permissions" and click save.

3. In the left sidebar, click on "Pages", Under the "Build and deployment" section, you'll see the "Source" dropdown.

4. Once selected, the page will update. You should see a message similar to: "Your GitHub Pages site is currently being built from a GitHub Actions workflow. [View latest deployment]."

You're done! The configuration is now complete.

We will create a YAML file that defines a "workflow"—a set of instructions for GitHub's servers to follow.

In your Hugo project's root directory, you need to create a specific folder structure for the workflow file.

```bash
mkdir -p .github/workflows
```
This creates a hidden .github folder and a workflows folder inside it.

Inside the .github/workflows/ directory, create a new file named hugo.yaml.

```bash
touch .github/workflows/hugo.yaml
```

Open the .github/workflows/hugo.yaml file and copy the following configuration into it. This is a popular and reliable configuration that uses community-built actions.

```bash
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches:
      - main

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment
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
      HUGO_VERSION: 0.128.0  # Updated to latest stable version
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb
          sudo dpkg -i ${{ runner.temp }}/hugo.deb
          
      - name: Install Dart Sass
        run: sudo snap install dart-sass
        
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0
          
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5  # Updated to v5
        
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
        
      - name: Build with Hugo
        env:
          HUGO_ENVIRONMENT: production
          HUGO_ENV: production
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"
            
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3  # Updated artifact upload
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
        uses: actions/deploy-pages@v4  # Updated to v4
```

Now, Commit and Push the Workflow File


```bash
# Add the workflow file to git
git add .github/workflows/hugo.yaml

# Commit the changes
git commit -m "Add GitHub Actions workflow for automated deployment"

# Push to trigger the workflow
git push origin main
```
## Step 8: View Your Blog

Your blog will be available at https://username.github.io after the GitHub Action completes.


## Conclusion

You now have a fully functional blog hosted on GitHub Pages using Hugo! The setup might seem complex at first, but the benefits are worth it:

- Free hosting with GitHub Pages

- Blazing fast static site generation

- Version control with Git

- Easy content management with markdown

Happy blogging!


## Additional Resources:


[Cloud Native Trainer with Mike](https://www.youtube.com/watch?v=zrmeOu8DYyw) 

[Git crash course ]()

