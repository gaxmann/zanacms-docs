---
title: "ZANACMS Guide for Rich Mode"
permalink: /guide/rich-mode/
---
# ZANACMS Guide for Rich Mode

Rich mode is the main editor-oriented content mode of ZANACMS. It stores ready-to-use page data as PHP arrays and uses the built-in rich text editor for page editing. The public page runtime stays small: it loads the page data file and lets the selected layout render the page.

Shared setup topics such as layouts, languages, footer syntax, CSS and media access are in [`guide/index.md`]({{ '/guide/' | relative_url }}). Public interface details, including ZTOKENS, page variables and footer tokens, are in [`guide/interfaces.md`]({{ '/guide/interfaces/' | relative_url }}).

## 1. File structure for Rich mode

A minimal Rich mode website has this structure:

```text
/
├── __config/
│   ├── conf.php
│   └── _admconf.php          optional admin-written navigation/sidebar data
├── index.php
├── pages/
│   ├── index.data.php
│   ├── contact.data.php
│   └── zvars.php              optional
├── img/
├── layout/
├── zp/
└── admin/
```

Important files:

```text
/__config/conf.php                website configuration
index.php                 entry point, calls out_page()
pages/<page>.data.php     page data, grouped by language
__config/_admconf.php     optional admin-written navigation/sidebar data
pages/zvars.php           optional PHP variables for [VAR:...]
img/                      images used by the editor and layouts
layout/                   layouts and shared ZP CSS
zp/                       ZANACMS runtime files
admin/                    Rich editor and admin tools
```

Rich mode does not need `/zpcache/`. The bundled starter files are in `/pages/example/rich/`; copy the contents of that directory into `/pages/`, not the directory itself. After copying, the page files should be placed directly as `/pages/index.data.php` and `/pages/contact.data.php`. Navigation and global sidebar data come from `/__config/conf.php` or, if the matching key is not set there, from `/__config/_admconf.php`. The files under `/pages` are the editable content files. Rich mode needs `/admin/`; without the Rich editor, the Rich mode concept is not useful.

## 2. Minimal index.php

For Rich mode, `index.php` is only the entry point:

```php
<?php

include_once __DIR__.'/zp/zana.php';
out_page();

```

The visible page content comes from `/pages/<page>.data.php`.

## 3. Minimal `/__config/conf.php`

A minimal Rich mode configuration looks like this:

```php
<?php

$GLOBALS['zconf']=[
	// 'adminexposure'=>1, // 1..3
	'mode'=>'rich',
	'layout'=>'html/water',
	// 'urlrewrite'=>['/', true],
	'stdlang'=>'en',
	// 'col2'=>'<p>[col2]</p>',
	'foot'=>[
		"<span style='font-size:0.65em'>[footertext]</span>",
	],
];

$GLOBALS['zlangs']=[
	'en'=>[
		'sitetitle'=>'ZANACMS Example',
		'sitesub'=>'Minimal Rich website',
		'vars'=>[
			'footertext'=>'Powered by ZANACMS',
			'col2'=>'Optional sidebar.',
		],
	],
	'de'=>[
		'sitetitle'=>'ZANACMS Beispiel',
		'sitesub'=>'Minimale Rich-Webpräsenz',
		'vars'=>[
			'footertext'=>'Erzeugt mit ZANACMS',
			'col2'=>'Optionale Seitenleiste.',
		],
	],
];

```

`mode` selects the content mode:

```php
'mode'=>'rich',
```

`stdlang` is the standard language. The language selector normally uses the languages defined in `$GLOBALS['zlangs']`. A Rich page may also contain local extra languages in its `.data.php` file.

`foot` is configured in `/__config/conf.php`. Footer links can use the same internal link syntax as Rich page content.

## 4. Page data files

Rich pages are stored as PHP array files:

```text
/pages/index.data.php
/pages/contact.data.php
```

A simple page file:

```php
<?php
return array(
	'mode'=>'rich',
	'langs'=>array(
		'en'=>array(
			'title'=>'',
			'h1'=>'Home',
			'body'=>'<p>This is a Rich page. Open the <a href="'.zlink('contact').'">contact page</a>.</p>',
		),
		'de'=>array(
			'title'=>'',
			'h1'=>'Startseite',
			'body'=>'<p>Dies ist eine Rich-Seite. Öffne die <a href="'.zlink('contact').'">Kontaktseite</a>.</p>',
		),
	),
);
```

