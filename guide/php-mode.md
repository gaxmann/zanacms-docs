---
title: "PHP Mode"
permalink: /guide/php-mode/
parent: Guide
nav_order: 2
---
# ZANACMS Guide for PHP Mode

PHP mode is the mode for advanced users that know PHP. In PHP mode, pages are PHP files. Each page includes ZP, prepares its page data, and calls `out_page()`.

Shared setup topics such as layouts, languages, footer syntax, CSS and editor/media access are in [`guide/intro.md`]({{ '/guide/intro/' | relative_url }}). Full details for public helper functions such as `zlink()` and `zhref()` are in [`guide/interfaces.md`]({{ '/guide/interfaces/' | relative_url }}).

## 1. File structure for PHP mode

A minimal PHP mode website has this structure:

```text
/
├── __config/
│   └── conf.php
├── index.php
├── contact.php
├── img/
├── layout/
└── zp/
```

`/__config/conf.php` is the website configuration. The PHP page files are in the website root. `layout` contains the visual layouts. `img` contains your images. `zp` contains the ZP runtime files. PHP mode does not need a `pages` directory and does not need `zpcache` for normal page output. If the optional admin navigation or sidebar tools are used, they store fallback data in `/__config/_admconf.php`. PHP mode does not need `/admin/` for runtime; it needs `/admin/` only if admin tools such as media upload, navigation or sidebar editing are used. Otherwise `/admin/` can be removed.

## 2. Minimal index.php

A PHP mode page can look like this:

```php
<?php
// ZANACMS php mode
// config in ./__config/conf.php

include_once __DIR__.'/zp/zana.php';

$h1=[
	'en'=>'Home',
	'de'=>'Start',
];
$lg=zautolg($h1);

switch($lg){
	case 'de':
		$body="Dies ist eine kleine PHP-Seite.

".zhref('contact', 'Kontakt >');
		break;

	case 'en':
	default:
		$body="This is a small PHP page.

".zhref('contact', 'Contact >');
}

$GLOBALS['zdata']=[
	'title'=>'',
	'h1'=>$h1[$lg],
	'body'=>zautop($body),
	// 'noindex'=>true,
];

out_page();
```

The page includes:

```php
include_once __DIR__.'/zp/zana.php';
```

Then it prepares `$GLOBALS['zdata']`. Finally it calls:

```php
out_page();
```

A normal PHP-mode page is a complete public page. It includes `zp/zana.php`, can call `zautolg()` or other ZP helpers, sets `$GLOBALS['zdata']`, and calls `out_page()`.

This is different from a PHP fragment used by an MD or Rich page with `mode: php`. Such a fragment lives in `/pages/PAGEID.php`, must not include `zp/zana.php`, and must not call `out_page()`. It may call `zautolg()` when it needs language selection, and it sets `$GLOBALS['zdata']`; ZANACMS has already loaded the runtime before the fragment is included.

## 3. Minimal `/__config/conf.php`

A minimal PHP mode configuration looks like this:

```php
<?php

$GLOBALS['zconf']=[
	// 'adminexposure'=>3, // 1..5
	'mode'=>'php',
	'layout'=>'html/water',
	// 'urlrewrite'=>['/', true],
	// 'layoutimg'=>['src'=>'img/example.jpg', 'pos'=>'center'],
	'stdlang'=>'en',
	// 'col2'=>'<p>[col2]</p>',
	'foot'=>[
		"<span style='font-size:0.65em'>[footertext]</span>",
	],
	'navi'=>[
		'index'=>['index'],
		'contact'=>['contact'],
	],
];

$GLOBALS['zlangs']=[
	'en'=>[
		'sitetitle'=>'ZANACMS Example',
		'sitesub'=>'Minimal PHP website',
		'navi'=>[
			'index'=>'Home',
			'contact'=>'Contact',
		],
		'vars'=>[
			'footertext'=>'Powered by ZANACMS',
			'col2'=>'Optional sidebar.',
		],
	],
	'de'=>[
		'sitetitle'=>'ZANACMS Beispiel',
		'sitesub'=>'Minimale PHP-Webpräsenz',
		'navi'=>[
			'index'=>'Start',
			'contact'=>'Kontakt',
		],
		'vars'=>[
			'footertext'=>'Erzeugt mit ZANACMS',
			'col2'=>'Optionale Seitenleiste.',
		],
	],
];

```

