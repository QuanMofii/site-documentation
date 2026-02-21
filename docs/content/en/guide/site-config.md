---
title: Site Config
linkTitle: Site Config
weight: 9
---

This guide explains how to configure this documentation site for your business or project. All project-specific information is centralized in a single configuration file, making it easy to rebrand and deploy for different organizations.

<!--more-->

## Overview

When deploying this documentation template for a new business, you need to update project metadata such as:
- Project/product name
- Description and tagline
- GitHub repository URL
- Logo and branding assets
- Contact and social links

All these settings are centralized in **one file**: `hugo.yaml`.

## Configuration File Structure

Open `hugo.yaml` and locate the `params.project` section. This is the **single source of truth** for all project metadata:

```yaml {filename="hugo.yaml"}
params:
  project:
    # Core identity
    name: "Your Project Name"                         # Project/product name
    shortName: "YPN"                                  # Short name for compact displays
    tagline: "Your Awesome Tagline"                   # Brief tagline/slogan
    
    # Descriptions
    description: "Full description of your project for SEO and meta tags."
    shortDescription: "Brief description for hero sections"
    
    # Organization/Author info
    author: "Your Name"
    organization: "Your Organization"
    email: "contact@example.com"
    
    # Links
    website: "https://your-website.com"
    github: "https://github.com/your-org/your-repo"
    githubEditBase: "https://github.com/your-org/your-repo/edit/main/docs/content"
    
    # Social links (optional - leave empty if not used)
    twitter: "https://twitter.com/yourhandle"
    discord: "https://discord.gg/yourserver"
    
    # Branding
    logo: "images/logo.svg"
    logoDark: "images/logo-dark.svg"
    
    # Version info
    currentVersion: "v1.0"
    
    # License
    license: "MIT"
    licenseUrl: "https://github.com/your-org/your-repo/blob/main/LICENSE"
```

## Step-by-Step Configuration

Follow these steps to configure the site for your business:

{{< steps >}}

### Update Base Settings

At the top of `hugo.yaml`, update:

```yaml {filename="hugo.yaml"}
baseURL: "https://docs.your-domain.com/"
title: "Your Project Name"
```

### Update Project Info

In the `params.project` section, update all fields with your business information:

| Field | Description | Example |
|-------|-------------|---------|
| `name` | Full project name | "My Product" |
| `shortName` | Abbreviated name | "MP" |
| `tagline` | Brief slogan | "Build faster, ship smarter" |
| `description` | Full description for SEO | "Complete documentation for..." |
| `author` | Author or team name | "DevTeam" |
| `organization` | Company/org name | "Acme Inc" |
| `github` | Repository URL | "https://github.com/acme/docs" |

### Update Language Titles

For each language in the `languages` section, update the `title`:

```yaml {filename="hugo.yaml"}
languages:
  en:
    title: Your Project Name
  vi:
    title: Tên Dự Án
  ja:
    title: プロジェクト名
  zh-cn:
    title: 项目名称
  fa:
    title: نام پروژه
```

### Update Menu Links

Update the GitHub link in the main menu:

```yaml {filename="hugo.yaml"}
menu:
  main:
    - identifier: github
      name: GitHub
      url: "https://github.com/your-org/your-repo"
      params:
        icon: github
```

### Update Edit URL

Update the base URL for the "Edit this page" feature:

```yaml {filename="hugo.yaml"}
params:
  editURL:
    base: "https://github.com/your-org/your-repo/edit/main/docs/content"
```

### Replace Branding Assets

Replace these files in `static/images/`:

| File | Purpose | Recommended Size |
|------|---------|------------------|
| `logo.svg` | Light mode logo | Height: 32-40px |
| `logo-dark.svg` | Dark mode logo | Height: 32-40px |
| `favicon.ico` | Browser favicon | 32x32px |

### Configure Comments (Optional)

If using Giscus for comments, update the repository settings:

```yaml {filename="hugo.yaml"}
params:
  comments:
    enable: true
    type: giscus
    giscus:
      repo: your-org/your-repo
      repoId: YOUR_REPO_ID
      category: General
      categoryId: YOUR_CATEGORY_ID
```

Visit [giscus.app](https://giscus.app/) to generate these values.

### Update Content

Update the main content pages:
- Homepage: `content/{lang}/_index.md`
- About page: `content/{lang}/about/index.md`
- Product documentation structure

{{< /steps >}}

## Using Project Info in Content

You can dynamically display project information in your content using shortcodes:

### Display Project Values

```markdown
Welcome to {{</* project "name" */>}}!

{{</* project "description" */>}}

Current version: {{</* project "currentVersion" */>}}
```

**Available fields:**
- `name`, `shortName`, `tagline`
- `description`, `shortDescription`
- `author`, `organization`, `email`
- `website`, `github`
- `currentVersion`, `license`, `licenseUrl`
- `twitter`, `discord`

### Create Links

```markdown
Check out our {{</* project-link "github" */>}}

Visit the {{</* project-link "website" "official website" */>}}

Licensed under {{</* project-link "license" */>}}
```

## Configuration Checklist

Use this checklist when setting up for a new business:

| Item | Location | Status |
|------|----------|--------|
| Base URL | `hugo.yaml` → `baseURL` | ☐ |
| Site title | `hugo.yaml` → `title` | ☐ |
| Project info | `hugo.yaml` → `params.project.*` | ☐ |
| Language titles | `hugo.yaml` → `languages.*.title` | ☐ |
| GitHub menu link | `hugo.yaml` → `menu.main` | ☐ |
| Edit URL | `hugo.yaml` → `params.editURL.base` | ☐ |
| Logo files | `static/images/logo*.svg` | ☐ |
| Favicon | `static/images/favicon.ico` | ☐ |
| Homepage content | `content/*/\_index.md` | ☐ |
| About page | `content/*/about/index.md` | ☐ |
| Giscus config (if used) | `hugo.yaml` → `params.comments.giscus` | ☐ |

## Files Requiring Manual Updates

Some files cannot use dynamic configuration and must be updated manually:

| File | What to Change |
|------|----------------|
| `go.mod` | Module path (if using Hugo Modules) |
| `README.md` | Project description and badges |
| `LICENSE` | License text if changing license type |
| Content front matter | Page titles containing project name |
| Banner messages | `hugo.yaml` → `params.banner.message` and per-language banners |

## Quick Start Example

Minimum configuration for a new deployment:

```yaml {filename="hugo.yaml"}
baseURL: "https://docs.mybusiness.com/"
title: "My Business Docs"

params:
  project:
    name: "My Business"
    shortName: "MB"
    description: "Documentation for My Business products"
    organization: "My Business Inc"
    github: "https://github.com/mybusiness/docs"
    githubEditBase: "https://github.com/mybusiness/docs/edit/main/docs/content"
    website: "https://mybusiness.com"
    currentVersion: "v1.0"
```

Then update:
1. GitHub menu link URL
2. Logo files in `static/images/`
3. Homepage and About page content

## Tips for Multi-Deployment Use

1. **Maintain a base template** - Keep a clean version without business-specific content
2. **Use Git branches** - Create separate branches for different deployments
3. **Document changes** - Keep notes on what was customized for each deployment
4. **Automate setup** - Consider creating a setup script that prompts for project info
