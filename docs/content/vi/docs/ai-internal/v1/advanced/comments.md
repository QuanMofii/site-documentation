---
title: Hệ thống bình luận
linkTitle: Comments
---

Theme hỗ trợ thêm hệ thống bình luận vào site. Hiện hỗ trợ [giscus](https://giscus.app/).

<!--more-->

## giscus

[giscus](https://giscus.app/) là hệ thống bình luận chạy trên [GitHub Discussions](https://docs.github.com/en/discussions). Miễn phí và mã nguồn mở.

Để bật giscus, thêm vào file cấu hình site:

```yaml {filename="hugo.yaml"}
params:
  comments:
    enable: false
    type: giscus

    giscus:
      repo: <repository>
      repoId: <repository ID>
      category: <category>
      categoryId: <category ID>
```

Cấu hình giscus có thể tạo từ trang [giscus.app](https://giscus.app/). Xem thêm chi tiết tại đó.

Có thể bật hoặc tắt bình luận cho từng trang trong front matter:

```yaml {filename="content/docs/about.md"}
---
title: Giới thiệu
comments: true
---
```
