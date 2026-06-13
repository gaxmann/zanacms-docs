---
title: Installation
permalink: /guide/installation/
parent: Guide
nav_order: 1
---
# Installation

## 1. Download and upload ZANACMS

Download the ZANACMS archive.

Then use one of these two ways:

**Option A:** Unpack the archive on your computer and upload all files to your webspace.

**Option B:** Upload the archive to your webspace and unpack it there.

The ZANACMS files must be placed directly in the website directory. Do not leave an extra archive folder between the website directory and the ZANACMS files.

## 2. Choose the mode

ZANACMS supports three operating modes:

* **PHP mode** uses PHP files for page content and page data.
* **MD mode** uses Markdown files and is a regular lightweight content mode.
* **Rich mode** uses page data files and the rich text editor.

Use [**PHP mode**]({{ '/guide/php-mode/' | relative_url }}) if you want to write pages with PHP and arrays. Use [**MD mode**]({{ '/guide/markdown-mode/' | relative_url }}) if you want lightweight Markdown files with automatic cache building. Use [**Rich mode**]({{ '/guide/rich-mode/' | relative_url }}) if you want the normal visual editor workflow.

## 2a. Prepare PHP mode

Copy this file:

```text
/__config/conf.example-php.php
```

to:

```text
/__config/conf.php
```

Copy this file:

```text
/index.example-php.php
```

to:

```text
/index.php
```

PHP mode does not need example page files below `/pages/`.

Open the website in the browser.

## 2b. Prepare MD mode

Copy this file:

```text
/__config/conf.example-md.php
```

to:

```text
/__config/conf.php
```

Copy this file:

```text
/index.example-rich-or-md.php
```

to:

```text
/index.php
```

Copy the files from:

```text
/pages/example/md/
```

to:

```text
/pages/
```

Copy the contents of the directory, not the directory itself.

Open the website in the browser.

## 2c. Prepare Rich mode

Copy this file:

```text
/__config/conf.example-rich.php
```

to:

```text
/__config/conf.php
```

Copy this file:

```text
/index.example-rich-or-md.php
```

to:

```text
/index.php
```

Copy the files from:

```text
/pages/example/rich/
```

to:

```text
/pages/
```

Copy the contents of the directory, not the directory itself.

Open the website in the browser.

## 3. Register the admin browser

Admin access is granted to single browsers or devices.

Open this file by FTP or in the file manager of your hosting account:

```text
/__config/EDITACCESS_ENABLE.php
```

For the first setup, open a short setup window. Example:

```php
return [5,1,0];
```

The first number is the access time in minutes. The second number is the number of new device cookies that may be created during this setup window. The third number is the `adminexposure` level for newly created cookies; `0` uses the normal configured level.

Open the admin area in the browser that should get admin access:

```text
/admin/
```

Click **Access management**. This opens Device management.

Click **Activate this device now**.

After the allowed device cookies have been created, the setup window is closed.

## 4. Open the admin area

Open the website in the registered browser.

Click the **Admin** link in the navigation.

In MD mode and Rich mode, the editor links are available after the current browser has admin access.

In PHP mode, the admin area is optional for runtime. It can still be used for media tools and device access.
