---
title: سازماندهی فایل‌ها
weight: 1
prev: /guide
---

## ساختار دایرکتوری

به‌طور پیش‌فرض، Hugo فایل‌های Markdown را در دایرکتوری `content` جستجو می‌کند و ساختار این دایرکتوری تعیین‌کننده ساختار نهایی خروجی وبسایت شماست.
این سایت را به عنوان مثال در نظر بگیرید:

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

هر یک از فایل‌های `_index.md` صفحه اصلی مربوط به بخش خود هستند. سایر فایل‌های Markdown صفحات معمولی هستند.

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

## طرح‌بندی‌ها

قالب سه طرح‌بندی برای انواع مختلف محتوا ارائه می‌دهد:

| طرح‌بندی   | دایرکتوری          | ویژگی‌ها                                                      |
| :-------- | :----------------- | :----------------------------------------------------------- |
| `docs`    | `content/docs/`    | مناسب برای مستندات ساختاریافته، مشابه این بخش.               |
| `blog`    | `content/blog/`    | برای پست‌های وبلاگ، با نمایش لیست و مقاله‌های تفصیلی.         |
| `default` | سایر دایرکتوری‌ها  | نمایش تک‌صفحه‌ای مقاله بدون نوار کناری.                      |

برای سفارشی‌سازی یک بخش به منظور تقلید رفتار یک طرح‌بندی داخلی، نوع مورد نظر را در front matter فایل `_index.md` بخش مشخص کنید.

```yaml {filename="content/my-docs/_index.md"}
---
title: مستندات من
cascade:
  type: docs
---
```

مثال پیکربندی بالا تضمین می‌کند که فایل‌های محتوا در `content/my-docs/` به‌طور پیش‌فرض به عنوان مستندات (نوع `docs`) در نظر گرفته می‌شوند.

## ناوبری نوار کناری

ناوبری نوار کناری به‌طور خودکار بر اساس سازماندهی محتوا به ترتیب الفبایی ایجاد می‌شود. برای پیکربندی دستی ترتیب نوار کناری، می‌توانیم از پارامتر `weight` در front matter فایل‌های Markdown استفاده کنیم.

```yaml {filename="content/guide/_index.md"}
---
title: راهنما
weight: 2
---
```

{{< callout type="info" >}}
  توصیه می‌شود نوار کناری را خیلی عمیق نگه ندارید. اگر محتوای زیادی دارید، **آن‌ها را به چند بخش تقسیم کنید**.
{{< /callout >}}

## ناوبری بخش

### ترتیب صفحه‌بندی بخش

