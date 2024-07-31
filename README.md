# Getting Started

1.  Create a new GitHub repository with name `<user-name>.github.io` & check  add README check box
2.  Go to its settings, click `Pages`
3.  Select the `main` branch as the source for deployment under `Build and Deployment`, `Branch`
4.  Go to `<user-name>.github.io` to find your static web page available

# Using `MkDocs` with GitHub Pages

**Prerequisite:** Must have python installed

Install `MkDocs`

```
pip install mkdocs
```

Inside the GitHub repo directory initiate `MkDocs` project

```
python -m mkdocs new .
```

You should have a project structure that looks like this

```
<user-name>.github.io/
	|---mkdocs.yml      # The configuration file.
	|---docs/
	      |---index.md  # The documentation homepage.
	      |---...       # Other markdown pages, images and other files.
```

Checkout your new site locally at http://localhost:8000

```
python -m mkdocs serve
```

(Optional) Use material for `MkDocs`. Add in `mkdocs.yml`

```yaml
theme:
  name: material
```

(Optional) Add multiple pages & navigation to your website. Add in `mkdocs.yml`

```
nav:
  - Home: index.md
  - <page-1-name>: <page-1-file>
  - <page-2-name>: <page-2-file>
  - ...
```

## Using GitHub Actions to Build & Deploy your site

Create `.github/workflows/build.yml`

```yaml
---
name: build

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.11"
          
      # Allows mkdocs to commit to the repo
      - run: |
          git config user.name github-actions[bot]
          git config user.email \
          41898282+github-actions[bot]@users.noreply.github.com

      - run: pip install mkdocs
      - run: mkdocs gh-deploy --force --clean
```

Under GitHub repository's `Setting` > `Code and automation` > `Pages` select `gh-pages` as the branch for deployment of your site with `/ (root)` folder

## Add YAML & HTML Linting

Create `.github/workflows/lint.yml` file to lint YAML & HTML files on pull requests to the `main` branch

```yaml
---
name: lint

on:
  pull_request:
    branches:
      - "main"

jobs:
  lint-html:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: "latest"

      - name: install prettier
        run: npm install --save-dev --save-exact prettier

      - name: lint html files
        run: npx prettier . --write

  lint-yaml:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Install yamllint
        run: pip install yamllint

      - name: Lint yaml files
        run: yamllint .
```
