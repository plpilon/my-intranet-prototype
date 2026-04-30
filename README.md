# Guide: Implementing a GC Intranet via Git, MkDocs, and NGINX
1. Architectural Overview
This intranet architecture utilizes a Static Site Generator (SSG) approach. It separates content creation from presentation and hosting to ensure security and performance within a firewalled environment.
Version Control: Hosted on GCcode (GitLab) using `staging` and `main` branches.
Engine: MkDocs with the `static-i18n` plugin for bilingual routing.
UI: GCWeb (Canada.ca Design System) delivered via CDN.
Hosting: NGINX serving two distinct paths: `/` (Production) and `/preview` (Staging).
---
## 2. Configuration Core (`mkdocs.yml`)
The `mkdocs.yml` file manages your bilingual navigation labels and enables advanced Markdown features.
```yaml
site_name: DIA Intranet / Intranet de l'AID
site_url: [https://intranet.dia.gc.ca/](https://intranet.dia.gc.ca/)

theme:
  name: null
  custom_dir: theme

plugins:
  - search:
      lang: [en, fr]
  - i18n:
      docs_structure: suffix
      fallback_to_default: true
      languages:
        - locale: en
          name: English
          default: true
          nav:
            - Home: index.md
            - Resources: resources.md
        - locale: fr
          name: Français
          nav:
            - Accueil: index.md
            - Ressources pour les employés: resources.md

markdown_extensions:
  - md_in_html
  - attr_list

```
---
## 3. Operational Workflow (Comms Advisor)
Designed for non-technical users to manage content entirely within the GitLab Web IDE.

### Step 1: Drafting in Staging
Switch the branch dropdown in GCcode to `staging`.
Click Web IDE.
Bilingual Pairing: Ensure every page has an English (`.en.md`) and French (`.fr.md`) version.
Metadata: Start every file with a title block:
Markdown
    ```
    ---
    title: Your Page Title
    ---
    
    ```
Commit: Save changes to trigger the auto-preview.

### Step 2: Preview & Approval
Verify the build by visiting `https://intranet.dia.gc.ca/preview/`.
Once approved, create a Merge Request from `staging` to `main`.

### Step 3: Deployment
Technical Lead reviews and merges the request.
The Production pipeline updates the live site at `https://intranet.dia.gc.ca/`.

---
## 4. Component Cheat Sheet (HTML in Markdown)
Drop these snippets into your Markdown files to use GCWeb design patterns.

### Page Introduction
HTML
```
<div class="gc-intro-provisional">
  <div class="container">
    <div class="row">
      <div class="col-md-7">
        <h1 class="h1">Official Page Title</h1>
        <p>A brief summary paragraph for the audience.</p>
      </div>
    </div>
  </div>
</div>

```
### 3-Column Grid
Note: Ensure there is a blank line between the HTML tag and the Markdown header.
HTML
```
<div class="row" markdown="1">

<div class="col-md-4" markdown="1">
### Section A
Content for column one.
</div>

<div class="col-md-4" markdown="1">
### Section B
Content for column two.
</div>

<div class="col-md-4" markdown="1">
### Section C
Content for column three.
</div>

</div>

```
---
## 5. Automation Pipeline (`.gitlab-ci.yml`)
YAML
```
stages:
  - build
  - deploy

build_site:
  stage: build
  image: python:3.10
  script:
    - pip install mkdocs mkdocs-static-i18n
    - mkdocs build
  artifacts:
    paths:
      - site/

deploy_preview:
  stage: deploy
  only:
    - staging
  script:
    - rsync -avz site/ user@server:/var/www/html/staging/

deploy_prod:
  stage: deploy
  only:
    - main
  when: manual
  script:
    - rsync -avz site/ user@server:/var/www/html/production/

```
---
## 6. NGINX Configuration
Nginx
```
server {
    listen 80;
    server_name intranet.dia.gc.ca;

    location / {
        root /var/www/html/production;
        index index.html;
    }

    location /preview {
        alias /var/www/html/staging;
        index index.html;
    }
}
```
