---
title: 站点配置
linkTitle: 站点配置
weight: 9
---

本指南说明如何为您的业务或项目配置此文档站点。所有项目信息都集中在单个配置文件中，使不同组织的品牌更换和部署变得简单。

<!--more-->

## 概述

为新业务部署此文档模板时，您需要更新项目元数据：
- 项目/产品名称
- 描述和标语
- GitHub仓库URL
- Logo和品牌资源
- 联系方式和社交链接

所有这些设置都集中在**一个文件**中：`hugo.yaml`。

## 配置文件结构

打开`hugo.yaml`并找到`params.project`部分。这是所有项目元数据的**单一来源**：

```yaml {filename="hugo.yaml"}
params:
  project:
    # 核心标识
    name: "您的项目名称"
    shortName: "YPN"
    tagline: "您的精彩标语"
    
    # 描述
    description: "用于SEO和元标签的项目完整描述。"
    shortDescription: "用于hero部分的简短描述"
    
    # 组织/作者信息
    author: "您的姓名"
    organization: "您的组织"
    email: "contact@example.com"
    
    # 链接
    website: "https://your-website.com"
    github: "https://github.com/your-org/your-repo"
    githubEditBase: "https://github.com/your-org/your-repo/edit/main/docs/content"
```

## 分步配置指南

{{< steps >}}

### 更新基本设置

在`hugo.yaml`顶部更新：

```yaml {filename="hugo.yaml"}
baseURL: "https://docs.your-domain.com/"
title: "您的项目名称"
```

### 更新项目信息

在`params.project`部分，用您的业务信息更新所有字段。

### 更新语言标题

在`languages`部分更新每种语言的`title`。

### 更新菜单链接

更新主菜单中的GitHub链接。

### 替换品牌资源

替换`static/images/`中的以下文件：

| 文件 | 用途 |
|------|------|
| `logo.svg` | 亮色模式logo |
| `logo-dark.svg` | 暗色模式logo |
| `favicon.ico` | 浏览器图标 |

### 更新内容

更新主要内容页面：
- 首页：`content/{lang}/_index.md`
- 关于页面：`content/{lang}/about/index.md`

{{< /steps >}}

## 在内容中使用项目信息

您可以使用短代码在内容中动态显示项目信息：

```markdown
欢迎使用{{</* project "name" */>}}！

{{</* project "description" */>}}

当前版本：{{</* project "currentVersion" */>}}
```

## 配置清单

| 项目 | 位置 | 状态 |
|------|------|------|
| Base URL | `hugo.yaml` → `baseURL` | ☐ |
| 站点标题 | `hugo.yaml` → `title` | ☐ |
| 项目信息 | `hugo.yaml` → `params.project.*` | ☐ |
| 语言标题 | `hugo.yaml` → `languages.*.title` | ☐ |
| GitHub菜单链接 | `hugo.yaml` → `menu.main` | ☐ |
| Logo文件 | `static/images/logo*.svg` | ☐ |

## 快速开始示例

新部署的最小配置：

```yaml {filename="hugo.yaml"}
baseURL: "https://docs.mybusiness.com/"
title: "我的业务文档"

params:
  project:
    name: "我的业务"
    shortName: "MB"
    description: "我的业务产品文档"
    organization: "我的业务公司"
    github: "https://github.com/mybusiness/docs"
    githubEditBase: "https://github.com/mybusiness/docs/edit/main/docs/content"
    website: "https://mybusiness.com"
    currentVersion: "v1.0"
```
