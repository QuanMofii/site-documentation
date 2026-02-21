---
title: Tùy biến theme
linkTitle: Customization
---

Theme cung cấp một số tùy chọn tùy biến mặc định trong file cấu hình `hugo.yaml`. Trang này mô tả các tùy chọn có sẵn và cách tùy biến theme thêm.

<!--more-->

## CSS tùy chỉnh

Để thêm CSS tùy chỉnh, tạo file `assets/css/custom.css` trong site. Theme sẽ tự động tải file này.

### Font chữ

Có thể tùy chỉnh font chữ nội dung bằng:

```css {filename="assets/css/custom.css"}
.content {
  font-family: "Times New Roman", Times, serif;
}
```

### Thẻ mã nội dòng

Màu chữ xen với `mã nội dòng` có thể tùy chỉnh bằng:

```css {filename="assets/css/custom.css"}
.content code:not(.code-block code) {
  color: #c97c2e;
}
```

### Màu chủ đạo

Màu chủ đạo của theme có thể tùy chỉnh qua các biến `--primary-hue`, `--primary-saturation` và `--primary-lightness`:

```css {filename="assets/css/custom.css"}
:root {
  --primary-hue: 100deg;
  --primary-saturation: 90%;
  --primary-lightness: 50%;
}
```

### Biến bố cục thành phần

Theme cung cấp biến CSS để tùy chỉnh độ rộng trang, navbar và footer:

```css {filename="assets/css/custom.css"}
:root {
  /* Độ rộng trang - cũng cấu hình được qua hugo.yaml params.page.width */
  --hextra-max-page-width: 80rem; /* mặc định: 80rem (normal), 90rem (wide), 100% (full) */

  /* Độ rộng navbar - cũng cấu hình được qua hugo.yaml params.navbar.width */
  --hextra-max-navbar-width: 90rem;

  /* Độ rộng footer - cũng cấu hình được qua hugo.yaml params.footer.width */
  --hextra-max-footer-width: 80rem;
}
```

### Biến theme Tailwind

Từ theme v0.10.0 (xây trên Tailwind CSS v4), bạn có thể tùy chỉnh theme bằng cách ghi đè biến CSS trong khối `@layer theme`. Nhờ vậy có thể đổi giao diện toàn cục mà không cần sửa từng class.

```css {filename="assets/css/custom.css"}
@layer theme {
  :root {
    --hx-default-mono-font-family: "JetBrains Mono", monospace;
  }
}
```

