---
title: HTML designs
permalink: /guide/html-designs/
parent: Guide
nav_order: 6
---
# HTML designs

ZANACMS HTML designs live below `/layout/html/`. They use the generic HTML layout generator and do not need a PHP generator file.

## Directory structure

A minimal design directory looks like this:

```text
/layout/html/-owndesign/
├── design.html
└── zp.css
```

`design.html` is the HTML shell. `zp.css` is the design CSS loaded by ZANACMS. Images, JavaScript and additional CSS files can be placed in the same design directory and referenced from `design.html` or CSS.

For local custom designs, use a leading hyphen such as `-owndesign`. Supplied ZANACMS design directories can be replaced by updates; local hyphen-prefixed design directories are not used by supplied packages.

## Basic configuration

Select a design in `/__config/conf.php`:

```php
'layout'=>'html/z-airy',
```

A CSS variant can be selected after the design name:

```php
'layout'=>'html/z-airy.blue',
```

This keeps the same design directory and `design.html`, but loads the matching `zp<variant>.css` file instead of `zp.css`.

```text
html/z-airy              -> /layout/html/z-airy/zp.css
html/z-airy.blue         -> /layout/html/z-airy/zpblue.css
html/just-the-docs.dark  -> /layout/html/just-the-docs/zpdark.css
```

For generated variants, the target CSS is rebuilt from `zp.css` when the variant file is missing or older than `zp.css`. This applies to the supplied `html/just-the-docs.dark` and `html/z-airy.blue` variants. A deleted generated file is created again when that exact layout variant is selected and the layout directory is writable.

## Required and optional files

`design.html` is required.

`zp.css` is optional. HTML designs usually use it, while generator-based layouts may load other stylesheet files. `custom.css` is optional and is loaded after the layout CSS when the selected layout provides the normal ZANACMS CSS path; it is intended for local changes to a supplied design.

`design.ini` is optional. If the design provides editable sidebar content through `~~ZCOL2~~`, add:

```text
# number of columns (for editor)
columns=2
```

Only set `columns=2` when the design should accept editable sidebar content. Without `design.ini`, or without a `columns` entry, ZANACMS treats the design as `columns=1`. The settings page is linked from the admin dashboard when `adminexposure >= 3`; the sidebar field is shown only for designs with `columns=2` and when `col2` is not set directly in `/__config/conf.php`.

The same file can contain CSS variant replacements. Repeated `variant.<name>[]` lines are applied to `zp.css` to build `zp<name>.css`:

```text
variant.dark[]=#ffffff|#111111
variant.dark[]=#222222|#eeeeee
```

This builds a search list and a replacement list for the selected variant. Optional `zp<name>.extra.css` is appended after the replacements. For example:

```text
zp.css
+ design.ini variant.dark[] replacements
+ zpdark.extra.css if it exists
= zpdark.css
```

Only `zp.css` and the generated target CSS are compared for rebuilds. Changes to `design.ini` or `zp<name>.extra.css` do not rebuild the target automatically; delete the generated CSS or make `zp.css` newer. Without a selected variant, ZANACMS returns `zp.css` directly and does not read `design.ini` for CSS variant generation.

## HTML placeholders

The HTML generator replaces placeholders in `design.html`. Common placeholders are:

```text
~~ZHEAD~~
~~ZHEADLAST~~
~~ZBASEURL~~
~~ZDIRSCRIPTS~~
~~ZDIRDESIGN~~
~~ZSITETITLE~~
~~ZSITESUB~~
~~ZTITLE~~
~~ZHOMEURL~~
~~ZNAVI~~
~~ZNAVI_LI~~
~~ZH1~~
~~ZBODY~~
~~ZCOL2~~
~~ZPAGEIMG~~
~~ZFOOT~~
~~ZBODYLAST~~
```

`~~ZBODY~~` is the main page content. `~~ZPAGEIMG~~` outputs the normal page image. If `~~ZPAGEIMG~~` is not used, ZANACMS can insert the page image into the body automatically.

`~~ZCOL2~~` outputs sidebar content. The content can come from page data, `$GLOBALS['zconf']['col2']` or from the admin settings data in `_admconf.php`. A selected `col2img` is output before the sidebar text with the CSS class `col2img`.

## Layout image in HTML designs

HTML designs can use the `layoutimg` value from the configuration as a CSS background image. ZANACMS provides the following CSS variables for this:

```css
--z-layoutimg
--z-layoutimg-pos
```

Example in `__config/conf.php`:

```php
'layoutimg'=>['src'=>'img/header.jpg', 'pos'=>'center'],
```

An HTML design can use these values in its CSS file:

```css
#header {
	background-image:var(--z-layoutimg, url(default-header.jpg));
	background-position:var(--z-layoutimg-pos, center);
}
```

The second value in `var(...)` is the design default. It is used when no `layoutimg` is set in the configuration.

A design's default image should still be defined in the design CSS file. This allows each CSS variant to define its own default image, for example `zp.css` for the default variant and `zpblue.css` for the blue variant.

These CSS variables do not change the meaning of `~~ZLAYOUTIMG~~`. `~~ZLAYOUTIMG~~` remains an HTML image fragment for designs or generators that need to output an actual image in the HTML. The CSS variables are intended for HTML designs that use the layout image as a background image.

## Referencing files

In `design.html`, use `~~ZDIRDESIGN~~` for files from the current design directory. Use `~~ZDIRSCRIPTS~~` for shared script files below `/layout/_scripts/`. Use `~~ZBASEURL~~` only for resources outside the current design directory:

```html
<link rel="stylesheet" href="~~ZDIRDESIGN~~extra.css">
<script src="~~ZDIRDESIGN~~theme.js"></script>
```

In CSS files in the same directory, use normal relative CSS URLs:

```css
header {
	background-image:url(header.jpg);
}
```

Keep filesystem paths and URL paths separate. PHP code uses filesystem paths. HTML and CSS output use URL paths.

## Supplied Just the Docs design

The `html/just-the-docs` design includes a dark CSS variant:

```php
'layout'=>'html/just-the-docs.dark',
```

The Just the Docs base stylesheet is loaded separately by the template. ZANACMS-specific changes stay in `zp.css`. The generated dark variant is `zpdark.css`; optional extra rules are stored in `zpdark.extra.css`. The base stylesheet can be replaced from upstream again if needed.

## Supplied Z-Airy design

The package includes the preview design `html/z-airy` with a blue CSS variant `html/z-airy.blue`. The generated file is `zpblue.css` and can be recreated from `zp.css` and the `variant.blue[]` entries in `design.ini`. This design is included for preview and evaluation purposes. Use on a public or private website requires a valid design license.

## Related files

- [Design and CSS]({{ '/help/design-and-css/' | relative_url }})
- [Settings]({{ '/help/settings/' | relative_url }})
