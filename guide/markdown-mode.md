---
title: "ZANACMS Guide for MD Mode"
permalink: /guide/markdown-mode/
---
# ZANACMS Guide for MD Mode

MD mode is the Markdown-based content mode of ZANACMS. It is for users who want to edit page files as Markdown, either directly or through the optional built-in Markdown editor. Editor-oriented Rich sites should use Rich mode; see [`guide/rich-mode.md`]({{ '/guide/rich-mode/' | relative_url }}).

Shared setup topics such as layouts, languages, footer syntax, CSS and media access are in [`guide/index.md`]({{ '/guide/' | relative_url }}). Public interface details, including ZTOKENS, page variables and footer tokens, are in [`guide/interfaces.md`]({{ '/guide/interfaces/' | relative_url }}).


### MD additions/extensions for ZANACMS

MD mode is normal Markdown with MD additions/extensions for ZANACMS: internal page links use `@page`, and relative images from `/img` use a compact media syntax. This affects relative image URLs such as `img/file.jpg`; image URLs starting with `/`, `http://`, `https://` or `~~ZBASEURL~~` keep normal Markdown image behaviour.

For relative `/img` images, the text in the brackets is not the normal Markdown alt text. It is read as `class|alt`:

```md
![](img/file.jpg)
![|Alternative text](img/file.jpg)
![alignright](img/file.jpg)
![alignleft|Alternative text](img/file.jpg)
![aligncenter|Alternative text](img/file.jpg)
```

Empty brackets mean no image class and an empty `alt` attribute. Text before `|` becomes the image class. Text after `|` becomes `alt`. Therefore `![Alternative text](img/file.jpg)` is not the right form for a relative ZANACMS media image; it would be treated as a class value, not as alt text. Use `![|Alternative text](img/file.jpg)` when only alt text is needed. Images wider than the configured small image width are reduced proportionally; smaller images keep their real width and height. If a larger image exists in `/img/big`, or if the selected `/img` image itself is larger than the small output size, ZANACMS adds a link to the larger image.

## 1. File structure for MD mode

A minimal MD mode website has this structure:

```text
/
├── __config/
│   └── conf.php
├── index.php
├── pages/
│   ├── __NAVIGATION.txt
│   ├── __col2.en.md          optional sidebar
│   ├── index.en.md
│   ├── index.de.md
│   ├── contact.en.md
│   ├── contact.de.md
│   ├── legalnotice.en.md
│   └── privacypolicy.en.md
├── img/
├── layout/
├── zp/
├── admin/                  optional editor/media tools
└── zpcache/
```

`pages` contains Markdown files, the navigation source file and optional sidebar files such as `/pages/__col2.en.md`. The bundled starter files are in `/pages/example/md/`; copy the contents of that directory into `/pages/`, not the directory itself. After copying, the files should be placed directly as `/pages/index.en.md`, `/pages/contact.en.md` and `/pages/__NAVIGATION.txt`. `zpcache` contains generated MD cache files and should not be edited manually. `/admin/` is only needed when the built-in editor or media tools are used; it can be removed when Markdown files are edited directly, for example via FTP.

## 2. Minimal index.php

```php
<?php

include_once __DIR__.'/zp/zana.php';
out_page();

```

## 3. Minimal `/__config/conf.php`

```php
<?php

$GLOBALS['zconf']=[
	// 'adminexposure'=>1, // 1..3
	'mode'=>'md',
	'layout'=>'html/water',
	// 'urlrewrite'=>['/', true],
	'stdlang'=>'en',
	// 'col2'=>'<p>[col2]</p>',
	'foot'=>[
		"[[@legalnotice]] • [[@privacypolicy]]",
		"<span style='font-size:0.65em'>[footertext]</span>",
	],
	'favicon'=>'',
];

$GLOBALS['zlangs']=[
	'en'=>[
		'sitetitle'=>'ZANACMS Example',
		'sitesub'=>'Minimal Markdown website',
		'vars'=>[
			'legalnotice'=>'Legal notice',
			'privacypolicy'=>'Privacy policy',
			'footertext'=>'Powered by ZANACMS',
			'col2'=>'Optional sidebar.',
		],
	],
	'de'=>[
		'sitetitle'=>'ZANACMS Beispiel',
		'sitesub'=>'Minimale md-Webpräsenz',
		'vars'=>[
			'legalnotice'=>'Impressum',
			'privacypolicy'=>'Datenschutz',
			'footertext'=>'Erzeugt mit ZANACMS',
			'col2'=>'Optionale Seitenleiste.',
		],
	],
];

```

