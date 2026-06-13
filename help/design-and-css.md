---
title: "Design and CSS"
permalink: /help/design-and-css/
parent: Help
nav_order: 14
---
# Design and CSS

ZANACMS separates website content from layout and CSS. This makes it possible to update system files without losing design customisations.

## Safe CSS changes

Global custom CSS — applied across all layouts — can be placed in:

```text
/layout/supercustom.css
```

Layout-specific custom CSS can be placed in:

```text
/layout/<family>/<design>/custom.css
```

Both files are loaded after the supplied layout CSS and are not overwritten by updates. Do not change core CSS files such as `zp.css` or `zpsuper.css` directly, as they can be overwritten during an update. `zp.css` is not required for every layout family; generator-based layouts may use their own stylesheet files.

## Editor classes and editor CSS

Optional editor classes can be configured in `/__config/customadmin.php`:

```php
<?php

return [
	'editorclasses'=>[
		'lead'=>'Lead',
		'small'=>['en'=>'Small', 'de'=>'Klein'],
	],
	'editor_layout_css'=>'supercustom.css',
];
```

When `editorclasses` is set, these classes replace the visible class entries in the editor toolbar; the remove option remains. In Rich mode, the cleaner still removes unknown pasted classes, but keeps the configured classes, built-in ZANACMS classes, alignment classes and classes beginning with `zp`.

`editor_layout_css` is optional and relative to `/layout/`. Use it when the editor should load extra CSS for previewing those classes in the admin area.

## Supplied HTML designs

Supplied HTML designs and selectable CSS variants currently include `html/water`, `html/just-the-docs`, `html/just-the-docs.dark`, `html/z-airy` and `html/z-airy.blue`. They use the generic HTML layout generator and a `design.html` template under `/layout/html/<design>/`.

Generated supplied variants such as `zpdark.css` and `zpblue.css` can be deleted and are recreated when the matching layout variant is selected, if the design directory is writable. The source is `zp.css` plus the matching `variant.<name>[]` entries in `design.ini`; optional `zp<name>.extra.css` is appended when it exists.

The supplied `html/z-airy` design is included for preview and evaluation purposes. Use on a public or private website requires a valid design license.


## Sidebar layouts

HTML layouts can use `~~ZCOL2~~` for a sidebar. The admin settings page sidebar field is shown only when the active design has `design.ini` with `columns=2` and `col2` is not set directly in `/__config/conf.php`. Only set `columns=2` when the design should accept editable sidebar content; the default is `columns=1`. If a sidebar image is selected in the editor, ZANACMS renders it before the sidebar text with class `col2img`. The selected image applies to all language versions of the sidebar.

The supplied global CSS contains:

```css
.col2img {
	width:100%;
	height:auto;
}
```

Use `/layout/supercustom.css` or the layout `custom.css` file for local visual adjustments. The normal `pageimg` remains separate from `col2img`.

## Own HTML design

To create a completely custom design, add a new design folder, for example:

```text
/layout/html/-owndesign/
```

A custom design folder is yours entirely. Because you are not modifying an existing supplied design, you do not need `custom.css` to protect your changes from updates — the whole design directory is already separate.

## More details

- [Configuration]({{ '/help/configuration/' | relative_url }})
- [CSS customisation]({{ '/guide/interfaces/' | relative_url }}#10-css-customisation)
- [Settings]({{ '/help/settings/' | relative_url }})
- [HTML designs]({{ '/guide/html-designs/' | relative_url }})
- [HTML layouts]({{ '/guide/interfaces/' | relative_url }}#11-html-layouts)
- [HTML layout readme](https://github.com/gaxmann/zanacms/blob/master/layout/html/README.md)