Important settings:

```php
'mode'=>'php',
```

selects PHP mode.

```php
'layout'=>'html/water',
```

selects the layout. The format is `<family>/<design>`. A layout name without slash, such as `test`, is resolved as `test/test`.

Some layouts can use optional design values. For example, `paradigm-shift/paradigm-shift` uses `layoutimg` for its intro image:

```php
'layoutimg'=>['src'=>'img/example.jpg', 'pos'=>'center'],
```

```php
'stdlang'=>'en',
```

selects the standard language.

```php
'php_regexpages'=>'^(?!_)(?!.*__)[a-z0-9_-]+$',
```

is optional. It controls which PHP files in the website root are shown in the PHP page overview in `/admin/p.php`. The value is a regular expression body without delimiters. By default, page files must use lowercase letters, numbers, `_` and `-`, must not start with `_`, and must not contain `__`.



## Sidebar in PHP mode

PHP mode can define a global sidebar directly in `/__config/conf.php` with `$GLOBALS['zconf']['col2']`, override it per page with allowed `$GLOBALS['zdata']['col2']`, or use the admin sidebar tool. The admin tool stores its fallback value in `/__config/_admconf.php`, below `['mode']['php']['col2']`.

For a page-specific sidebar in a PHP page, set:

```php
$GLOBALS['zdata']['col2']='<p>Sidebar for this page.</p>';
```

`$GLOBALS['zdata']['col2']` is read through `zgetconf('col2')` and overrides the global `$GLOBALS['zconf']['col2']` for that page. If `$GLOBALS['zconf']['col2']` is set, the editor is locked. If neither page data nor config provides `col2`, the admin tool **Sidebar** uses the Rich editor and stores its fallback content in:

```text
/__config/_admconf.php
```

That value is only used for `~~ZCOL2~~`. Normal PHP pages still live in the website root.

## 4. Page content

In PHP mode, the page content is set in `$GLOBALS['zdata']`. Example:

```php
$GLOBALS['zdata']=[
	'title'=>'',
	'h1'=>'Home',
	'layoutimg'=>['src'=>'img/page.jpg'],
	'body'=>'<p>This is a small PHP page.</p>',
];
```

`layoutimg` is optional and only used by layouts that support it. It can override single values from `$GLOBALS['zconf']['layoutimg']` for the current page. For example, `paradigm-shift/paradigm-shift` uses `layoutimg['src']` for its intro image and keeps the global `pos` value if the page does not set it.

`title` is used for the HTML page title. `h1` is used as the visible main heading. `body` contains the page content as HTML. If `title` is empty, ZP can use `h1` as a fallback.

## 5. Language handling in PHP pages

A PHP page should first register its available languages and then build language-dependent HTML. This keeps `zlink()` and `zhref()` in the selected content language.

Fast method with `zhref()` after `zautolg()`:

```php
$h1=[
	'en'=>'Home',
	'de'=>'Start',
];
$lg=zautolg($h1);

switch($lg){
	case 'de':
		$body="Dies ist eine kleine PHP-Seite.

".zhref('contact', 'Kontakt >');
		break;

	case 'en':
	default:
		$body="This is a small PHP page.

".zhref('contact', 'Contact >');
}

$GLOBALS['zdata']=[
	'title'=>'',
	'h1'=>$h1[$lg],
	'body'=>zautop($body),
];
```

Alternative method with ZTOKENS:

```php
$tx=[
	'en'=>[
		'h1'=>'Home',
		'body'=>zautop('This is a small PHP page.

'.ztokenhref('contact', 'Contact >')),
	],
	'de'=>[
		'h1'=>'Start',
		'body'=>zautop('Dies ist eine kleine PHP-Seite.

'.ztokenhref('contact', 'Kontakt >')),
	],
];

$lg=zautolg($tx);
$GLOBALS['zdata']=$tx[$lg];
```

The first method is direct and faster. The second method keeps ZTOKENS in the body and lets ZANACMS replace them during final output. Do not call `zlink()` or `zhref()` while building all translated body strings before `zautolg()` has selected the delivered language.

`zautolg($tx)` registers the languages of this PHP page from `array_keys($tx)` and stores the delivered content language internally. Therefore the example does not need to set `$GLOBALS['zdata']['lg']` manually. Set `$GLOBALS['zdata']['lg']` only in old/manual PHP pages that do not use `zautolg()` and have already decided which content language is actually delivered.