## 4. Markdown page files

MD page files use this naming pattern:

```text
/pages/pagename.language.md
```

Examples:

```text
/pages/index.en.md
/pages/index.de.md
/pages/contact.en.md
/pages/contact.de.md
/pages/legalnotice.en.md
/pages/privacypolicy.en.md
```

A simple page:

```md
# Info

This is a Markdown page.

[Contact page](@contact)
[Legal notice](@legalnotice)
```

The first level-one heading is used as the main heading if no header field overrides it. The language code in the filename is the page language. These files also define the local languages of that page.

## 5. Markdown syntax

Normal Markdown can be used:

```md
# Main heading

## Subheading

**Bold text**

*Italic text*

- List item
- List item

1. First item
2. Second item

[External link](https://example.com)

![Image text](/img/image.jpg)
```

HTML can also be used inside Markdown if needed. Relative images such as `![](img/example.jpg)`, `![|Alt text](img/example.jpg)` or `![alignright|Alt text](img/example.jpg)` are rendered as ZANACMS media images with the configured small image width and an optional link to the larger image when available. For these relative images the bracket text is `class|alt`, not normal Markdown alt text. Images starting with `/`, `http://`, `https://` or `~~ZBASEURL~~` keep normal Markdown image behaviour.

## 6. Optional page header

A Markdown page may start with an optional header:

```md
---
title: Privacy policy
noindex: true
layoutimg.src: img/header.jpg
layoutimg.pos: center
---
# Privacy policy

Text...
```

Supported fields:

```text
title
h1
noindex
layoutimg.*
pageimg.src
mode
```

`mode: php` loads `/pages/PAGEID.php` as a page data fragment while the website itself remains in MD mode. The PHP fragment must only set page data; it must not call `out_page()`.


## Sidebar in MD mode

If the active HTML layout contains `~~ZCOL2~~`, the active design must define `columns=2` in `design.ini`, and `$GLOBALS['zconf']['col2']` is not set, the sidebar editor stores one Markdown file per language:

```text
/pages/__col2.en.md
/pages/__col2.de.md
```

These files contain only sidebar content. They do not support a page title or page H1. The optional sidebar image is edited once as `col2img.src`, written consistently to the sidebar files, and rendered before the text as `.col2img` in all languages.

## 7. Navigation file

MD navigation is stored in:

```text
/pages/__NAVIGATION.txt
```

Example:

```text
# ZANACMS navigation
# @pagename | langcode:text (*:Text --> Text is valid for all languages)

@index | de:Start | en:Home
> @legalnotice | de:Impressum | en:Legal notice
> @privacypolicy | de:Datenschutz | en:Privacy policy
@contact | de:Kontakt | en:Contact
```

Lines starting with `#` are comments. `>` creates a sub level. `*:` can be used for shared text according to the navigation parser rules.

The navigation editor uses this text format in MD and Rich mode. MD mode keeps `/pages/__NAVIGATION.txt` as its source file. Rich mode stores the parsed result in `/pages/__navi.data.php`.

## 8. Internal links

Normal Markdown links work:

```md
[Open website](https://example.com)
```

Internal ZP page links use `@page` as the Markdown link target:

```md
[Contact](@contact)
[Contact with parameter](@contact?value=4)
```

