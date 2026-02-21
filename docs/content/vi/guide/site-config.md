---
title: Cấu hình Site
linkTitle: Cấu hình Site
weight: 9
---

Hướng dẫn này giải thích cách cấu hình trang tài liệu này cho doanh nghiệp hoặc dự án của bạn. Tất cả thông tin dự án được tập trung trong một file cấu hình duy nhất, giúp việc đổi thương hiệu và triển khai cho các tổ chức khác nhau trở nên dễ dàng.

<!--more-->

## Tổng quan

Khi triển khai template tài liệu này cho một doanh nghiệp mới, bạn cần cập nhật metadata dự án như:
- Tên dự án/sản phẩm
- Mô tả và slogan
- URL repository GitHub
- Logo và tài sản thương hiệu
- Liên hệ và mạng xã hội

Tất cả cài đặt này được tập trung trong **một file**: `hugo.yaml`.

## Cấu trúc File Cấu hình

Mở `hugo.yaml` và tìm phần `params.project`. Đây là **nguồn duy nhất** cho tất cả metadata dự án:

```yaml {filename="hugo.yaml"}
params:
  project:
    # Định danh cốt lõi
    name: "Tên Dự Án"                                 # Tên dự án/sản phẩm
    shortName: "TDA"                                  # Tên ngắn cho hiển thị gọn
    tagline: "Slogan Tuyệt Vời"                       # Slogan ngắn gọn
    
    # Mô tả
    description: "Mô tả đầy đủ về dự án cho SEO và meta tags."
    shortDescription: "Mô tả ngắn cho phần hero"
    
    # Thông tin Tổ chức/Tác giả
    author: "Tên Bạn"
    organization: "Tổ Chức Của Bạn"
    email: "lienhe@example.com"
    
    # Liên kết
    website: "https://website-cua-ban.com"
    github: "https://github.com/to-chuc/repo"
    githubEditBase: "https://github.com/to-chuc/repo/edit/main/docs/content"
    
    # Mạng xã hội (tùy chọn - để trống nếu không dùng)
    twitter: "https://twitter.com/taikhoan"
    discord: "https://discord.gg/server"
    
    # Thương hiệu
    logo: "images/logo.svg"
    logoDark: "images/logo-dark.svg"
    
    # Phiên bản
    currentVersion: "v1.0"
    
    # Giấy phép
    license: "MIT"
    licenseUrl: "https://github.com/to-chuc/repo/blob/main/LICENSE"
```

## Hướng dẫn Cấu hình Từng Bước

Làm theo các bước sau để cấu hình site cho doanh nghiệp của bạn:

{{< steps >}}

### Cập nhật Cài đặt Cơ bản

Ở đầu `hugo.yaml`, cập nhật:

```yaml {filename="hugo.yaml"}
baseURL: "https://docs.domain-cua-ban.com/"
title: "Tên Dự Án"
```

### Cập nhật Thông tin Dự án

Trong phần `params.project`, cập nhật tất cả các trường với thông tin doanh nghiệp:

| Trường | Mô tả | Ví dụ |
|--------|-------|-------|
| `name` | Tên đầy đủ dự án | "Sản Phẩm Của Tôi" |
| `shortName` | Tên viết tắt | "SPCT" |
| `tagline` | Slogan ngắn | "Xây dựng nhanh, triển khai thông minh" |
| `description` | Mô tả đầy đủ cho SEO | "Tài liệu hoàn chỉnh cho..." |
| `author` | Tên tác giả hoặc team | "DevTeam" |
| `organization` | Tên công ty/tổ chức | "Công ty ABC" |
| `github` | URL Repository | "https://github.com/abc/docs" |

### Cập nhật Tiêu đề Ngôn ngữ

Cho mỗi ngôn ngữ trong phần `languages`, cập nhật `title`:

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

### Cập nhật Liên kết Menu

Cập nhật liên kết GitHub trong menu chính:

```yaml {filename="hugo.yaml"}
menu:
  main:
    - identifier: github
      name: GitHub
      url: "https://github.com/to-chuc/repo"
      params:
        icon: github
```

### Cập nhật URL Chỉnh sửa

Cập nhật URL base cho tính năng "Chỉnh sửa trang này":

```yaml {filename="hugo.yaml"}
params:
  editURL:
    base: "https://github.com/to-chuc/repo/edit/main/docs/content"
```

### Thay thế Tài sản Thương hiệu

Thay thế các file trong `static/images/`:

| File | Mục đích | Kích thước khuyến nghị |
|------|----------|------------------------|
| `logo.svg` | Logo chế độ sáng | Cao: 32-40px |
| `logo-dark.svg` | Logo chế độ tối | Cao: 32-40px |
| `favicon.ico` | Favicon trình duyệt | 32x32px |

