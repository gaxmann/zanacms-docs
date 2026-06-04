---
title: "Design and CSS"
permalink: /help/design-and-css/
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

Both files are loaded after the supplied layout CSS and are not overwritten by updates. Do not change core CSS files such as `zp.css` or `zpsuper.css` directly, as they can be overwritten during an update.

## Own HTML design

To create a completely custom design, add a new design folder, for example:

```text
/layout/html/-owndesign/
```

A custom design folder is yours entirely. Because you are not modifying an existing supplied design, you do not need `custom.css` to protect your changes from updates — the whole design directory is already separate.

## More details

- [Configuration]({{ '/help/configuration/' | relative_url }})
- [CSS customisation]({{ '/guide/interfaces/' | relative_url }}#10-css-customisation)
- [HTML layouts]({{ '/guide/interfaces/' | relative_url }}#11-html-layouts)
- [HTML layout readme](https://github.com/gaxmann/zanacms/blob/master/layout/html/README.md)