The same internal-link idea is used in Rich mode and is described in more detail in [`guide/rich-mode.md`]({{ '/guide/rich-mode/' | relative_url }}#8-internal-links).

## 9. Variables

MD mode can use page variables:

```md
[VAR:contact_note]
```

Variables are defined in:

```text
/pages/zvars.php
```

They are intended for prepared reusable values or generated output. A PHP developer can provide the variable, and a normal editor user can insert it without writing PHP. More detail is in [`guide/interfaces.md`]({{ '/guide/interfaces/' | relative_url }}#8-page-variables).

The central array is `$ZVARS_PAGES`:

```php
$ZVARS_PAGES=[
	'contact_note'=>['en'=>'Contact note','de'=>'Kontakt-Hinweis'],
	'example_box'=>'Example box',
];
```

The output function is:

```php
function zvar_get($key, $lg) {
	switch ($key) {
		case 'contact_note':
			return '<strong>Contact:</strong> Add your custom PHP output here.';
	}
	return '';
}
```

See [`guide/rich-mode.md`]({{ '/guide/rich-mode/' | relative_url }}#9-variables) for the common variable rules.

## 10. Footer

Footer lines are configured in `/__config/conf.php`:

```php
'foot'=>[
	"[[@legalnotice]] • [[@privacypolicy]]",
	"<span style='font-size:0.65em'>[footertext]</span>",
],
```

Footer text variables are stored in `$GLOBALS['zlangs'][language]['vars']`. Footer text is resolved by the common footer-token logic, not by Markdown link syntax. Use `[[@page]]` for a complete footer link, or `<a href="[@page]">Text</a>` when the footer text should provide its own link label.

## 11. URL rewrite

URL rewrite is optional and off by default. With `urlrewrite`, internal MD links such as `[Contact](@contact)` use the configured URL base. With rewrite enabled they are delivered as URLs such as `/en/contact`. The cached MD page output still applies the current language, base path and `lay` design selector when the page is delivered.

## 12. Cache

MD mode uses generated cache files in:

```text
/zpcache
```

ZANACMS tracks which languages exist for each MD page. Official website languages come from `$GLOBALS['zlangs']`. Additional MD files outside the official website languages add their language only on that page. If a non-official language is requested and the page does not have that language, MD delivers `fallbacklg` or `stdlang`; canonical and further internal links use that delivered language. If an official language is requested but the current page is missing in that language, MD still keeps further internal links in the requested official language, while canonical points to the delivered fallback language.

The cache exists so Markdown files do not need to be parsed on every page request. ZP rebuilds cache files when the Markdown source file, `__NAVIGATION.txt` or `/__config/conf.php` has changed, or when a required cache file is missing. This also applies when Markdown files are uploaded or edited by FTP. The language scan is refreshed after 3600 seconds / 60 minutes by default. You can override this with `mdlangcachetime`, for example `// 'mdlangcachetime'=>900, // 15 min MD langs cache time`. If a direct language URL such as `/fr/contact` is requested and `contact.fr.md` exists but is not yet known to the cache, ZP updates the cache before delivering the page. If generated output looks stale or broken, delete the files in `/zpcache`; ZP can rebuild them.

`[VAR:...]` tokens are not cached as final output. Variables are evaluated after the cached page HTML was loaded.

## 13. MD editor and media gallery

The MD editor is available through `/admin/e.php` when editor access is enabled and the website is in MD mode. The editor can edit existing Markdown pages, create missing language versions, upload images into `/img`, insert compact image syntax such as `![](img/file.jpg)`, `![|Alt text](img/file.jpg)` or `![alignright|Alt text](img/file.jpg)`, insert page links as `[Text](@page)` or `[Text](@page?x=1)`, insert `[VAR:...]` tokens from `/pages/zvars.php`, and wrap selected text in simple Markdown or allowed HTML. The toolbar shares its top media controls and button order with the Rich editor, but its actions write Markdown/text into the textarea. If the editor and media tools are not used, `/admin/` can be removed and the Markdown files can be maintained directly.

`pageimg.src` stores the optional page image. See `guide/rich-mode.md` for the shared page image output rule.

Editor access is described in [`guide/index.md`]({{ '/guide/' | relative_url }}#optional-editor-media-gallery-and-device-manager).

The media gallery is available at:

```text
/admin/m.php
```

## 14. Mistakes in MD mode

- Copy the contents of `/pages/example/md/` into `/pages/`, not the example directory itself.
- Do not edit files in `/zpcache/` manually; ZANACMS rebuilds them from the Markdown sources.
- For relative images from `/img`, use the ZANACMS `class|alt` form, for example `![alignright|Alt text](img/file.jpg)`.
- `/admin/` is optional in MD mode when Markdown files are edited directly, for example by FTP.

## 15. See also

- [`guide/index.md`]({{ '/guide/' | relative_url }}) for shared setup and all-modes topics
- [`guide/interfaces.md`]({{ '/guide/interfaces/' | relative_url }}) for ZTOKENS, footer tokens, page variables and public interfaces
- [`guide/rich-mode.md`]({{ '/guide/rich-mode/' | relative_url }}) for the visual Rich editor workflow
