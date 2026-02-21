---
title: "Trang bổ sung"
weight: 1
prev: /docs/advanced
aliases:
  - /docs/advanced/glossary/
---

Theme cung cấp các trang bổ sung mà bạn có thể bật rõ ràng: trang thuật ngữ (glossary) và trang lưu trữ (archives).

<!--more-->

## Trang thuật ngữ (Glossary)

{{< callout type="info" >}}
  Để biết thêm về glossary tích hợp của Hugo, xem [Hugo Glossary Quick Reference](https://gohugo.io/quick-reference/glossary/).
{{< /callout >}}

### File dữ liệu nguồn

Định nghĩa thuật ngữ được lưu tập trung trong file dữ liệu `termbase.yaml` cho từng [ngôn ngữ được hỗ trợ](../multi-language/).

{{< filetree/container >}}
  {{< filetree/folder name="data" state="open" >}}
    {{< filetree/folder name="en" state="open" >}}
      {{< filetree/file name="termbase.yaml" >}}
    {{< /filetree/folder >}}
    {{< filetree/folder name="vi" state="open" >}}
      {{< filetree/file name="termbase.yaml" >}}
    {{< /filetree/folder >}}
    {{< filetree/folder name="ja" state="open" >}}
      {{< filetree/file name="termbase.yaml" >}}
    {{< /filetree/folder >}}
  {{< /filetree/folder >}}
{{< /filetree/container >}}

Mỗi file YAML chứa danh sách mục glossary. Mỗi mục gồm:

- `term`: Tên đầy đủ của khái niệm hoặc cụm từ.
- `definition`: Giải thích ngắn gọn về thuật ngữ.
- `abbr` (tùy chọn): Viết tắt hoặc từ viết tắt thường dùng.

```yaml {filename="data/vi/termbase.yaml"}
- term: seo
  abbr: SEO
  definition: "Tối ưu hóa công cụ tìm kiếm – cải thiện khả năng hiển thị trang web trên công cụ tìm kiếm"
- term: static site generator
  definition: "Phần mềm xử lý đầu vào văn bản để tạo ra các trang web tĩnh"
```

### Trang Glossary

Để hiển thị trang mục lục glossary (liệt kê tất cả thuật ngữ kèm mô tả và viết tắt),
cần có file nội dung glossary theo từng ngôn ngữ. Dùng hậu tố mã ngôn ngữ trong tên file,
ví dụ `content/glossary/_index.en.md`.

```markdown {filename="content/glossary/_index.vi.md"}
---
title: Thuật ngữ
layout: glossary
---
```

Xem ví dụ trang glossary tại [Thuật ngữ]({{% relref "/glossary" %}}).

## Lưu trữ (Archives)

Bạn có thể tạo trang lưu trữ theo thời gian (nhóm theo năm) cho các bài trong một section.

1. Tạo trang lưu trữ:
   ```yaml {filename="content/archives/_index.md"}
   ---
   title: Lưu trữ
   layout: archives
   toc: false
   ---
   ```
2. (Tùy chọn) Thêm vào menu chính:
   ```yaml {filename="hugo.yaml"}
   menu:
     main:
       - identifier: archives
         name: Lưu trữ
         pageRef: /archives
   ```
3. (Tùy chọn, đa ngôn ngữ) Thêm trang mục lục lưu trữ theo từng ngôn ngữ, cùng layout, ví dụ:
   - `content/archives/_index.fa.md`
   - `content/archives/_index.ja.md`
   - `content/archives/_index.vi.md`
4. (Tùy chọn) Đổi section nội dung dùng cho lưu trữ. Mặc định là `blog`.
   ```yaml {filename="hugo.yaml"}
   params:
     archives:
       section: blog
   ```
5. (Tùy chọn) Đổi định dạng ngày tháng mục lưu trữ. Mặc định là `Jan 02`.
   ```yaml {filename="hugo.yaml"}
   params:
     archives:
       dateFormat: "Jan 02"
   ```

Thông báo khi không có kết quả dùng key i18n `noResultsFound`.

Xem ví dụ trang lưu trữ tại [Lưu trữ]({{% relref "/archives" %}}).