`mode` and `noindex` are page-wide values. They belong to the page ID, not to a single language. The language entries are stored below `langs`; these entries also define the local languages of that page. Common page-wide fields are:

```text
mode
noindex
```

Common language fields below `langs[language]` are:

```text
body
title
h1
layoutimg.src
layoutimg.pos
pageimg.src
```

`mode` is usually `rich`. `noindex` is only stored when it is active, as `true`. `body` contains ready-to-output HTML. `title` may stay empty; if the layout/page title is not explicitly set, the website or layout can use other configured title data. `h1` is the page headline and stays stored separately from `body`.

The Rich editor shows `h1` as a visible heading while editing. On save, the heading is stored back into the `h1` field and is not stored as part of `body`.

`layoutimg.*` can override page-specific layout image values. `pageimg.src` stores the page image selected in the Rich editor. Example:

```php
'layoutimg'=>array(
	'src'=>'img/header.jpg',
	'pos'=>'center',
),
```

## Page image

The Rich editor can store one page image in `pageimg.src`. If the active layout contains `~~ZPAGEIMG~~`, the image is inserted there. Otherwise ZP inserts it inside the page body directly after the first opening `<p>` tag. The generated image uses the standard class `alignpageimg`.


## Sidebar in Rich mode

`~~ZCOL2~~` first reads `col2` through `zgetconf('col2')`, so allowed page data can override the global sidebar.

If the active HTML layout contains `~~ZCOL2~~`, the active design must define `columns=2` in `design.ini`, and `$GLOBALS['zconf']['col2']` is not set, the sidebar tool stores its HTML content in:

```text
/__config/_admconf.php
```

The Rich sidebar data lives below `_admconf.php['mode']['rich']['col2']`. PHP mode uses `_admconf.php['mode']['php']['col2']`. The optional `col2img.src` image is stored once for all languages and rendered before the sidebar text with class `col2img`.

## 5. Rich page with PHP fragment

A Rich page can delegate its page data to a PHP fragment by setting the page-wide mode:

```php
'mode'=>'php',
```

Example:

```php
<?php
return array(
	'mode'=>'php',
	'langs'=>array(
		'en'=>array(
			'title'=>'',
			'h1'=>'Contact',
		),
	),
);
```

For page `contact`, ZP then loads:

```text
/pages/contact.php
```

That file is a data fragment. It must not include `zp/zana.php` and it must not call `out_page()`. Example:

```php
<?php
$GLOBALS['zdata']=[
	'title'=>'',
	'h1'=>'Contact',
	'body'=>'<p>Generated contact form output.</p>',
];
```

> This is useful if a PHP programmer wants to set up a page quickly with a contact form, a database table or other complex output. Use a PHP fragment when the whole page content should be generated by PHP. For smaller reusable dynamic parts inside normal Rich pages, use variables from `/pages/zvars.php`. These variables can provide dynamic content, such as database tables or other generated HTML, and can be inserted by the website owner through the Rich editor.

The Rich editor shows a notice when the page is in `mode: php`. The body cannot be edited visually until the mode is changed back to `rich` in the settings.

## 6. Navigation data

Rich mode can set navigation manually in `/__config/conf.php` with `$GLOBALS['zconf']['navi']`. If `navi` is not set there, the admin navigation editor stores the parsed navigation for Rich mode in:

```text
/__config/_admconf.php['mode']['rich']['navi']
```

The matching labels are stored below `_admconf.php['mode']['rich']['langs'][lang]['navi']`.

The stored navigation uses the existing `znavi()` format. Internal navigation targets should use page IDs from the active mode:

```php
<?php
return [
	'mode'=>[
		'rich'=>[
			'navi'=>[
				'index'=>['index', [
				]],
				'contact'=>['contact'],
			],
			'langs'=>[
				'en'=>[
					'navi'=>[
						'index'=>'Home',
						'contact'=>'Contact',
					],
				],
			],
		],
	],
];
```

