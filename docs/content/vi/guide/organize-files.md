---
title: Tổ chức file
weight: 1
prev: /guide
---

## Cấu trúc thư mục

Mặc định Hugo tìm file Markdown trong thư mục `content`, và cấu trúc thư mục quyết định cấu trúc đầu ra của website.
Lấy site này làm ví dụ:

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

Mỗi file `_index.md` là trang index của section tương ứng. Các file Markdown khác là trang thường.

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

Giao diện cung cấp ba bố cục cho các loại nội dung khác nhau:

| Layout    | Thư mục               | Tính năng                                                                 |
| :-------- | :--------------------- | :------------------------------------------------------------------------- |
| `docs`    | `content/docs/`        | Phù hợp tài liệu có cấu trúc, giống section này.                           |
| `blog`    | `content/blog/`       | Cho bài blog, có trang danh sách và trang bài chi tiết.                    |
| `default` | Các thư mục khác      | Trang bài đơn, không sidebar.                                             |

Để tùy section dùng hành vi của layout có sẵn, đặt `type` trong front matter của `_index.md` của section đó.

```yaml {filename="content/my-docs/_index.md"}
---
title: My Docs
cascade:
  type: docs
---
```

Cấu hình ví dụ trên đảm bảo các file trong `content/my-docs/` được coi là tài liệu (type `docs`) mặc định.

## Thanh điều hướng bên (Sidebar)

Sidebar được tạo tự động theo tổ chức nội dung (theo thứ tự chữ cái). Để cấu hình thứ tự sidebar thủ công, dùng tham số `weight` trong front matter của file Markdown.

```yaml {filename="content/guide/_index.md"}
---
title: Hướng dẫn
weight: 2
---
```

{{< callout type="info" >}}
  Nên giữ sidebar không quá sâu. Nếu nội dung nhiều, hãy **tách thành nhiều section**.
{{< /callout >}}

## Điều hướng section


### Thứ tự phân trang section

Thứ tự các trang (truy cập qua [`PAGE.PrevInSection`](https://gohugo.io/methods/page/previnsection/) và [`PAGE.NextInSection`](https://gohugo.io/methods/page/nextinsection/) trong [page collection](https://gohugo.io/quick-reference/glossary/#page-collection)) mặc định bị đảo ngược.

Để tắt thứ tự đảo ngược, đặt tham số tùy chỉnh `reversePagination` trong front matter của trang thành `false`. Mặc định `reversePagination` là `true`.

#### Ví dụ

Với cấu trúc thư mục sau:

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

Và front matter sau trong các bài:

```yaml {filename="content/blog/my-blog-series/post-a/index.md"}
---
title: Bài A
weight: 1
---
```
```yaml {filename="content/blog/my-blog-series/post-b/index.md"}
---
title: Bài B
weight: 2
---
```
```yaml {filename="content/blog/my-blog-series/post-c/index.md"}
---
title: Bài C
weight: 3
---
```

Khi đọc ở cuối `post-b/index.md`, trang "Tiếp" sẽ là `post-a`, trang "Trước" là `post-c` do `reversePagination` mặc định là `true`. Cách này phù hợp khi muốn bài hiển thị theo thứ tự thời gian mới nhất trước. Với series blog nhiều phần, ta thường muốn người đọc từ bài đầu rồi đến bài hai, ... nên cần tắt thứ tự đảo.

Có thể tắt `reversePagination` cho mọi bài trong series bằng cách thêm front matter sau vào `my-blog-series/_index.md`:

```yaml {filename="content/blog/my-blog-series/_index.md"}
---
title: Series blog của tôi
cascade:
    params:
        reversePagination: false
---
```

Dùng [`cascade`](https://gohugo.io/content-management/front-matter/#cascade-1) để áp dụng thiết lập cho mọi bài trong `my-blog-series`, tức `reversePagination` thành `false` cho mọi trang con. Khi đó ở `post-b/index.md`, trang "Tiếp" sẽ là `post-c` và "Trước" là `post-a`.

## Điều hướng breadcrumb

Breadcrumb được tạo tự động theo cấu trúc thư mục `/content`.

Ví dụ với cấu trúc file [ở trên](#cấu-trúc-thư-mục), breadcrumb ở đầu trang `/guide/organize-files/` sẽ hiển thị:

```
Tài liệu > Hướng dẫn > Tổ chức file
```

### Tùy chỉnh tiêu đề liên kết breadcrumb

Mặc định mỗi liên kết breadcrumb dựa trên tham số `title` của trang. Bạn có thể tùy chỉnh bằng `linkTitle`.

Ví dụ nếu muốn breadcrumb hiển thị "Foo Bar" thay vì "Tổ chức file":

```yaml {filename="content/guide/organize-files.md"}
---
linkTitle: Foo Bar
title: Tổ chức file
---
```

Khi đó breadcrumb sẽ là:
```
Tài liệu > Hướng dẫn > Foo Bar
```

### Bật / tắt breadcrumb

Mặc định breadcrumb bật hay tắt cho một trang phụ thuộc [content type](https://gohugo.io/quick-reference/glossary/#content-type) và [page kind](https://gohugo.io/quick-reference/glossary/#page-kind):

| Content Type   | Section | Trang    |
|:---------------|:-------:|:--------|
| `docs`         | Bật     | Bật     |
| `blog`         | Tắt     | Bật     |
| Loại khác      | Tắt     | Tắt     |

Có thể ghi đè trên từng trang bằng tham số `breadcrumbs` trong front matter:

```yaml {filename="content/guide/organize-files.md"}
---
breadcrumbs: false
title: Tổ chức file
---
```

Bạn cũng có thể dùng [cascade](https://gohugo.io/content-management/front-matter/#cascade-1) để ghi đè mặc định cho trang và các trang con:

```yaml {filename="content/portfolio/_index.md"}
---
title: "Portfolio"

cascade:
  params:
    breadcrumbs: true
---
```

## Cấu hình thư mục nội dung

Mặc định Hugo dùng thư mục gốc `content/` để build site. Nếu cần dùng thư mục khác (ví dụ `docs/`), đặt tham số [`contentDir`](https://gohugo.io/getting-started/configuration/#contentdir) trong cấu hình site `hugo.yaml`.

## Thêm ảnh

Cách đơn giản nhất là đặt file ảnh cùng thư mục với file Markdown. Ví dụ thêm file `image.png` bên cạnh `my-page.md`:

{{< filetree/container >}}
  {{< filetree/folder name="content" >}}
    {{< filetree/folder name="docs" >}}
        {{< filetree/file name="my-page.md" >}}
        {{< filetree/file name="image.png" >}}
    {{< /filetree/folder >}}
  {{< /filetree/folder >}}
{{< /filetree/container >}}

Sau đó dùng cú pháp Markdown sau để chèn ảnh vào nội dung:

```markdown {filename="content/docs/my-page.md"}
![](image.png)
```

Bạn cũng có thể dùng tính năng [page bundles][page-bundles] của Hugo để đặt ảnh cùng file Markdown: chuyển `my-page.md` thành thư mục `my-page`, đặt nội dung vào `index.md` và đặt file ảnh trong thư mục `my-page`:

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

Hoặc đặt file ảnh trong thư mục `static` để ảnh dùng chung cho mọi trang:

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

Đường dẫn ảnh bắt đầu bằng `/` và tính từ thư mục static:

```markdown {filename="content/docs/my-page.md"}
![](/images/image.png)
```

[page-bundles]: https://gohugo.io/content-management/page-bundles/#leaf-bundles
