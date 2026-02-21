---
title: 文件组织
weight: 1
prev: /guide
---

## 目录结构

默认情况下，Hugo 会在 `content` 目录中查找 Markdown 文件，目录的结构决定了网站最终的输出结构。
以本网站为例：

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

每个 `_index.md` 文件都是对应部分的索引页。其他 Markdown 文件则是常规页面。

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

## 布局

该主题为不同类型的内容提供了三种布局：

| 布局      | 目录               | 特点                                                           |
| :-------- | :----------------- | :------------------------------------------------------------- |
| `docs`    | `content/docs/`    | 适合结构化文档，与本部分相同。                                 |
| `blog`    | `content/blog/`    | 用于博客文章，包含列表和详细文章视图。                         |
| `default` | 其他所有目录       | 单页文章视图，无侧边栏。                                       |

要自定义一个部分以模仿内置布局的行为，可以在该部分的 `_index.md` 的 front matter 中指定所需的类型。

```yaml {filename="content/my-docs/_index.md"}
---
title: 我的文档
cascade:
  type: docs
---
```

上述示例配置确保 `content/my-docs/` 内的内容文件默认会被视为文档（`docs` 类型）。

## 侧边栏导航

侧边栏导航会根据内容组织按字母顺序自动生成。要手动配置侧边栏顺序，可以在 Markdown 文件的 front matter 中使用 `weight` 参数。

```yaml {filename="content/guide/_index.md"}
---
title: 指南
weight: 2
---
```

{{< callout type="info" >}}
  建议不要让侧边栏过深。如果有大量内容，可以考虑**将其拆分为多个部分**。
{{< /callout >}}

## 部分导航

### 部分分页顺序