Navigation labels are stored below `_admconf.php['mode'][mode]['langs'][lang]['navi']`, unless they are configured manually in `$GLOBALS['zlangs']`. Other site-wide language data such as `sitetitle`, `sitesub` and `foot` normally belongs in `/__config/conf.php`.

Use the page ID for internal navigation targets, for example `contact`. `contact.php` and `[@contact]` are tolerated, but the recommended stored form is the plain page ID. The output is resolved through `zlink()`, so the same navigation data works with query URLs and with optional URL rewrite.

## 7. Navigation editor

The navigation editor for Rich mode is available at:

```text
/admin/n.php
```

It is active when `navi` is not set in `/__config/conf.php`.

The editor does not show the PHP array directly. It shows a compact text format:

```text
# ZANACMS navigation
# @pagename | langcode:text (*:Text --> Text is valid for all languages)

@index | de:Start | en:Home

@contact | de:Kontakt | en:Contact
```

On load, the current mode's navigation from `/__config/_admconf.php` is converted to this text form. On save, the text form is parsed and the current mode branch in `/__config/_admconf.php` is written.

If a line cannot be parsed, the file is not saved. The editor shows the text again with an error message. The editor does not reject duplicate page IDs; the historic navigation array format decides what happens on the same level.

`*:` can be used as a short form. If it is the only label on a line, it sets only the standard language. If a line already contains the standard language and `*:` is also present, remaining website languages from `$GLOBALS['zlangs']` can receive that text.

## 8. Internal links

The Rich editor uses `[@page]` targets while editing and converts them to dynamic `zlink()` calls when saving the `.data.php` file:

```html
<a href="[@contact]">Contact</a>
```

```php
'<a href="'.zlink('contact').'">Contact</a>'
```

Footer text uses the common runtime token resolver because `/__config/conf.php` footer lines should not contain function calls. Use `[[@contact]]` for a complete footer link, or `<a href="[@contact]">Contact</a>` when the footer text should provide its own link label. MD footer text uses the same footer notation; MD body text should use normal Markdown links such as `[Contact](@contact)`.

At runtime ZP resolves the target through `zlink()`. Depending on configuration it may become a query URL such as `./?p=contact` or a rewrite URL such as `/en/contact`. Official website languages come from `$GLOBALS['zlangs']`. Additional Rich `langs` entries such as `ru` add that language only on the current page. If a non-official language is requested and the page does not have that language, Rich delivers `fallbacklg` or `stdlang`; canonical and further internal links use that delivered language. If an official language is requested but the current page is missing in that language, Rich keeps further internal links in the requested official language, while canonical points to the delivered fallback language.

## 9. Variables

Rich mode can use page variables from:

```text
/pages/zvars.php
```

The included example file is:

```text
/pages/zvars.example.php
```

Copy it to `/pages/zvars.php` and adjust it. The variable list is stored in `$ZVARS_PAGES`:

```php
$ZVARS_PAGES=[
	'contact_note'=>['en'=>'Contact note','de'=>'Kontakt-Hinweis'],
	'example_box'=>'Example box',
];
```

The value is the label shown in the editor select. The output function is:

```php
function zvar_get($key, $lg) {
	switch ($key) {
		case 'contact_note':
			return '<strong>Contact:</strong> Add your custom PHP output here.';
		case 'example_box':
			return '<div class="box">This text was generated by pages/zvars.php.</div>';
	}
	return '';
}
```

The Rich editor shows the variable select only if `/pages/zvars.php` exists and `$ZVARS_PAGES` contains entries. The editor inserts:

```text
[VAR:contact_note]
```