Chi tiết xem [Tài liệu Tailwind Theme Variables](https://tailwindcss.com/docs/theme#default-theme-variable-reference).

### Tùy biến theme thêm

Có thể tùy biến thêm bằng cách ghi đè style mặc định qua các class CSS được expose. Ví dụ tùy chỉnh phần footer:

```css {filename="assets/css/custom.css"}
.hextra-footer {
  /* Style áp dụng cho phần footer */
}

.hextra-footer:is(html[class~="dark"] *) {
  /* Style cho footer ở chế độ tối */
}
```

Các class sau dùng để tùy chỉnh các phần của theme.

#### Chung

- `hextra-scrollbar` – Thanh cuộn
- `content` – Khung nội dung trang

#### Shortcodes

##### Badge

- `hextra-badge` – Thẻ badge

##### Card

- `hextra-card` – Thẻ card
- `hextra-card-image` – Ảnh trong card
- `hextra-card-icon` – Icon trong card
- `hextra-card-subtitle` – Phụ đề card

##### Cards

- `hextra-cards` – Khung lưới cards

##### Jupyter Notebook

- `hextra-jupyter-code-cell` – Khung ô mã Jupyter
- `hextra-jupyter-code-cell-outputs-container` – Khung chứa output ô mã
- `hextra-jupyter-code-cell-outputs` – Phần tử div output

##### PDF

- `hextra-pdf` – Khung chứa PDF

##### Steps

- `hextra-steps` – Khung chứa các bước

##### Tabs

- `hextra-tabs-panel` – Khung panel tab
- `hextra-tabs-toggle` – Nút chuyển tab

##### Filetree

- `hextra-filetree` – Khung filetree

##### Folder

- `hextra-filetree-folder` – Khung thư mục trong filetree

#### Navbar

- `hextra-nav-container` – Khung navbar
- `hextra-nav-container-blur` – Lớp blur navbar
- `hextra-hamburger-menu` – Nút menu hamburger

#### Footer

- `hextra-footer` – Phần footer
- `hextra-custom-footer` – Khung section footer tùy chỉnh

#### Tìm kiếm

- `hextra-search-wrapper` – Khung bọc ô tìm kiếm
- `hextra-search-input` – Ô nhập tìm kiếm
- `hextra-search-results` – Danh sách kết quả

Class lồng nhau tùy chọn trong giao diện tìm kiếm:

- `hextra-search-title` – Tiêu đề kết quả
- `hextra-search-active` – Liên kết kết quả đang active
- `hextra-search-no-result` – Trạng thái không có kết quả
- `hextra-search-prefix` – Nhãn breadcrumb/prefix cho nhóm kết quả
- `hextra-search-excerpt` – Đoạn trích kết quả
- `hextra-search-match` – Đoạn query được tô sáng

#### Mục lục

- `hextra-toc` – Khung mục lục

#### Sidebar

- `hextra-sidebar-container` – Khung sidebar
- `hextra-sidebar-active-item` – Mục đang active trong sidebar

#### Chuyển ngôn ngữ

- `hextra-language-switcher` – Nút chuyển ngôn ngữ
- `hextra-language-options` – Khung tùy chọn ngôn ngữ

#### Chuyển theme

- `hextra-theme-toggle` – Nút chuyển theme

#### Nút sao chép mã

- `hextra-code-copy-btn-container` – Khung nút sao chép
- `hextra-code-copy-btn` – Nút sao chép
- `hextra-copy-icon` – Icon sao chép
- `hextra-success-icon` – Icon thành công

#### Khối mã

- `hextra-code-block` – Khung khối mã
- `hextra-code-filename` – Phần tử tên file của khối mã

#### Feature Card

- `hextra-feature-card` – Liên kết feature card

#### Feature Grid

- `hextra-feature-grid` – Khung lưới feature

### Tô sáng cú pháp

Danh sách theme tô sáng cú pháp tại [Chroma Styles Gallery](https://xyproto.github.io/splash/docs/all.html). Có thể tạo file style bằng lệnh:

```shell
hugo gen chromastyles --style=github
```

Để ghi đè theme tô sáng mặc định, thêm style đã tạo vào file CSS tùy chỉnh.

## Script tùy chỉnh

Bạn có thể thêm script tùy chỉnh vào cuối thẻ head của mỗi trang bằng cách tạo file:

```
layouts/_partials/custom/head-end.html
```

## Section thêm trong footer

Có thể thêm section bổ sung vào footer bằng cách tạo file `layouts/_partials/custom/footer.html` trong site.

```html {filename="layouts/_partials/custom/footer.html"}
<!-- Nội dung footer của bạn -->
```

Section thêm sẽ được chèn trước phần copyright trong footer. Bạn có thể dùng [HTML](https://developer.mozilla.org/en-US/docs/Web/HTML) và [cú pháp template Hugo](https://gohugo.io/templates/) để thêm nội dung.

Biến Hugo dùng được trong footer: `.switchesVisible` và `.displayCopyright`.

## Layout tùy chỉnh

Layout của theme có thể ghi đè bằng cách tạo file cùng tên trong thư mục `layouts` của site. Ví dụ để ghi đè layout `single.html` cho docs, tạo file `layouts/docs/single.html`.

Tham khảo [Hugo Templates][hugo-template-docs] để biết thêm.

## Tùy biến thêm

Không tìm thấy nội dung cần? Hãy [mở discussion](https://github.com/imfing/hextra/discussions) hoặc đóng góp cho theme!

[hugo-template-docs]: https://gohugo.io/templates/
