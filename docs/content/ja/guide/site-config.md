---
title: サイト設定
linkTitle: サイト設定
weight: 9
---

このガイドでは、このドキュメントサイトをビジネスやプロジェクト向けに設定する方法を説明します。すべてのプロジェクト情報は単一の設定ファイルに集約されており、異なる組織向けのブランド変更とデプロイが簡単です。

<!--more-->

## 概要

このドキュメントテンプレートを新しいビジネス向けにデプロイする際、以下のプロジェクトメタデータを更新する必要があります：
- プロジェクト/製品名
- 説明とタグライン
- GitHubリポジトリURL
- ロゴとブランドアセット
- 連絡先とソーシャルリンク

これらの設定はすべて**1つのファイル**に集約されています：`hugo.yaml`。

## 設定ファイルの構造

`hugo.yaml`を開き、`params.project`セクションを見つけてください。これがすべてのプロジェクトメタデータの**単一ソース**です：

```yaml {filename="hugo.yaml"}
params:
  project:
    # コアアイデンティティ
    name: "プロジェクト名"
    shortName: "PN"
    tagline: "素晴らしいタグライン"
    
    # 説明
    description: "SEOとメタタグ用のプロジェクトの完全な説明。"
    shortDescription: "ヒーローセクション用の簡潔な説明"
    
    # 組織/著者情報
    author: "あなたの名前"
    organization: "あなたの組織"
    email: "contact@example.com"
    
    # リンク
    website: "https://your-website.com"
    github: "https://github.com/your-org/your-repo"
    githubEditBase: "https://github.com/your-org/your-repo/edit/main/docs/content"
```

## ステップバイステップ設定ガイド

{{< steps >}}

### 基本設定を更新

`hugo.yaml`の先頭で更新：

```yaml {filename="hugo.yaml"}
baseURL: "https://docs.your-domain.com/"
title: "プロジェクト名"
```

### プロジェクト情報を更新

`params.project`セクションで、すべてのフィールドをビジネス情報で更新します。

### 言語タイトルを更新

`languages`セクションで各言語の`title`を更新します。

### メニューリンクを更新

メインメニューのGitHubリンクを更新します。

### ブランドアセットを置き換え

`static/images/`の以下のファイルを置き換えます：

| ファイル | 用途 |
|----------|------|
| `logo.svg` | ライトモードロゴ |
| `logo-dark.svg` | ダークモードロゴ |
| `favicon.ico` | ブラウザファビコン |

### コンテンツを更新

メインコンテンツページを更新：
- ホームページ：`content/{lang}/_index.md`
- Aboutページ：`content/{lang}/about/index.md`

{{< /steps >}}

## コンテンツでのプロジェクト情報の使用

ショートコードを使用してコンテンツ内でプロジェクト情報を動的に表示できます：

```markdown
{{</* project "name" */>}}へようこそ！

{{</* project "description" */>}}

現在のバージョン：{{</* project "currentVersion" */>}}
```

## 設定チェックリスト

| 項目 | 場所 | 状態 |
|------|------|------|
| Base URL | `hugo.yaml` → `baseURL` | ☐ |
| サイトタイトル | `hugo.yaml` → `title` | ☐ |
| プロジェクト情報 | `hugo.yaml` → `params.project.*` | ☐ |
| 言語タイトル | `hugo.yaml` → `languages.*.title` | ☐ |
| GitHubメニューリンク | `hugo.yaml` → `menu.main` | ☐ |
| ロゴファイル | `static/images/logo*.svg` | ☐ |

## クイックスタート例

新しいデプロイのための最小限の設定：

```yaml {filename="hugo.yaml"}
baseURL: "https://docs.mybusiness.com/"
title: "マイビジネスドキュメント"

params:
  project:
    name: "マイビジネス"
    shortName: "MB"
    description: "マイビジネス製品のドキュメント"
    organization: "マイビジネス株式会社"
    github: "https://github.com/mybusiness/docs"
    githubEditBase: "https://github.com/mybusiness/docs/edit/main/docs/content"
    website: "https://mybusiness.com"
    currentVersion: "v1.0"
```
