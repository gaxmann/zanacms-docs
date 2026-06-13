---
title: Installation
permalink: /guide/installation/
nav_order: 3
---
# Installation

## 1. Download and upload ZANACMS

Download the ZANACMS archive.

Then use one of these two ways:

**Option a:** Unpack the archive on your computer and upload all files to your webspace.

**Option b:** Upload the archive to your webspace and unpack it there.

The ZANACMS files must be placed directly in the website directory. Do not leave an extra archive folder between the website directory and the ZANACMS files.

## 2. Choose the mode

ZANACMS supports three operating modes:

* **PHP mode** – uses PHP files
* **MD mode** – uses Markdown files or editor
* **Rich mode** – uses rich text editor

Use [**PHP mode**]({{ '/guide/php-mode/' | relative_url }}) if you want to write pages with PHP and arrays. Use [**MD mode**]({{ '/guide/markdown-mode/' | relative_url }}) if you want lightweight Markdown files with automatic cache building. Use [**Rich mode**]({{ '/guide/rich-mode/' | relative_url }}) if you want the normal visual editor workflow.

## 2a. Prepare PHP mode

Copy this file:

```text
/__config/conf.example-php.php
```

to ––>:

```text
/__config/conf.php
```

Copy this file:

```text
/index.example-php.php
```

to ––>:

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

to ––>:

```text
/__config/conf.php
```

Copy this file:

```text
/index.example-rich-or-md.php
```

to ––>:

```text
/index.php
```

Copy the files from:

```text
/pages/example/md/
```

to ––>:

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

to ––>:

```text
/__config/conf.php
```

Copy this file:

```text
/index.example-rich-or-md.php
```

to ––>:

```text
/index.php
```

Copy the files from:

```text
/pages/example/rich/
```

to ––>:

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

For the first setup, open a short temporary access window. Example:

```php
return [5,1,0];
```

The first number is the temporary access window time in minutes. The second number is the number of new device cookies that may be created during this temporary access window. The third number is the `adminexposure` level for newly created cookies. Use `0` as the third value to use the normal configured level.

Open the admin area in the browser that should get admin access:

```text
/admin/
```

Click **Get access**. This opens the access page.

Click **Activate this device now**.

After the allowed device cookies have been created, the temporary access window is closed automatically.

## 4. Open the admin area

Click the **Admin** link in the navigation and enjoy your admin access.

In MD and Rich mode, the `[e]` editor links are available in the footer of each web page.

In PHP mode, you have to edit the files via FTP, but can access the admin area via the `[i]` link in the footer that helps you upload images and copy PHP image code for your pages.
