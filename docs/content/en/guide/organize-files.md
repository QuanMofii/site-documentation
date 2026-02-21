---
title: Organize Files
weight: 1
prev: /guide
---

## Directory Structure

By default, Hugo searches for Markdown files in the `content` directory, and the structure of the directory determines the final output structure of your website.
Take this site as an example:

<!--more-->

{{< filetree/container >}}
  {{< filetree/folder name="content" >}}
    {{< filetree/file name="_index.md" >}}
    {{< filetree/folder name="docs" state="open" >}}
      {{< filetree/file name="_index.md" >}}
      {{< filetree/file name="getting-started.md" >}}
      {{< filetree/folder name="guide" state="open" >}}
        {{< filetree/file name="_index.md" >}}
        {{< filetree/file name="organize-files.md" >}}
      {{< /filetree/folder >}}
    {{< /filetree/folder >}}
    {{< filetree/folder name="blog" state="open" >}}
      {{< filetree/file name="_index.md" >}}
      {{< filetree/file name="post-1.md" >}}
    {{< /filetree/folder >}}
  {{< /filetree/folder >}}
{{< /filetree/container >}}

Each of the `_index.md` files is the index page for the corresponding section. The other Markdown files are regular pages.

```
content
├── _index.md // <- /
├── docs
│   ├── _index.md // <- /docs/
│   ├── getting-started.md // <- /docs/getting-started/
│   └── guide
│       ├── _index.md // <- /guide/
│       └── organize-files.md // <- /guide/organize-files/
└── blog
    ├── _index.md // <- /blog/
    └── post-1.md // <- /blog/post-1/
```

## Layouts

The theme offers three layouts for different content types:

| Layout    | Directory             | Features                                                         |
| :-------- | :-------------------- | :--------------------------------------------------------------- |
| `docs`    | `content/docs/`       | Ideal for structured documentation, same as this section.        |
| `blog`    | `content/blog/`       | For blog postings, with both listing and detailed article views. |
| `default` | All other directories | Single-page article view without sidebar.                        |

To customize a section to mirror the behavior of a built-in layout, specify the desired type in the front matter of the section's `_index.md`.

```yaml {filename="content/my-docs/_index.md"}
---
title: My Docs
cascade:
  type: docs
---
```

The above example configuration ensures that the content files inside `content/my-docs/` will be treated as documentation (`docs` type) by default.

## Sidebar Navigation

The sidebar navigation is generated automatically based on the content organization alphabetically. To manually configure the sidebar order, we can use the `weight` parameter in the front matter of the Markdown files.

```yaml {filename="content/guide/_index.md"}
---
title: Guide
weight: 2
---
```

{{< callout type="info" >}}
  It is recommended to keep the sidebar not too deep. If you have a lot of content, consider **splitting them into multiple sections**.
{{< /callout >}}

## Section Navigation


### Section Pagination Order

