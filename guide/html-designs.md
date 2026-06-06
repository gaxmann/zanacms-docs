---
title: HTML designs
permalink: /guide/html-designs/
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

This keeps the same design directory and `design.html`, but loads `zpblue.css` instead of `zp.css`.

```text
html/z-airy       -> /layout/html/z-airy/zp.css
html/z-airy.blue  -> /layout/html/z-airy/zpblue.css
```

## Required and optional files

`design.html` is required.

`zp.css` is optional technically, but most designs need it. `custom.css` is optional and is loaded after `zp.css`; it is intended for local changes to a supplied design.

`design.ini` is optional. If the design provides editable sidebar content through `~~ZCOL2~~`, add:

```text
# number of columns (for editor)
columns=2
```

Only set `columns=2` when the design should accept editable sidebar content. Without `design.ini`, or without a `columns` entry, ZANACMS treats the design as `columns=1` and does not show the sidebar editor in the admin menu.

## HTML placeholders

The HTML generator replaces placeholders in `design.html`. Common placeholders are:

```text
~~ZHEAD~~
~~ZHEADLAST~~
~~ZBASEURL~~
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

`~~ZCOL2~~` outputs sidebar content. The content can come from `$GLOBALS['zconf']['col2']` or from the sidebar editor files. A selected `col2img` is output before the sidebar text with the CSS class `col2img`.

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

In `design.html`, use `~~ZBASEURL~~` for local files:

```html
<link rel="stylesheet" href="~~ZBASEURL~~layout/html/-owndesign/extra.css">
<script src="~~ZBASEURL~~layout/html/-owndesign/theme.js"></script>
```

In CSS files in the same directory, use normal relative CSS URLs:

```css
header {
	background-image:url(header.jpg);
}
```

Keep filesystem paths and URL paths separate. PHP code uses filesystem paths. HTML and CSS output use URL paths.

## Supplied Z-Airy design

The package includes the preview design `html/z-airy` with a blue CSS variant `html/z-airy.blue`. This design is included for preview and evaluation purposes. Use on a public or private website requires a valid design license.

## Related files

- [Design and CSS]({{ '/help/design-and-css/' | relative_url }})
- [Sidebar editor]({{ '/help/sidebar-editor/' | relative_url }})