### Cấu hình Bình luận (Tùy chọn)

Nếu dùng Giscus cho bình luận, cập nhật cài đặt repository:

```yaml {filename="hugo.yaml"}
params:
  comments:
    enable: true
    type: giscus
    giscus:
      repo: to-chuc/repo
      repoId: YOUR_REPO_ID
      category: General
      categoryId: YOUR_CATEGORY_ID
```

Truy cập [giscus.app](https://giscus.app/) để tạo các giá trị này.

### Cập nhật Nội dung

Cập nhật các trang nội dung chính:
- Trang chủ: `content/{lang}/_index.md`
- Trang Giới thiệu: `content/{lang}/about/index.md`
- Cấu trúc tài liệu sản phẩm

{{< /steps >}}

## Sử dụng Thông tin Dự án trong Nội dung

Bạn có thể hiển thị động thông tin dự án trong nội dung bằng shortcodes:

### Hiển thị Giá trị Dự án

```markdown
Chào mừng đến với {{</* project "name" */>}}!

{{</* project "description" */>}}

Phiên bản hiện tại: {{</* project "currentVersion" */>}}
```

**Các trường có sẵn:**
- `name`, `shortName`, `tagline`
- `description`, `shortDescription`
- `author`, `organization`, `email`
- `website`, `github`
- `currentVersion`, `license`, `licenseUrl`
- `twitter`, `discord`

### Tạo Liên kết

```markdown
Xem {{</* project-link "github" */>}}

Truy cập {{</* project-link "website" "website chính thức" */>}}

Được cấp phép theo {{</* project-link "license" */>}}
```

## Danh sách Kiểm tra Cấu hình

Sử dụng danh sách này khi thiết lập cho doanh nghiệp mới:

| Mục | Vị trí | Trạng thái |
|-----|--------|------------|
| Base URL | `hugo.yaml` → `baseURL` | ☐ |
| Tiêu đề site | `hugo.yaml` → `title` | ☐ |
| Thông tin dự án | `hugo.yaml` → `params.project.*` | ☐ |
| Tiêu đề ngôn ngữ | `hugo.yaml` → `languages.*.title` | ☐ |
| Link GitHub menu | `hugo.yaml` → `menu.main` | ☐ |
| URL chỉnh sửa | `hugo.yaml` → `params.editURL.base` | ☐ |
| File logo | `static/images/logo*.svg` | ☐ |
| Favicon | `static/images/favicon.ico` | ☐ |
| Nội dung trang chủ | `content/*/\_index.md` | ☐ |
| Trang Giới thiệu | `content/*/about/index.md` | ☐ |
| Cấu hình Giscus (nếu dùng) | `hugo.yaml` → `params.comments.giscus` | ☐ |

## Các File Cần Cập nhật Thủ công

Một số file không thể dùng cấu hình động và phải cập nhật thủ công:

| File | Cần thay đổi gì |
|------|-----------------|
| `go.mod` | Đường dẫn module (nếu dùng Hugo Modules) |
| `README.md` | Mô tả dự án và badges |
| `LICENSE` | Nội dung giấy phép nếu thay đổi loại |
| Front matter nội dung | Tiêu đề trang chứa tên dự án |
| Thông báo banner | `hugo.yaml` → `params.banner.message` và banner theo ngôn ngữ |

## Ví dụ Bắt đầu Nhanh

Cấu hình tối thiểu cho một triển khai mới:

```yaml {filename="hugo.yaml"}
baseURL: "https://docs.doanhnghiep.com/"
title: "Tài liệu Doanh nghiệp"

params:
  project:
    name: "Doanh Nghiệp"
    shortName: "DN"
    description: "Tài liệu cho các sản phẩm Doanh Nghiệp"
    organization: "Công ty Doanh Nghiệp"
    github: "https://github.com/doanhnghiep/docs"
    githubEditBase: "https://github.com/doanhnghiep/docs/edit/main/docs/content"
    website: "https://doanhnghiep.com"
    currentVersion: "v1.0"
```

Sau đó cập nhật:
1. URL link GitHub trong menu
2. File logo trong `static/images/`
3. Nội dung trang chủ và trang Giới thiệu

## Mẹo cho Triển khai Đa Doanh nghiệp

1. **Duy trì template cơ sở** - Giữ một phiên bản sạch không có nội dung riêng của doanh nghiệp
2. **Sử dụng Git branches** - Tạo branch riêng cho các triển khai khác nhau
3. **Ghi chú các thay đổi** - Lưu lại những gì đã tùy chỉnh cho mỗi triển khai
4. **Tự động hóa thiết lập** - Cân nhắc tạo script setup nhắc nhập thông tin dự án
