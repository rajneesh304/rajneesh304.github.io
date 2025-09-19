---
title: Hosting blogs using Jekyll
date: 2025-09-19
author: rajneesh304
category: "[Tech]"
---

# Introduction
I am maintaining my blogs inside an `obsidian` vault. I want my `Jekyll` website to pick up blogs from a particular directory in that vault periodically. I am syncing that vault to github. 
# Prereqs

1. Basic Jekyll Setup
2. A repo which contains blog posts

# Steps to sync blogs with jekyll
## Step 1: Create a Personal Access Token (PAT)
Since the workflow needs to access and write to a private repository, we must create a Personal Access Token. This token will act as a password for our workflow.
1. Go to **GitHub Settings** > **Developer settings** > **Personal access tokens** > **Tokens (classic)**.
2. Click **Generate new token**.
3. Give the token a descriptive name, like "Jekyll-Obsidian-Sync."
4. Set an expiration date for the token.
5. Under **Select scopes**, check the box for `repo`. This gives the token read and write access to our private repositories.
6. Click **Generate token** and **copy the token value immediately**.

## Step 2: Add the PAT to Obsidian Repository Secrets
We must store the token as a secret in our Obsidian repository to keep it secure.
1. Navigate to the **Obsidian repository** on GitHub.
2. Click **Settings** > **Secrets and variables** > **Actions**.
3. Click **New repository secret**.
4. In the "Name" field, type `JEKYLL_SYNC_TOKEN`.
5. In the "Value" field, paste the token copied in the previous step.
6. Click **Add secret**.

## Step 3: Create the GitHub Actions Workflow File
Now we'll create a YAML file that defines the sync workflow. This file lives in the `.github/workflows/` directory of our **Obsidian vault repository**.
1. In the local Obsidian vault repository, create a new directory named `.github/workflows`.
2. Inside this new directory, create a file named `sync-to-jekyll.yml`.
3. Paste the following code into the `sync-to-jekyll.yml` file:

```yaml
name: Sync to Jekyll

on:
  push:
    branches:
      - main  # or whatever branch you use for your Obsidian notes

jobs:
  sync:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Obsidian Vault
        uses: actions/checkout@v4
        with:
          repository: your-username/your-obsidian-vault # Replace with your repo name
          token: ${{ secrets.JEKYLL_SYNC_TOKEN }}

      - name: Configure Git
        run: |
          git config --global user.email "your-email@example.com"
          git config --global user.name "your-username"

      - name: Clone Jekyll Repository
        run: |
          git clone --single-branch --branch main https://your-username:${{ secrets.JEKYLL_SYNC_TOKEN }}@github.com/your-username/your-jekyll-site.git
          cd your-jekyll-site
          
      - name: Copy Obsidian Notes to Jekyll Posts
        run: |
          mkdir -p your-jekyll-site/_posts
          cp -r your-obsidian-vault/_posts/* your-jekyll-site/_posts/  # Assumes posts are in _posts folder in your vault
          
      - name: Commit and Push Changes
        run: |
          cd your-jekyll-site
          git add .
          git commit -m "Automated sync from Obsidian vault"
          git push
```

replace `your-username`, `your-obsidian-vault`, and `your-jekyll-site` with actual GitHub username and repository names.