通过 [`PAGE.PrevInSection`](https://gohugo.io/methods/page/previnsection/) 和 [`PAGE.NextInSection`](https://gohugo.io/methods/page/nextinsection/) 访问的页面在[页面集合](https://gohugo.io/quick-reference/glossary/#page-collection)中的顺序默认是反向的。

要禁用这种反向排序，可以在页面的 front matter 中将 `reversePagination` 自定义参数设置为 `false`。默认情况下，`reversePagination` 设置为 `true`。

#### 示例

给定以下目录结构：

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

并在文章的 front matter 中设置：

```yaml {filename="content/blog/my-blog-series/post-a/index.md"}
---
title: 文章 A
weight: 1
---
```
```yaml {filename="content/blog/my-blog-series/post-b/index.md"}
---
title: 文章 B
weight: 2
---
```
```yaml {filename="content/blog/my-blog-series/post-c/index.md"}
---
title: 文章 C
weight: 3
---
```

如果读者位于 `post-b/index.md` 的底部，他们会看到下一页是 `post-a`，上一页是 `post-c`。这是由于 `reversePagination` 默认设置为 `true`。当我们希望文章按从最新到最旧的顺序显示时，这是可以的。然而，对于多部分的博客系列，我们通常希望读者先阅读第一部分，然后依次阅读后续部分。因此，我们希望禁用反向排序。

我们可以通过在 `my-blog-series/_index.md` 中添加以下 front matter 来关闭该系列中所有博客文章的 `reversePagination`：

```yaml {filename="content/blog/my-blog-series/_index.md"}
---
title: 我的博客系列
cascade:
    params:
        reversePagination: false
---
```

这里我们使用 [`cascade`](https://gohugo.io/content-management/front-matter/#cascade-1) 将设置传播到 `my-blog-series` 中的所有文章，以便所有后代都将 `reversePagination` 设置为 `false`。这将确保当读者在 `post-b/index.md` 时，他们会看到下一页是 `post-c`，上一页是 `post-a`。

## 面包屑导航

面包屑会根据 `/content` 的目录结构自动生成。

例如，考虑[上面展示的](#directory-structure)文件结构。给定该结构，位于 `/guide/organize-files/` 的页面顶部的面包屑会自动显示如下：

```
文档 > 指南 > 组织文件
```

### 自定义面包屑链接标题

默认情况下，每个面包屑链接是根据该页面的 `title` 参数生成的。可以通过指定 `linkTitle` 来自定义。

例如，如果我们希望面包屑显示为 `Foo Bar` 而不是 `Organize Files`：

```yaml {filename="content/guide/organize-files.md"}
---
linkTitle: Foo Bar
title: 组织文件
---
```

现在会生成以下面包屑：
```
文档 > 指南 > Foo Bar
```

### 隐藏面包屑

可以通过在页面的 front matter 中指定 `breadcrumbs: false` 来完全隐藏面包屑：

```yaml {filename="content/guide/organize-files.md"}
---
breadcrumbs: false
title: 组织文件
---
```

## 配置内容目录

默认情况下，Hugo 使用根目录 `content/` 来构建网站。
如果需要使用其他目录作为内容目录，例如 `docs/`，可以在站点配置 `hugo.yaml` 中设置 [`contentDir`](https://gohugo.io/getting-started/configuration/#contentdir) 参数。

## 主题与站点架构

本项目采用双目录架构，将**主题文件**（可跨项目复用）与**站点文件**（本文档站点专用）分离。

### 目录结构概览

```
theme/                          # 仓库根目录
├── layouts/                     # 主题布局（默认模板）
├── assets/                      # 主题资源（CSS、JS）
├── static/                      # 主题静态文件
├── data/                        # 主题数据文件
├── i18n/                        # 主题翻译
│
└── docs/                        # 示例/文档站点
    ├── content/                 # 站点内容（Markdown 文件）
    ├── layouts/                 # 站点布局覆盖
    ├── assets/                  # 站点专用资源
    ├── static/                  # 站点专用静态文件
    ├── data/                    # 站点专用数据
    └── hugo.yaml                # 站点配置
```

### Hugo 如何合并主题和站点文件

当 Hugo 从 `docs/` 构建站点时，它使用 `--themesDir=../..` 从仓库根目录加载主题。然后 Hugo **合并**来自两个位置的文件：

| 目录 | 主题（根目录） | 站点（docs/） | 行为 |
|:-----|:-------------|:-------------|:-----|
| `layouts/` | 默认模板 | 覆盖/扩展 | 同名站点文件覆盖主题文件 |
| `assets/` | 主题 CSS、JS | 站点专用资源 | 两者都可用，站点可覆盖 |
| `static/` | 主题默认 | 站点专用文件 | 合并，站点文件优先 |
| `data/` | 主题数据 | 站点数据 | 按文件名合并 |
| `content/` | （无） | 所有内容 | 仅存在于站点 |

### 何时使用哪个位置

**主题目录（根级别）** - 用于可复用组件：
- 适用于使用此主题的任何站点的默认布局
- 核心 CSS/JS 资源
- 默认图标和图片
- 翻译字符串（`i18n/`）

**站点目录（`docs/`）** - 用于站点专用内容：
- 所有 Markdown 内容文件
- 本站点专用的自定义短代码
- 站点专用图片和资源
- 配置覆盖

### 示例：布局覆盖

要为站点自定义主题布局，将其复制到 `docs/layouts/` 下的相同路径：

{{< filetree/container >}}
  {{< filetree/folder name="layouts" >}}
    {{< filetree/folder name="partials" >}}
      {{< filetree/file name="footer.html" >}}
    {{< /filetree/folder >}}
  {{< /filetree/folder >}}
  {{< filetree/folder name="docs" >}}
    {{< filetree/folder name="layouts" >}}
      {{< filetree/folder name="partials" >}}
        {{< filetree/file name="footer.html" info="（覆盖主题）" >}}
      {{< /filetree/folder >}}
    {{< /filetree/folder >}}
  {{< /filetree/folder >}}
{{< /filetree/container >}}

`docs/layouts/partials/footer.html` 将代替主题版本使用。

### 生成的文件

`docs/resources/_gen/` 目录包含 Hugo 生成/缓存的资源（处理后的图片、编译后的 CSS）。此目录是自动生成的，通常应添加到 `.gitignore`。

## 多产品和版本结构

主题支持按**产品**和**版本**组织文档，非常适合拥有多个产品的企业或具有多个发布版本的软件。

### 产品和版本的内容结构

```
content/
└── docs/
    ├── _index.md              # 产品着陆页
    ├── product-a/             # 产品A
    │   ├── _index.md
    │   ├── v1/                # 版本1
    │   │   ├── _index.md
    │   │   ├── getting-started.md
    │   │   └── advanced/
    │   └── v2/                # 版本2（最新）
    │       ├── _index.md
    │       └── ...
    ├── product-b/             # 产品B
    │   └── v1/
    └── product-c/             # 产品C
        └── v1/
```

### 菜单配置

在`hugo.yaml`中配置下拉菜单以显示产品和版本：

```yaml {filename="hugo.yaml"}
menu:
  main:
    # 产品下拉菜单（无pageRef的父项）
    - identifier: products
      name: Products
      weight: 1
    # 产品子项
    - identifier: product-a
      name: 产品A
      pageRef: /docs/product-a
      parent: products
      weight: 1
      params:
        icon: chip
    - identifier: product-b
      name: 产品B
      pageRef: /docs/product-b
      parent: products
      weight: 2
      params:
        icon: cloud

    # 版本下拉菜单
    - identifier: versions
      name: Versions
      weight: 2
    - identifier: ver-v2
      name: v2（最新）
      pageRef: /docs/product-a/v2
      parent: versions
      weight: 1
    - identifier: ver-v1
      name: v1
      pageRef: /docs/product-a/v1
      parent: versions
      weight: 2
```

### 工作原理

1. **产品下拉菜单**：没有`pageRef`的父菜单项会变成下拉菜单。带有`parent: products`的子项会显示在下拉菜单中。
2. **版本下拉菜单**：相同模式 - 显示可用版本供导航。
3. **侧边栏范围**：查看产品文档（如`/docs/product-a/v1/...`）时，侧边栏会自动限定为该产品的内容。

### 添加新产品

{{< steps >}}

### 创建内容结构

```bash
mkdir -p content/docs/my-product/v1
```

### 添加索引文件

创建`content/docs/my-product/_index.md`：

```yaml
---
title: 我的产品
---
```

创建`content/docs/my-product/v1/_index.md`：

```yaml
---
title: 我的产品 v1
---

欢迎使用我的产品文档！
```

### 添加到菜单

在`hugo.yaml`中添加到products菜单：

```yaml
- identifier: my-product
  name: 我的产品
  pageRef: /docs/my-product
  parent: products
  weight: 3
  params:
    icon: cube
```

{{< /steps >}}

### 添加新版本

{{< steps >}}

### 创建版本目录

```bash
mkdir -p content/docs/my-product/v2
```

### 复制或创建内容

从现有版本复制或创建新内容。

### 更新versions菜单

```yaml
- identifier: ver-v2
  name: v2（最新）
  pageRef: /docs/my-product/v2
  parent: versions
  weight: 1
```

{{< /steps >}}

## 添加图片

添加图片最简单的方法是将图片文件放在与 Markdown 文件相同的目录中。
例如，将图片文件 `image.png` 与 `my-page.md` 文件放在一起：

{{< filetree/container >}}
  {{< filetree/folder name="content" >}}
    {{< filetree/folder name="docs" >}}
        {{< filetree/file name="my-page.md" >}}
        {{< filetree/file name="image.png" >}}
    {{< /filetree/folder >}}
  {{< /filetree/folder >}}
{{< /filetree/container >}}

然后，可以使用以下 Markdown 语法将图片添加到内容中：

```markdown {filename="content/docs/my-page.md"}
![](image.png)
```

我们还可以利用 Hugo 的[页面包][page-bundles]功能将图片文件与 Markdown 文件组织在一起。为此，将 `my-page.md` 文件转换为目录 `my-page`，并将内容放入名为 `index.md` 的文件中，然后将图片文件放入 `my-page` 目录中：

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

或者，我们也可以将图片文件放在 `static` 目录中，这样所有页面都可以访问这些图片：

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

注意，图片路径以斜杠 `/` 开头，并且相对于 static 目录：

```markdown {filename="content/docs/my-page.md"}
![](/images/image.png)
```

[page-bundles]: https://gohugo.io/content-management/page-bundles/#leaf-bundles