ZP replaces allowed variable tokens at runtime. Unknown tokens remain visible. Variables are intended for prepared reusable values or generated output: a PHP developer can provide them, and a normal editor user can insert them without writing PHP. For translated helper strings inside `zvar_get()`, `zvarlg($tx)` reads the matching array value without setting the page language. More detail is in [`guide/interfaces.md`]({{ '/guide/interfaces/' | relative_url }}#8-page-variables).

## 10. Rich editor

The page editor is:

```text
/admin/e.php
```

In Rich mode `/admin/e.php` loads the Rich editor. The editor uses local Squire files from:

```text
/admin/squire/
```

The toolbar supports the usual formatting operations: undo, redo, paragraph and headings, bold, italic, underline, code, lists, quote, links, variables, alignment, horizontal rule, line break, HTML source view and the ZP classes `red`, `green`, `grey`, `lgrey` and `fade`.

The `<>` button switches between the visual Rich editor and the HTML source view. The source view is editable, but it is not a free HTML mode. It is intended for checking and correcting the supported ZP Rich HTML produced by the editor.

When switching back to the visual editor, and again when saving, the content is normalised and sanitised. Unsupported tags, unsupported attributes, external formatting, inline styles and Office/LibreOffice formatting are removed.

The Rich editor supports simple page content HTML, mainly:

`h1`, `h2`, `h3`, `h4`, `p`, `br`, `hr`, `ul`, `ol`, `li`, `blockquote`, `b`, `strong`, `i`, `em`, `u`, `code`, `a`, `span`, `img`

Supported classes are:

`red`, `green`, `grey`, `lgrey`, `fade`, `alignleft`, `alignright`, `aligncenter`

Images in Rich content are intended to be local ZP images from `/img` or `/img/big`. Arbitrary external images and pasted Office image data are not part of the Rich editor format.

The editor may rewrite technically valid HTML into simpler ZP Rich HTML. For example, a line break is intended inside a text block:

```html
<p>Line 1<br>Line 2</p>
```

A `<br>` directly between block elements is not a stable Rich editor structure and may be removed or normalised.

The Rich editor is deliberately limited to maintain clean page content. For free HTML, PHP-generated output or more complex dynamic content, use PHP mode, PHP fragments or `[VAR:...]` variables instead.

The image selector uses images from `/img`. Selecting an image inserts it directly into the editor. Image upload uses the shared editor upload code and stores images in `/img`.

The Rich editor is a page-content editor. It is not a full site configuration editor. Global footer texts and `sitetitle`/`sitesub` are still configured in `/__config/conf.php`.


## 11. Shared ZP CSS classes

Rich content can use the shared ZP content classes from:

```text
/layout/zpsuper.css
```

Common classes:

`red`, `green`, `grey`, `lgrey`, `fade`, `alignleft`, `alignright`, `aligncenter`

These classes are available in the editor preview and in the rendered page.

## 12. Adding a new page

To add a new page `about`, create or edit it through the Rich editor, or create:

```text
/pages/about.data.php
```

A minimal file:

```php
<?php
return array(
	'mode'=>'rich',
	'langs'=>array(
		'en'=>array(
			'title'=>'',
			'h1'=>'About',
			'body'=>'<p>About text.</p>',
		),
	),
);
```

Then add it to the navigation through `/admin/n.php`, or manually in `/__config/conf.php` / `/__config/_admconf.php`:

```php
'about'=>array('about'),
```

and add its label under `langs[language]['navi']`.

## 13. Relation to MD mode

Rich mode is the editor-oriented mode. MD mode is a separate regular mode. MD pages use Markdown files and `/zpcache/`; Rich pages use `.data.php` files and no Markdown cache. Shared concepts such as internal links, footer links and `[VAR:...]` variables are documented here because Rich mode is the main reference for those mechanisms.

For Markdown sites see [`guide/markdown-mode.md`]({{ '/guide/markdown-mode/' | relative_url }}). For PHP page fragments and full PHP mode see [`guide/php-mode.md`]({{ '/guide/php-mode/' | relative_url }}).

## 14. Mistakes in Rich mode

- Copy the contents of `/pages/example/rich/` into `/pages/`, not the example directory itself.
- Keep `/admin/` when using Rich mode; without the Rich editor, the Rich mode concept is not useful.
- Use `[@page]` links in Rich editor content and footer tokens in `/__config/conf.php`.

## 15. See also

- [`guide/index.md`]({{ '/guide/' | relative_url }}) for shared setup and all-modes topics
- [`guide/interfaces.md`]({{ '/guide/interfaces/' | relative_url }}) for ZTOKENS, footer tokens, page variables and public interfaces
- [`guide/markdown-mode.md`]({{ '/guide/markdown-mode/' | relative_url }}) for Markdown-based sites