The order in which pages, accessed via [`PAGE.PrevInSection`](https://gohugo.io/methods/page/previnsection/) and [`PAGE.NextInSection`](https://gohugo.io/methods/page/nextinsection/) in a [page collection](https://gohugo.io/quick-reference/glossary/#page-collection), are ordered, is reversed by default.

To disable this reversed ordering you can set the `reversePagination` custom parameter in the page front matter to `false`. By default `reversePagination` is set to `true`.

#### Example

Given the following directory structure:

{{< filetree/container >}}
  {{< filetree/folder name="content" >}}
    {{< filetree/file name="_index.md" >}}
    {{< filetree/folder name="blog" state="open" >}}
      {{< filetree/file name="_index.md" >}}
      {{< filetree/folder name="my-blog-series" state="open" >}}
        {{< filetree/file name="_index.md" >}}
        {{< filetree/folder name="post-a" state="open" >}}
          {{< filetree/file name="index.md" >}}
        {{< /filetree/folder >}}
        {{< filetree/folder name="post-b" state="open" >}}
          {{< filetree/file name="index.md" >}}
        {{< /filetree/folder >}}
        {{< filetree/folder name="post-c" state="open" >}}
          {{< filetree/file name="index.md" >}}
        {{< /filetree/folder >}}
      {{< /filetree/folder >}}
    {{< /filetree/folder >}}
  {{< /filetree/folder >}}
{{< /filetree/container >}}

And the following front matter in the posts:

```yaml {filename="content/blog/my-blog-series/post-a/index.md"}
---
title: Post A
weight: 1
---
```
```yaml {filename="content/blog/my-blog-series/post-b/index.md"}
---
title: Post B
weight: 2
---
```
```yaml {filename="content/blog/my-blog-series/post-c/index.md"}
---
title: Post C
weight: 3
---
```

If the reader is at the bottom of `post-b/index.md`, they will see that the next page is `post-a`, and the previous page is `post-c`. This is due to `reversePagination` being set to `true` by default. This is ok when we want our posts to be displayed in chronological order from latest to oldest. However, in the case of a blog series where there are multiple parts, we typically want people to read the first post, and then move to the second and so on. So we want to disable the reversed ordering.

We can turn off `reversePagination` in every blog post in this series by adding the following front matter to `my-blog-series/_index.md`

```yaml {filename="content/blog/my-blog-series/_index.md"}
---
title: My Blog Series
cascade:
    params:
        reversePagination: false
---
```

We are using [`cascade`](https://gohugo.io/content-management/front-matter/#cascade-1) here to propagate the setting to all posts in the `my-blog-series` so that `reversePagination` is set to `false` for all descendents. This will now ensure that when the reader is on `post-b/index.md` they will see that the next page is `post-c` and the previous page is `post-a`.

## Breadcrumb Navigation

Breadcrumbs are auto-generated based on the directory structure of `/content`.

For example, consider the file structure [demonstrated above](#directory-structure). Given that structure, the breadcrumbs atop the page at `/guide/organize-files/` would appear automatically as follows:

```
Documentation > Guide > Organize Files
```

### Customizing Breadcrumb Link Titles

By default, each breadcrumb link is generated based on that page's `title` parameter. You can customize this by specifying a `linkTitle`.

For example, if instead of `Organize Files` we wanted the breadcrumb to be `Foo Bar`:

```yaml {filename="content/guide/organize-files.md"}
---
linkTitle: Foo Bar
title: Organize Files
---
```

This would now generate the following breadcrumbs:
```
Documentation > Guide > Foo Bar
```

### Enabling and Disabling Breadcrumbs

Whether breadcrumbs are enabled, or disabled, by default for a page, is determined by its [content type](https://gohugo.io/quick-reference/glossary/#content-type) and [page kind](https://gohugo.io/quick-reference/glossary/#page-kind):

|  Content Type   | Section  | Page      |
|:----------------|:--------:|:----------|
| `docs`          | Enabled  | Enabled   |
| `blog`          | Disabled | Enabled   |
| Any other type  | Disabled | Disabled  |

You can override these defaults on a page by setting `breadcrumbs` in its front matter:

```yaml {filename="content/guide/organize-files.md"}
---
breadcrumbs: false
title: Organize Files
---
```

Similarly you can use [cascade](https://gohugo.io/content-management/front-matter/#cascade-1) to override the defaults on a page and its decendents:

```yaml {filename="content/portfolio/_index.md"}
---
title: "Portfolio"

cascade:
  params:
    breadcrumbs: true
---
```

## Configure Content Directory

By default, the root `content/` directory is used by Hugo to build the site.
If you need to use a different directory for content, for example `docs/`, this can be done by setting the [`contentDir`](https://gohugo.io/getting-started/configuration/#contentdir) parameter in the site configuration `hugo.yaml`.

## Theme vs Site Architecture

This project uses a dual-directory architecture that separates **theme files** (reusable across projects) from **site files** (specific to this documentation site).

### Directory Structure Overview

```
theme/                          # Repository root
├── layouts/                     # Theme layouts (default templates)
├── assets/                      # Theme assets (CSS, JS)
├── static/                      # Theme static files
├── data/                        # Theme data files
├── i18n/                        # Theme translations
│
└── docs/                        # Example/documentation site
    ├── content/                 # Site content (Markdown files)
    ├── layouts/                 # Site layout overrides
    ├── assets/                  # Site-specific assets
    ├── static/                  # Site-specific static files
    ├── data/                    # Site-specific data
    └── hugo.yaml                # Site configuration
```

### How Hugo Combines Theme and Site Files

When Hugo builds the site from `docs/`, it uses `--themesDir=../..` to load the theme from the repository root. Hugo then **merges** files from both locations:

| Directory | Theme (root) | Site (docs/) | Behavior |
|:----------|:-------------|:-------------|:---------|
| `layouts/` | Default templates | Overrides/extensions | Site files override theme files with same name |
| `assets/` | Theme CSS, JS | Site-specific assets | Both available, site can override |
| `static/` | Theme defaults | Site-specific files | Merged, site files take precedence |
| `data/` | Theme data | Site data | Merged by filename |
| `content/` | (none) | All content | Only exists in site |

### When to Use Each Location

**Theme directories (root level)** - for reusable components:
- Default layouts that work for any site using this theme
- Core CSS/JS assets
- Default icons and images
- Translation strings (`i18n/`)

**Site directories (`docs/`)** - for site-specific content:
- All Markdown content files
- Custom shortcodes specific to this site
- Site-specific images and assets
- Configuration overrides

### Example: Layout Override

To customize a theme layout for your site, copy it to the same path under `docs/layouts/`:

{{< filetree/container >}}
  {{< filetree/folder name="layouts" >}}
    {{< filetree/folder name="partials" >}}
      {{< filetree/file name="footer.html" >}}
    {{< /filetree/folder >}}
  {{< /filetree/folder >}}
  {{< filetree/folder name="docs" >}}
    {{< filetree/folder name="layouts" >}}
      {{< filetree/folder name="partials" >}}
        {{< filetree/file name="footer.html" info="(overrides theme)" >}}
      {{< /filetree/folder >}}
    {{< /filetree/folder >}}
  {{< /filetree/folder >}}
{{< /filetree/container >}}

The `docs/layouts/partials/footer.html` will be used instead of the theme's version.

### Generated Files

The `docs/resources/_gen/` directory contains Hugo's generated/cached assets (processed images, compiled CSS). This directory is auto-generated and should typically be in `.gitignore`.

## Multi-Product and Version Structure

The theme supports organizing documentation by **products** and **versions**, ideal for businesses with multiple products or software with multiple releases.

### Content Structure for Products and Versions

```
content/
└── docs/
    ├── _index.md              # Products landing page
    ├── product-a/             # Product A
    │   ├── _index.md
    │   ├── v1/                # Version 1
    │   │   ├── _index.md
    │   │   ├── getting-started.md
    │   │   └── advanced/
    │   └── v2/                # Version 2 (latest)
    │       ├── _index.md
    │       └── ...
    ├── product-b/             # Product B
    │   └── v1/
    └── product-c/             # Product C
        └── v1/
```

### Menu Configuration

Configure dropdown menus in `hugo.yaml` to show products and versions:

```yaml {filename="hugo.yaml"}
menu:
  main:
    # Products dropdown (parent item without pageRef)
    - identifier: products
      name: Products
      weight: 1
    # Product children
    - identifier: product-a
      name: Product A
      pageRef: /docs/product-a
      parent: products
      weight: 1
      params:
        icon: chip
    - identifier: product-b
      name: Product B
      pageRef: /docs/product-b
      parent: products
      weight: 2
      params:
        icon: cloud

    # Versions dropdown
    - identifier: versions
      name: Versions
      weight: 2
    - identifier: ver-v2
      name: v2 (latest)
      pageRef: /docs/product-a/v2
      parent: versions
      weight: 1
    - identifier: ver-v1
      name: v1
      pageRef: /docs/product-a/v1
      parent: versions
      weight: 2
```

### How It Works

1. **Products dropdown**: Parent menu items without `pageRef` become dropdowns. Children with `parent: products` appear in the dropdown.
2. **Versions dropdown**: Same pattern - shows available versions for navigation.
3. **Sidebar scoping**: When viewing a product's docs (e.g., `/docs/product-a/v1/...`), the sidebar automatically scopes to that product's content only.

### Adding a New Product

{{< steps >}}

### Create the content structure

```bash
mkdir -p content/docs/my-product/v1
```

### Add index files

Create `content/docs/my-product/_index.md`:

```yaml
---
title: My Product
---
```

Create `content/docs/my-product/v1/_index.md`:

```yaml
---
title: My Product v1
---

Welcome to My Product documentation!
```

### Add to menu

In `hugo.yaml`, add under the products menu:

```yaml
- identifier: my-product
  name: My Product
  pageRef: /docs/my-product
  parent: products
  weight: 3
  params:
    icon: cube
```

{{< /steps >}}

### Adding a New Version

{{< steps >}}

### Create version directory

```bash
mkdir -p content/docs/my-product/v2
```

### Copy or create content

Copy from an existing version or create new content.

### Update versions menu

```yaml
- identifier: ver-v2
  name: v2 (latest)
  pageRef: /docs/my-product/v2
  parent: versions
  weight: 1
```

{{< /steps >}}

## Add Images

To add images, the easiest way is to put the image files in the same directory as the Markdown file.
For example, add an image file `image.png` alongside the `my-page.md` file:

{{< filetree/container >}}
  {{< filetree/folder name="content" >}}
    {{< filetree/folder name="docs" >}}
        {{< filetree/file name="my-page.md" >}}
        {{< filetree/file name="image.png" >}}
    {{< /filetree/folder >}}
  {{< /filetree/folder >}}
{{< /filetree/container >}}

Then, we can use the following Markdown syntax to add the image to the content:

```markdown {filename="content/docs/my-page.md"}
![](image.png)
```

We can also utilize the [page bundles][page-bundles] feature of Hugo to organize the image files together with the Markdown file. To achieve that, turn the `my-page.md` file into a directory `my-page` and put the content into a file named `index.md`, and put the image files inside the `my-page` directory:

{{< filetree/container >}}
  {{< filetree/folder name="content" >}}
    {{< filetree/folder name="docs" >}}
        {{< filetree/folder name="my-page" >}}
            {{< filetree/file name="index.md" >}}
            {{< filetree/file name="image.png" >}}
        {{< /filetree/folder >}}
    {{< /filetree/folder >}}
  {{< /filetree/folder >}}
{{< /filetree/container >}}

```markdown {filename="content/docs/my-page/index.md"}
![](image.png)
```

Alternatively, we can also put the image files in the `static` directory, which will make the images available for all pages:

{{< filetree/container >}}
  {{< filetree/folder name="static" >}}
    {{< filetree/folder name="images" >}}
        {{< filetree/file name="image.png" >}}
    {{< /filetree/folder >}}
  {{< /filetree/folder >}}
  {{< filetree/folder name="content" >}}
    {{< filetree/folder name="docs" >}}
        {{< filetree/file name="my-page.md" >}}
    {{< /filetree/folder >}}
  {{< /filetree/folder >}}
{{< /filetree/container >}}

Note that the image path begins with a slash `/` and is relative to the static directory:

```markdown {filename="content/docs/my-page.md"}
![](/images/image.png)
```

[page-bundles]: https://gohugo.io/content-management/page-bundles/#leaf-bundles