## 6. Internal links

Use `zlink()` when creating internal links in PHP pages. Example:

```php
"<a href='".zlink('contact')."'>Contact</a>"
```

For a plain text link in PHP code or templates, `zhref()` returns the complete anchor tag and uses `zlink()` for the URL:

```php
zhref('contact', 'Contact >')
```

For a CSS class, pass `class` in the option array:

```php
zhref('contact', 'Contact >', ['class'=>'button'])
```

`zhref()` can also build external HTTP links. External `http://` and `https://` links open in a new tab by default:

```php
zhref('https://example.com', 'External website')
zhref('https://example.com', 'External website', ['newtab'=>false])
```

Internal links stay in the same tab by default. Use `['newtab'=>true]` only when an internal link should open a new tab.

If you want a ZTOKEN link instead of an immediate URL, use `ztokenhref()`:

```php
ztokenhref('contact', 'Contact >')
```

This returns an anchor with `[@contact]` as the URL token. The token is resolved during final output.

If additional query parameters follow, pass `qsep` as `?` in the option array. `zlink()` then returns the correct separator for the current URL style:

```php
"<a href='".zlink('contact', ['qsep'=>'?'])."somevalue=12'>Contact</a>"
```

In PHP page code, layout PHP and generator PHP, use `zlink('page')` for internal link URLs or `zhref('page', 'Text')` for a complete anchor tag. Optional link behaviour is passed as an array, for example `zlink('page', ['qsep'=>'?'])`, `zlink('page', ['lg'=>'en'])`. In the structured navigation config, use page IDs such as `'contact'`; ZP normalises them centrally through `zlink()` when the navigation is prepared. `contact.php` is also tolerated, but `contact` is the recommended form. In footer and text fields, use `[[@page]]`, for example `[[@contact]]`, for a complete internal link, or `[@contact]` when only the URL is needed inside a custom `<a href="[@contact]">Text</a>` tag. For the complete parameter reference of the public PHP functions, see [`guide/interfaces.md`]({{ '/guide/interfaces/' | relative_url }}#6-public-php-functions-for-page-authors).

## 7. URL rewrite

URL rewrite is optional and off by default. To prepare root-based URLs without rewrite, set only the base path:

```php
'urlrewrite'=>['/'],
```

To enable rewrite URLs, use the same base path with `true` and copy the matching `.htaccess` example to `.htaccess`:

```php
'urlrewrite'=>['/', true],
```

For a site installed in `/cms/`, use:

```php
'urlrewrite'=>['/cms/', true],
```

Internal PHP links should still be written as `zlink('page')` or `zhref('page', 'Text')`. With rewrite enabled, `zlink('contact')` becomes a URL such as `/en/contact`; without rewrite it stays a normal PHP URL in PHP mode. The `lay` design selector remains a query parameter.

## 8. Media gallery

PHP mode does not use a page editor and does not need `/admin/` for runtime. It needs `/admin/` only if image upload through the optional media gallery or device access tools are used. Rich and Markdown page editing are disabled in PHP mode. Activation of the optional media gallery and device manager is handled centrally; see [`guide/intro.md`]({{ '/guide/intro/' | relative_url }}#optional-editor-media-gallery-and-device-manager).

When gallery access is active, in PHP mode ZANACMS adds a small `[i]` link to the footer. The link opens the media gallery:

```text
/admin/m.php
```

The media gallery uses the ZP admin layout and lists all supported images from `/img`. It can upload images, create the resized files used by ZANACMS, and copy the matching PHP image snippet to the clipboard when an image is clicked. This is useful in PHP mode because images can be uploaded and selected without a page editor. Example snippet copied in PHP mode:

```php
".zoutimg('image.jpg',['align'=>'right','alt'=>'','big'=>true,'ratio'=>0.75])."
```

The generated snippet can be pasted into a PHP page body string.


## 9. Navigation

PHP mode can define navigation manually in `$GLOBALS['zconf']['navi']`. PHP mode is the primary developer mode, and this compact form is the normal manual configuration:

```php
'navi'=>[
	'suhr'=>['sunclock', [
		'setup'=>['suhr3'],
		'manual'=>['suhr2'],
		'overview'=>['suhr'],
		'support'=>['che7'],
	]],
	'lsea'=>['lichtz'],
	'kontakt'=>['kontakt'],
],
```

The array key is the navigation key. The first value is the target page ID or URL. The optional second value is subnavigation using the same structure.

Visible navigation labels are stored in `$GLOBALS['zlangs'][language]['navi']`:

```php
$GLOBALS['zlangs']=[
	'en'=>[
		'navi'=>[
			'suhr'=>['Sun Clock'],
			'setup'=>'Setup',
			'manual'=>'Manual',
			'overview'=>'Overview',
			'support'=>'Support',
			'lsea'=>['Light season','Season'],
			'kontakt'=>'Contact',
		],
	],
	'de'=>[
		'navi'=>[
			'suhr'=>['Sonnenuhr'],
			'setup'=>'Einrichtung',
			'manual'=>'Anleitung',
			'overview'=>'Übersicht',
			'support'=>'Unterstützen',
			'lsea'=>'Lichtzeit',
			'kontakt'=>'Kontakt',
		],
	],
];
```

A label may be a string or an array. When it is an array, ZANACMS chooses the text through the existing navigation-label logic for the current navigation level.

If `navi` is not set in `conf.php`, the admin navigation editor stores fallback navigation in `_admconf.php['mode']['php']['navi']` and matching labels below `_admconf.php['mode']['php']['langs'][language]['navi']`.

## 10. Footer

Footer lines are configured in `$GLOBALS['zconf']['foot']`. Example:

```php
'foot'=>[
	"<span style='font-size:0.65em'>[footertext]</span>",
],
```

`[[@page]]` creates a complete internal footer link. `[@page]` creates only the URL and can be used inside a custom `<a href="[@page]">Text</a>` tag. `[@page?]extra=3` keeps extra query parameters. `[footertext]` is replaced with text from `$GLOBALS['zlangs'][language]['vars']`. Example:

```php
'vars'=>[
	'footertext'=>'Powered by ZANACMS',
],
```

## 11. Adding a new PHP page

To add a new PHP page called `about.php`. Create the page file:

```text
/about.php
```

Add page content:

```php
<?php

include_once __DIR__.'/zp/zana.php';

$GLOBALS['zdata']=[
	'title'=>'',
	'h1'=>'About',
	'body'=>'<p>About this website.</p>',
];

out_page();

```

Add it to the navigation in `/__config/conf.php` (if wanted):

```php
'navi'=>[
	'index'=>['index'],
	'about'=>['about'],
],
```

Add the navigation text:

```php
'navi'=>[
	'index'=>'Home',
	'about'=>'About',
],
```

## 12. Optional page flags

PHP pages may set optional page-level flags in `$GLOBALS['zdata']`.

```php
$GLOBALS['zdata']['noindex']=true;
```

Set this before `out_page()` if the current PHP page should not be indexed by search engines. When enabled, ZANACMS outputs a noindex,nofollow,noodp robots meta tag for that page. This is useful for legal notice, privacy policy and contact pages or internal pages that should remain publicly accessible but should not appear in search results. The old `$noindex=true;` form is still supported as a legacy exception, but new PHP pages should use `$GLOBALS['zdata']['noindex']`.

## 13. Mistakes in PHP mode

- Do not put normal PHP mode page files below `/pages/`; PHP mode page files are in the website root.
- Use `zlink()` or `zhref()` in PHP page code, but use footer tokens such as `[[@contact]]` in `/__config/conf.php` footer configuration.
- PHP mode does not need `/admin/` for runtime. Keep `/admin/` only when the media gallery is used.

## 14. See also

- [`guide/intro.md`]({{ '/guide/intro/' | relative_url }}) for shared setup and all-modes topics
- [`guide/interfaces.md`]({{ '/guide/interfaces/' | relative_url }}) for public PHP function parameters and customisation interfaces


## Sitemap

PHP mode sitemap rebuilding is available unless `sitemap` is disabled. If the key is missing, the preset is `true`. To disable it, set:

```php
'sitemap'=>false,
```

Normal PHP-mode pages are root PHP files. For the PHP-mode sitemap scan, the admin maintenance process calls candidate page URLs with an internal `zreadpage` request and reads the page languages and `noindex` state from the real page runtime. Files that do not answer as ZANACMS pages are not added to the sitemap.

The current sitemap maintenance needs `/admin/` and actual admin use. If the admin area is removed or never opened, automatic sitemap rebuilding does not run.
