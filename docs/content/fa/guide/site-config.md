---
title: پیکربندی سایت
linkTitle: پیکربندی سایت
weight: 9
---

این راهنما نحوه پیکربندی این سایت مستندات برای کسب‌وکار یا پروژه شما را توضیح می‌دهد. تمام اطلاعات پروژه در یک فایل پیکربندی متمرکز شده است که تغییر برند و استقرار برای سازمان‌های مختلف را آسان می‌کند.

<!--more-->

## نمای کلی

هنگام استقرار این قالب مستندات برای یک کسب‌وکار جدید، باید متادیتای پروژه را به‌روزرسانی کنید:
- نام پروژه/محصول
- توضیحات و شعار
- URL مخزن GitHub
- لوگو و دارایی‌های برند
- لینک‌های تماس و شبکه‌های اجتماعی

همه این تنظیمات در **یک فایل** متمرکز شده‌اند: `hugo.yaml`.

## ساختار فایل پیکربندی

فایل `hugo.yaml` را باز کنید و بخش `params.project` را پیدا کنید. این **منبع واحد** برای تمام متادیتای پروژه است:

```yaml {filename="hugo.yaml"}
params:
  project:
    # هویت اصلی
    name: "نام پروژه شما"
    shortName: "NP"
    tagline: "شعار عالی شما"
    
    # توضیحات
    description: "توضیحات کامل پروژه برای SEO و متا تگ‌ها."
    shortDescription: "توضیحات کوتاه برای بخش‌های hero"
    
    # اطلاعات سازمان/نویسنده
    author: "نام شما"
    organization: "سازمان شما"
    email: "contact@example.com"
    
    # لینک‌ها
    website: "https://your-website.com"
    github: "https://github.com/your-org/your-repo"
    githubEditBase: "https://github.com/your-org/your-repo/edit/main/docs/content"
```

## راهنمای پیکربندی گام به گام

{{< steps >}}

### به‌روزرسانی تنظیمات پایه

در بالای `hugo.yaml`، به‌روزرسانی کنید:

```yaml {filename="hugo.yaml"}
baseURL: "https://docs.your-domain.com/"
title: "نام پروژه شما"
```

### به‌روزرسانی اطلاعات پروژه

در بخش `params.project`، تمام فیلدها را با اطلاعات کسب‌وکار خود به‌روزرسانی کنید.

### به‌روزرسانی عناوین زبان

برای هر زبان در بخش `languages`، `title` را به‌روزرسانی کنید.

### به‌روزرسانی لینک‌های منو

لینک GitHub را در منوی اصلی به‌روزرسانی کنید.

### جایگزینی دارایی‌های برند

فایل‌های زیر را در `static/images/` جایگزین کنید:

| فایل | هدف |
|------|-----|
| `logo.svg` | لوگوی حالت روشن |
| `logo-dark.svg` | لوگوی حالت تاریک |
| `favicon.ico` | فاویکون مرورگر |

### به‌روزرسانی محتوا

صفحات محتوای اصلی را به‌روزرسانی کنید:
- صفحه اصلی: `content/{lang}/_index.md`
- صفحه درباره ما: `content/{lang}/about/index.md`

{{< /steps >}}

## استفاده از اطلاعات پروژه در محتوا

می‌توانید اطلاعات پروژه را به صورت پویا در محتوا نمایش دهید:

```markdown
به {{</* project "name" */>}} خوش آمدید!

{{</* project "description" */>}}

نسخه فعلی: {{</* project "currentVersion" */>}}
```

## چک‌لیست پیکربندی

| مورد | مکان | وضعیت |
|------|------|-------|
| Base URL | `hugo.yaml` → `baseURL` | ☐ |
| عنوان سایت | `hugo.yaml` → `title` | ☐ |
| اطلاعات پروژه | `hugo.yaml` → `params.project.*` | ☐ |
| عناوین زبان | `hugo.yaml` → `languages.*.title` | ☐ |
| لینک GitHub منو | `hugo.yaml` → `menu.main` | ☐ |
| فایل‌های لوگو | `static/images/logo*.svg` | ☐ |

## مثال شروع سریع

پیکربندی حداقلی برای یک استقرار جدید:

```yaml {filename="hugo.yaml"}
baseURL: "https://docs.mybusiness.com/"
title: "مستندات کسب‌وکار من"

params:
  project:
    name: "کسب‌وکار من"
    shortName: "KB"
    description: "مستندات محصولات کسب‌وکار من"
    organization: "شرکت کسب‌وکار من"
    github: "https://github.com/mybusiness/docs"
    githubEditBase: "https://github.com/mybusiness/docs/edit/main/docs/content"
    website: "https://mybusiness.com"
    currentVersion: "v1.0"
```
