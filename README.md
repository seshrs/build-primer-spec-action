# Build Primer Spec (GitHub Action)

This repository is a "composite" GitHub Action step that builds a [Primer Spec](https://github.com/eecs485staff/primer-spec) website using Jekyll. You should use this action to deploy your website to GitHub Pages if you would like to stay up-to-date with Primer Spec's newest features automatically.

## Setting up GitHub Pages deployment

**Prerequisite:** Follow [Primer Spec's "Usage" steps](https://github.com/eecs485staff/primer-spec#usage) to set up the Markdown/HTML files in your repository.

1. Create a directory named `.github` if it doesn't already exist in your repo. Inside, create another directory `.github/workflows`.
2. Create a file named `pages.yml` with the following contents:
```yml
# GitHub Pages Configuration
#
# This workflow builds your repository's website and deploys it using GitHub Pages.
name: Deploy website with GitHub Pages

on:
  # Run on all pushes to main.
  push:
    branches: ["main", "master"]
  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow one concurrent deployment
concurrency:
  group: "pages"
  cancel-in-progress: true

# We have two jobs:
# (1) A "build" job that's responsible for building the website using Jekyll.
# (2) A "deploy" job that pushes the output of the build job to GitHub Pages.
jobs:
  # (1) Build job
  build:
    runs-on: ubuntu-latest

    # Sequence of tasks for this job
    steps:
      # Check out latest code using a pre-existing GH action
      # Docs: https://github.com/actions/checkout
      - name: 📁 Checkout code
        uses: actions/checkout@v2

      # Build the Primer Spec website. The action places the fully-built
      # website in an `_site/` directory.
      # Docs: https://github.com/seshrs/build-primer-spec-action
      - name: 🛠 Build Primer Spec website
        uses: seshrs/build-primer-spec-action@v1

      # Upload the _site directory created in the above step for use by
      # GitHub Pages.
      # Docs: https://github.com/actions/upload-pages-artifact
      - name: 🏺 Upload artifact
        uses: actions/upload-pages-artifact@v1

  # (2) Deploy job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v1
```
3. Go to your repository's *"Settings"* Page. Click *"Pages"* in the sidebar. Under *"Build & Deployment"*, click on the *"Source"* dropdown. Choose ***"GitHub Actions"***.

That's it! Future changes to the `main` / `master` branch of your repository will build and upload your new website!