ترتیب صفحات، که از طریق [`PAGE.PrevInSection`](https://gohugo.io/methods/page/previnsection/) و [`PAGE.NextInSection`](https://gohugo.io/methods/page/nextinsection/) در یک [مجموعه صفحه](https://gohugo.io/quick-reference/glossary/#page-collection) قابل دسترسی هستند، به‌طور پیش‌فرض معکوس شده است.

برای غیرفعال کردن این ترتیب معکوس، می‌توانید پارامتر سفارشی `reversePagination` را در front matter صفحه به `false` تنظیم کنید. به‌طور پیش‌فرض `reversePagination` روی `true` تنظیم شده است.

#### مثال

با توجه به ساختار دایرکتوری زیر:

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

و front matter زیر در پست‌ها:

```yaml {filename="content/blog/my-blog-series/post-a/index.md"}
---
title: پست A
weight: 1
---
```
```yaml {filename="content/blog/my-blog-series/post-b/index.md"}
---
title: پست B
weight: 2
---
```
```yaml {filename="content/blog/my-blog-series/post-c/index.md"}
---
title: پست C
weight: 3
---
```

اگر خواننده در انتهای `post-b/index.md` باشد، می‌بیند که صفحه بعدی `post-a` و صفحه قبلی `post-c` است. این به دلیل تنظیم `reversePagination` روی `true` به‌طور پیش‌فرض است. این زمانی مناسب است که بخواهیم پست‌ها به ترتیب زمانی از جدیدترین به قدیمی‌ترین نمایش داده شوند. اما در مورد یک سری وبلاگ که چندین بخش دارد، معمولاً می‌خواهیم افراد ابتدا پست اول را بخوانند، سپس به پست دوم و غیره بروند. بنابراین می‌خواهیم ترتیب معکوس را غیرفعال کنیم.

می‌توانیم `reversePagination` را در هر پست وبلاگ در این سری با اضافه کردن front matter زیر به `my-blog-series/_index.md` خاموش کنیم:

```yaml {filename="content/blog/my-blog-series/_index.md"}
---
title: سری وبلاگ من
cascade:
    params:
        reversePagination: false
---
```

در اینجا از [`cascade`](https://gohugo.io/content-management/front-matter/#cascade-1) استفاده می‌کنیم تا این تنظیم به تمام پست‌های `my-blog-series` منتقل شود و `reversePagination` برای تمام فرزندان روی `false` تنظیم شود. این اکنون تضمین می‌کند که وقتی خواننده در `post-b/index.md` است، صفحه بعدی `post-c` و صفحه قبلی `post-a` خواهد بود.

## ناوبری مسیر راهنما

مسیرهای راهنما به‌طور خودکار بر اساس ساختار دایرکتوری `/content` ایجاد می‌شوند.

به عنوان مثال، ساختار فایل [نمایش داده شده در بالا](#directory-structure) را در نظر بگیرید. با توجه به آن ساختار، مسیرهای راهنمای بالای صفحه در `/guide/organize-files/` به‌صورت خودکار به این شکل نمایش داده می‌شوند:

```
مستندات > راهنما > سازماندهی فایل‌ها
```

### سفارشی‌سازی عنوان لینک‌های مسیر راهنما

به‌طور پیش‌فرض، هر لینک مسیر راهنما بر اساس پارامتر `title` آن صفحه ایجاد می‌شود. می‌توانید این را با مشخص کردن `linkTitle` سفارشی کنید.

به عنوان مثال، اگر به جای `Organize Files` می‌خواستیم مسیر راهنما `Foo Bar` باشد:

```yaml {filename="content/guide/organize-files.md"}
---
linkTitle: Foo Bar
title: سازماندهی فایل‌ها
---
```

این اکنون مسیرهای راهنمای زیر را ایجاد می‌کند:
```
مستندات > راهنما > Foo Bar
```

### مخفی کردن مسیرهای راهنما

می‌توانید مسیرهای راهنما را به‌طور کامل از یک صفحه با مشخص کردن `breadcrumbs: false` در front matter آن مخفی کنید:

```yaml {filename="content/guide/organize-files.md"}
---
breadcrumbs: false
title: سازماندهی فایل‌ها
---
```

## پیکربندی دایرکتوری محتوا

به‌طور پیش‌فرض، دایرکتوری ریشه `content/` توسط Hugo برای ساخت سایت استفاده می‌شود.
اگر نیاز به استفاده از دایرکتوری دیگری برای محتوا دارید، مثلاً `docs/`، این کار را می‌توان با تنظیم پارامتر [`contentDir`](https://gohugo.io/getting-started/configuration/#contentdir) در پیکربندی سایت `hugo.yaml` انجام داد.

## معماری قالب در مقابل سایت

این پروژه از معماری دو دایرکتوری استفاده می‌کند که **فایل‌های قالب** (قابل استفاده مجدد در پروژه‌ها) را از **فایل‌های سایت** (مخصوص این سایت مستندات) جدا می‌کند.

### نمای کلی ساختار دایرکتوری

```
theme/                          # ریشه مخزن
├── layouts/                     # طرح‌بندی‌های قالب (قالب‌های پیش‌فرض)
├── assets/                      # دارایی‌های قالب (CSS، JS)
├── static/                      # فایل‌های استاتیک قالب
├── data/                        # فایل‌های داده قالب
├── i18n/                        # ترجمه‌های قالب
│
└── docs/                        # سایت نمونه/مستندات
    ├── content/                 # محتوای سایت (فایل‌های Markdown)
    ├── layouts/                 # بازنویسی طرح‌بندی سایت
    ├── assets/                  # دارایی‌های مخصوص سایت
    ├── static/                  # فایل‌های استاتیک مخصوص سایت
    ├── data/                    # داده‌های مخصوص سایت
    └── hugo.yaml                # پیکربندی سایت
```

### نحوه ترکیب فایل‌های قالب و سایت توسط Hugo

وقتی Hugo سایت را از `docs/` می‌سازد، از `--themesDir=../..` برای بارگذاری قالب از ریشه مخزن استفاده می‌کند. سپس Hugo فایل‌ها را از هر دو مکان **ادغام** می‌کند:

| دایرکتوری | قالب (ریشه) | سایت (docs/) | رفتار |
|:----------|:-----------|:-------------|:------|
| `layouts/` | قالب‌های پیش‌فرض | بازنویسی/گسترش | فایل‌های سایت با نام یکسان فایل‌های قالب را بازنویسی می‌کنند |
| `assets/` | CSS، JS قالب | دارایی‌های مخصوص سایت | هر دو در دسترس، سایت می‌تواند بازنویسی کند |
| `static/` | پیش‌فرض‌های قالب | فایل‌های مخصوص سایت | ادغام شده، فایل‌های سایت اولویت دارند |
| `data/` | داده‌های قالب | داده‌های سایت | بر اساس نام فایل ادغام می‌شوند |
| `content/` | (هیچ) | تمام محتوا | فقط در سایت وجود دارد |

### چه زمانی از کدام مکان استفاده کنیم

**دایرکتوری‌های قالب (سطح ریشه)** - برای کامپوننت‌های قابل استفاده مجدد:
- طرح‌بندی‌های پیش‌فرض که برای هر سایتی که از این قالب استفاده می‌کند کار می‌کنند
- دارایی‌های اصلی CSS/JS
- آیکون‌ها و تصاویر پیش‌فرض
- رشته‌های ترجمه (`i18n/`)

**دایرکتوری‌های سایت (`docs/`)** - برای محتوای مخصوص سایت:
- تمام فایل‌های محتوای Markdown
- شورت‌کدهای سفارشی مخصوص این سایت
- تصاویر و دارایی‌های مخصوص سایت
- بازنویسی پیکربندی

### مثال: بازنویسی طرح‌بندی

برای سفارشی‌سازی یک طرح‌بندی قالب برای سایت خود، آن را به همان مسیر در `docs/layouts/` کپی کنید:

{{< filetree/container >}}
  {{< filetree/folder name="layouts" >}}
    {{< filetree/folder name="partials" >}}
      {{< filetree/file name="footer.html" >}}
    {{< /filetree/folder >}}
  {{< /filetree/folder >}}
  {{< filetree/folder name="docs" >}}
    {{< filetree/folder name="layouts" >}}
      {{< filetree/folder name="partials" >}}
        {{< filetree/file name="footer.html" info="(بازنویسی قالب)" >}}
      {{< /filetree/folder >}}
    {{< /filetree/folder >}}
  {{< /filetree/folder >}}
{{< /filetree/container >}}

`docs/layouts/partials/footer.html` به جای نسخه قالب استفاده خواهد شد.

### فایل‌های تولید شده

دایرکتوری `docs/resources/_gen/` شامل دارایی‌های تولید/کش شده توسط Hugo است (تصاویر پردازش شده، CSS کامپایل شده). این دایرکتوری به‌طور خودکار تولید می‌شود و معمولاً باید در `.gitignore` قرار گیرد.

## ساختار چند محصول و نسخه

قالب از سازماندهی مستندات بر اساس **محصولات** و **نسخه‌ها** پشتیبانی می‌کند که برای کسب‌وکارهای با چندین محصول یا نرم‌افزار با چندین نسخه ایده‌آل است.

### ساختار محتوا برای محصولات و نسخه‌ها

```
content/
└── docs/
    ├── _index.md              # صفحه فرود محصولات
    ├── product-a/             # محصول A
    │   ├── _index.md
    │   ├── v1/                # نسخه ۱
    │   │   ├── _index.md
    │   │   ├── getting-started.md
    │   │   └── advanced/
    │   └── v2/                # نسخه ۲ (جدیدترین)
    │       ├── _index.md
    │       └── ...
    ├── product-b/             # محصول B
    │   └── v1/
    └── product-c/             # محصول C
        └── v1/
```

### پیکربندی منو

منوهای کشویی را در `hugo.yaml` پیکربندی کنید تا محصولات و نسخه‌ها نمایش داده شوند:

```yaml {filename="hugo.yaml"}
menu:
  main:
    # منوی کشویی محصولات (آیتم والد بدون pageRef)
    - identifier: products
      name: Products
      weight: 1
    # آیتم‌های فرزند محصول
    - identifier: product-a
      name: محصول A
      pageRef: /docs/product-a
      parent: products
      weight: 1
      params:
        icon: chip
    - identifier: product-b
      name: محصول B
      pageRef: /docs/product-b
      parent: products
      weight: 2
      params:
        icon: cloud

    # منوی کشویی نسخه‌ها
    - identifier: versions
      name: Versions
      weight: 2
    - identifier: ver-v2
      name: v2 (جدیدترین)
      pageRef: /docs/product-a/v2
      parent: versions
      weight: 1
    - identifier: ver-v1
      name: v1
      pageRef: /docs/product-a/v1
      parent: versions
      weight: 2
```

### نحوه کار

1. **منوی کشویی محصولات**: آیتم‌های منوی والد بدون `pageRef` به منوی کشویی تبدیل می‌شوند. آیتم‌های فرزند با `parent: products` در منوی کشویی نمایش داده می‌شوند.
2. **منوی کشویی نسخه‌ها**: همان الگو - نسخه‌های موجود برای ناوبری را نمایش می‌دهد.
3. **محدوده نوار کناری**: هنگام مشاهده مستندات یک محصول (مثلاً `/docs/product-a/v1/...`)، نوار کناری به‌طور خودکار فقط به محتوای آن محصول محدود می‌شود.

### اضافه کردن محصول جدید

{{< steps >}}

### ایجاد ساختار محتوا

```bash
mkdir -p content/docs/my-product/v1
```

### اضافه کردن فایل‌های ایندکس

ایجاد `content/docs/my-product/_index.md`:

```yaml
---
title: محصول من
---
```

ایجاد `content/docs/my-product/v1/_index.md`:

```yaml
---
title: محصول من v1
---

به مستندات محصول من خوش آمدید!
```

### اضافه کردن به منو

در `hugo.yaml`، به منوی products اضافه کنید:

```yaml
- identifier: my-product
  name: محصول من
  pageRef: /docs/my-product
  parent: products
  weight: 3
  params:
    icon: cube
```

{{< /steps >}}

### اضافه کردن نسخه جدید

{{< steps >}}

### ایجاد دایرکتوری نسخه

```bash
mkdir -p content/docs/my-product/v2
```

### کپی یا ایجاد محتوا

از نسخه موجود کپی کنید یا محتوای جدید ایجاد کنید.

### به‌روزرسانی منوی versions

```yaml
- identifier: ver-v2
  name: v2 (جدیدترین)
  pageRef: /docs/my-product/v2
  parent: versions
  weight: 1
```

{{< /steps >}}

## اضافه کردن تصاویر

برای اضافه کردن تصاویر، ساده‌ترین راه این است که فایل‌های تصویر را در همان دایرکتوری فایل Markdown قرار دهید.
به عنوان مثال، یک فایل تصویر `image.png` را در کنار فایل `my-page.md` اضافه کنید:

{{< filetree/container >}}
  {{< filetree/folder name="content" >}}
    {{< filetree/folder name="docs" >}}
        {{< filetree/file name="my-page.md" >}}
        {{< filetree/file name="image.png" >}}
    {{< /filetree/folder >}}
  {{< /filetree/folder >}}
{{< /filetree/container >}}

سپس می‌توانیم از سینتکس Markdown زیر برای اضافه کردن تصویر به محتوا استفاده کنیم:

```markdown {filename="content/docs/my-page.md"}
![](image.png)
```

همچنین می‌توانیم از ویژگی [page bundles][page-bundles] Hugo استفاده کنیم تا فایل‌های تصویر را همراه با فایل Markdown سازماندهی کنیم. برای این کار، فایل `my-page.md` را به یک دایرکتوری `my-page` تبدیل کنید و محتوا را در یک فایل به نام `index.md` قرار دهید و فایل‌های تصویر را داخل دایرکتوری `my-page` قرار دهید:

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

به‌عنوان جایگزین، می‌توانیم فایل‌های تصویر را در دایرکتوری `static` قرار دهیم، که تصاویر را برای تمام صفحات قابل دسترس می‌کند:

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

توجه کنید که مسیر تصویر با یک اسلش `/` شروع می‌شود و نسبت به دایرکتوری static است:

```markdown {filename="content/docs/my-page.md"}
![](/images/image.png)
```

[page-bundles]: https://gohugo.io/content-management/page-bundles/#leaf-bundles