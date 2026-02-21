---
title: "Đa ngôn ngữ"
weight: 1
prev: /docs/advanced/additional-pages
---

Theme hỗ trợ tạo site đa ngôn ngữ bằng [chế độ đa ngôn ngữ](https://gohugo.io/content-management/multilingual/) của Hugo.

<!--more-->

## Bật đa ngôn ngữ

Để site đa ngôn ngữ, cần khai báo các ngôn ngữ được hỗ trợ trong file cấu hình site:

```yaml {filename="hugo.yaml"}
defaultContentLanguage: en
languages:
  en:
    languageName: English
    weight: 1
  fr:
    languageName: Français
    weight: 2
  ja:
    languageName: 日本語
    weight: 3
```

## Quản lý bản dịch theo tên file

Hugo hỗ trợ quản lý bản dịch theo tên file. Ví dụ nếu có file tiếng Anh `content/docs/_index.md`, có thể tạo `content/docs/_index.fr.md` cho bản tiếng Pháp.

{{< filetree/container >}}
  {{< filetree/folder name="content" >}}
    {{< filetree/folder name="docs" state="open" >}}
      {{< filetree/file name="_index.md" >}}
      {{< filetree/file name="_index.fr.md" >}}
      {{< filetree/file name="_index.ja.md" >}}
    {{< /filetree/folder >}}
  {{< /filetree/folder >}}
{{< /filetree/container >}}

Lưu ý: Hugo cũng hỗ trợ [dịch theo thư mục nội dung](https://gohugo.io/content-management/multilingual/#translation-by-content-directory).

## Dịch mục menu

Để dịch mục menu trên thanh điều hướng, cần đặt trường `identifier`:

```yaml {filename="hugo.yaml"}
menu:
  main:
    - identifier: documentation
      name: Documentation
      pageRef: /docs
      weight: 1
    - identifier: blog
      name: Blog
      pageRef: /blog
      weight: 2
```

và dịch trong file i18n tương ứng:

```yaml {filename="i18n/vi.yaml"}
documentation: Tài liệu
blog: Blog
```

## Dịch chuỗi

Để dịch chuỗi ở các vị trí khác, thêm bản dịch vào file i18n tương ứng:

```yaml {filename="i18n/vi.yaml"}
readMore: Đọc thêm
```

Danh sách chuỗi theme dùng nằm trong file `i18n/en.yaml`.

## Đọc thêm

- [Hugo Multilingual Mode](https://gohugo.io/content-management/multilingual/)
- [Hugo Multilingual Part 1: Content translation](https://www.regisphilibert.com/blog/2018/08/hugo-multilingual-part-1-managing-content-translation/)
- [Hugo Multilingual Part 2: Strings localization](https://www.regisphilibert.com/blog/2018/08/hugo-multilingual-part-2-i18n-string-localization/)
