---
title: "Interfaces"
permalink: /guide/interfaces/
parent: Guide
nav_order: 7
---
# ZANACMS Interfaces

This document describes the public customisation and extension interfaces of ZANACMS. It is intended for advanced users who build their own site structure, layout adjustments, local variables or small integrations. It is not a guide for changing ZANACMS core files.

For the first setup, use the [Introduction]({{ '/guide/intro/' | relative_url }}). For mode-specific page formats, use [PHP mode]({{ '/guide/php-mode/' | relative_url }}), [Markdown mode]({{ '/guide/markdown-mode/' | relative_url }}) and [Rich mode]({{ '/guide/rich-mode/' | relative_url }}).


## Contents

```text
1. What counts as a public interface
2. Modes and responsibility
3. Configuration in /__config/conf.php
4. Footer syntax
5. Internal links by context
6. Public PHP functions for page authors
7. Public data arrays
8. Page variables
9. ZTOKENS and MD additions/extensions for ZANACMS
10. CSS customisation
11. HTML layouts
12. Layout families and generators
13. Admin language files
14. What is not a public interface
```

## 1. What counts as a public interface

ZANACMS should be customised through documented files, arrays and functions. Core files in `/zp/` and admin files in `/admin/` are runtime and administration code and should not be changed for normal site customisation.

Public interfaces are:

```text
/__config/conf.php
/__config/_admconf.php
/__config/EDITACCESS_ENABLE.php
/__config/service.php
$GLOBALS['zconf']
$GLOBALS['zadmconf']
$GLOBALS['zlangs']
$GLOBALS['zdata']
/pages/zvars.php
/layout/supercustom.css
/layout/<family>/<design>/custom.css
/layout/html/<design>/design.html
/layout/html/<design>/zp.css
/layout/<family>/_generator/zpgen.php
/admin/lang/<language>.php
```

### Interface overview

| Area | Interface | Main use |
| --- | --- | --- |
| Site configuration | `/__config/conf.php`, `$GLOBALS['zconf']` | manual mode, layout, footer, language defaults and site options |
| Admin fallback configuration | `/__config/_admconf.php`, `$GLOBALS['zadmconf']` | admin-written layout, navigation and sidebar fallback values when the matching key is not set in `conf.php` |
| Service access | `/__config/service.php` | optional agency/support access that creates a normal editing cookie |
| Site texts | `$GLOBALS['zlangs']` | site title, subtitle, navigation labels and layout text variables |
| Current page | `$GLOBALS['zdata']` | title, heading, body, page language and page image/layout data |
| PHP page helpers | `out_page()`, `zlink()`, `zhref()`, `ztokenhref()`, `zautop()`, `zsetlangs()`, `zautolg()`, `zvarlg()`, `zquote()`, `zoutimg()` | normal PHP page output and internal links |
| MD/Rich variables | `/pages/zvars.php` | prepared values or generated output for normal editors |
| Sidebar admin data | `/__config/_admconf.php` | admin-written sidebar content for `~~ZCOL2~~` when page data and `zconf['col2']` are not set |
| ZTOKENS / text tokens | `[@page]`, `[[@page]]`, `[footertext]`, `[VAR:name]` | replacement tokens for footer, col2 and editor content |
| CSS | `/layout/supercustom.css`, `/layout/<family>/<design>/custom.css` | update-safe visual changes |
| Own HTML design | `/layout/html/<design>/design.html`, `/layout/html/<design>/zp.css` | local HTML design without a PHP generator |
| Layout family generator | `/layout/<family>/_generator/zpgen.php` | project-specific layout family support |
| Admin language | `/admin/lang/<language>.php` | local admin interface translation |


## 2. Modes and responsibility

ZANACMS has three content modes:

- PHP mode: page content is written in PHP files.
- MD mode: page content is written in Markdown files. Files can be edited directly or through the optional Markdown editor.
- Rich mode: page content is stored in `.data.php` files and edited through the Rich editor.

The modes share layout output, footer handling, language handling and internal URL generation, but their page source formats are intentionally different.

A language code is the short language code used by ZANACMS, normally two lowercase letters such as `en` or `de`. It appears in `$GLOBALS['zlangs']`, MD filenames, Rich page language arrays and internal URLs.

## 3. Configuration in `/__config/conf.php`

`/__config/conf.php` is loaded before normal page output. It contains configuration arrays and language texts.

Do not call ZANACMS runtime functions in `/__config/conf.php`. Configuration must stay plain PHP data.

Important configuration areas:

```text
mode            php, md or rich
layout          selected layout as <family>/<design>
urlrewrite      optional URL base and rewrite setting
stdlang         standard language of the website
fallbacklg      optional fallback language
foot            footer lines
col2            optional sidebar HTML for layouts with `~~ZCOL2~~`; page data can override the config value through `zgetconf('col2')`; if set in config, this manual value has runtime priority
headlast        optional HTML directly before </head>
bodylast        optional HTML directly before </body>
adminexposure   optional admin surface level, 1..5, default 3; /__config/isdev.txt acts as 8
autoupdcheck   optional; false disables the cached GitHub status check in /admin/
```

`adminexposure` controls how much of the admin surface is shown. Level 1 is the Editor level and cannot delete pages, languages or images and cannot use Settings. Level 2 is Foolproof and cannot use Settings. Level 3 is the default and shows Settings plus the cached GitHub status. Level 4 is technically proficient and can also use the update page and update actions. Level 5 currently keeps additional room for a fuller admin surface. `/__config/isdev.txt` acts as internal service level 8 for development.

Configuration values must be plain data. They must not depend on functions from `/zp/zana.php`. The device manager temporary access window is controlled by `/__config/EDITACCESS_ENABLE.php`.

### Cached GitHub status check

The optional GitHub status check in the admin status table is enabled by default. It is only a cached information display. To disable it, set:

```php
'autoupdcheck'=>false,
```

The status check reads the latest version from GitHub and stable/warning policy data from `img/updatepolicy.json`. `warnings` can mark installed version ranges for which an update is recommended. `endofline` marks breaking versions; the admin dashboard will not present breaking updates as normal safe updates. Details are documented in [guide/update.md]({{ '/guide/update/' | relative_url }}).

### Service access

Optional agency/support service access is enabled by:

```text
/__config/service.php
```

This file contains only the bcrypt hash of a service key with at least 64 characters:

```php
<?php
return '$2y$14$...';
```

No example file is shipped.

Service keys shorter than 64 characters are rejected before the bcrypt hash is checked.

Generate a service key with at least 64 characters on a Unix-like shell:

```sh
LC_ALL=C tr -dc 'A-Za-z0-9' </dev/urandom | head -c 128; echo
```

Create the bcrypt hash with cost 14:

```sh
php -r 'echo password_hash(trim(fgets(STDIN)), PASSWORD_BCRYPT, ["cost"=>14]), PHP_EOL;'
```

The normal device-manager URL is not changed by `service.php`. Without `/__config/EDITACCESS_ENABLE.php`, `/admin/d.php` stays locked as usual. The service-only mask is shown only through:

```text
/admin/d.php?isservice=1
```

and only while `/__config/service.php` exists. This mask contains only the access select with `--- Please choose ---` and `◇ Service access`, plus a text field labelled `Service:`.

On success, the service key creates a normal `zeditcook` editing cookie. The browser receives no separate service cookie. `/admin/_zdata.php` stores no service key and no service hash. It stores a normal device entry and can mark it with:

```php
'adminexposure'=>8,
```

A valid service-marked cookie gives real admin exposure level 8.

### Agency support level selector

When the real admin exposure level is 8, the admin navigation shows a small selector for levels 1, 2, 3, 4, 5 and 8. This can be used to view the admin surface as a customer with a lower `adminexposure` level would see it.

The selector stores the temporary display level in:

```text
zservicelevel
```

The cookie lives for up to one hour. It is ignored unless the real admin exposure level is 8. It can only lower the displayed admin level; it cannot raise access above the real level. The real level remains available for checks that must not be affected by the simulated display level.

## 4. Footer syntax

Footer lines are configured in `$GLOBALS['zconf']['foot']`.

Footer syntax is mode-independent. It works in PHP mode, MD mode and Rich mode because footer text is resolved by the common runtime text logic. Footer tokens are part of ZTOKENS.

Use:

```text
[[@page]]
```

for a complete internal page link.

Use:

```text
[@page]
```

for only the URL, for example inside a custom `<a href="[@page]">Text</a>` tag.

Use:

```text
[footertext]
```

for translated text from `$GLOBALS['zlangs'][language]['vars']`. These simple variables are replaced before link tokens, so a variable value may itself contain `[@page]` or `[[@page]]`.

Use:

```text
[@page?]value=12
```

when extra query parameters have to be appended.

Do not use `zlink()` or `zhref()` directly inside footer configuration in `/__config/conf.php`.

`~~ZCOL2~~` reads sidebar text through `zgetconf('col2')`. This means allowed `$GLOBALS['zdata']['col2']` can override the global sidebar for the current page. Text variables are read from `$GLOBALS['zlangs'][language]['vars']` first, then link tokens are resolved. If `$GLOBALS['zconf']['col2']` is set, the manual config value has runtime priority and the matching admin value is ignored. If neither page data nor manual config provides `col2`, the settings page stores sidebar content in `/__config/_admconf.php`. The sidebar field in the settings page is shown only when `col2` is not set directly in `/__config/conf.php` and the active design has `columns=2`. MD mode stores Markdown there and caches the rendered sidebar HTML in `/zpcache/_meta.php`; Rich and PHP mode store HTML there, in separate mode branches.

## 5. Internal links by context

PHP page code, layout PHP and generator PHP:

```php
zlink('contact')
zhref('contact', 'Contact')
```

MD body text:

```md
[Contact](@contact)
[Contact with parameter](@contact?value=4)
```

Rich editor body text:

```html
<a href="[@contact]">Contact</a>
```

Footer text in all modes:

```text
[[@contact]]
<a href="[@contact]">Contact</a>
```

The context matters. Do not mix PHP function calls into `/__config/conf.php` footer text.

## 6. Public PHP functions for page authors

These functions are intended for PHP page code after `/zp/zana.php` has been loaded. They can also be used in layout PHP and generator PHP when the same output is needed. This reference was checked against the function signatures and option names in `/zp/zana.php`.


### Function quick reference

| Function | Main purpose | Parameters |
| --- | --- | --- |
| `out_page($force_mode='')` | render the current page | optional mode override |
| `zlink($page, $md=[])` | build an internal page URL | page ID, options array |
| `zhref($page, $text, $md=[])` | build an internal or external anchor tag | page ID or HTTP URL, link text, options array |
| `ztokenhref($page, $text, $md=[])` | build an anchor tag with a ZTOKEN URL | page ID, link text, options array |
| `zautop($tx)` | convert plain text paragraphs to simple HTML | text block |
| `zsetlangs($langs)` | register available page languages | flat language-code array |
| `zautolg($tx)` | select delivered language for a PHP page | language array |
| `zvarlg($tx)` | read the matching language value from an array without setting page language | language array |
| `zquote($tx, $lg=null)` | add language-dependent quotation marks | text, optional language code |
| `zoutimg($file, $md=[])` | output a local image from `/img` | image file, options array |


### `out_page($force_mode='')`

Outputs the current page with the selected mode and layout.

Parameters:

```text
$force_mode     optional mode override, for example 'php', 'md' or 'rich'
```

Normal page entry files call it without parameters:

```php
out_page();
```

`$force_mode` is for special cases. Normal websites should select the mode in `/__config/conf.php`.

### `zlink($page, $md=[])`

Returns the URL for an internal ZANACMS page.

Parameters:

```text
$page           page ID, for example 'contact'
$md['lg']       optional target language
$md['qsep']     set to '?' when extra query parameters will be appended
$md['forcelg']  true to force a language parameter even for the standard language
$md['esc']      false returns & in query strings instead of &amp;
```

The page value is normalised internally. Use the plain page ID, for example `contact`, not a file path. If the current page registered its language set, `zlink()` follows the delivered content language when the requested language is not available for the current page.

Examples:

```php
zlink('contact')
zlink('contact', ['lg'=>'de'])
zlink('contact', ['qsep'=>'?']).'value=12'
zlink('contact', ['esc'=>false])
```


### `zhref($page, $text, $md=[])`

Returns a complete anchor tag. For internal page IDs it uses `zlink()` for the URL. For absolute `http://` and `https://` URLs it uses the URL directly.

Parameters:

```text
$page              page ID, for example 'contact', or an absolute HTTP URL
$text              visible link text
$md['class']       optional CSS class for the anchor tag
$md['newtab']      true or false; internal default false, HTTP default true
```

For internal links, the `$md` array also accepts the `zlink()` options `lg`, `qsep`, `forcelg` and `esc`.

Examples:

```php
zhref('contact', 'Contact')
zhref('contact', 'Contact', ['class'=>'button'])
zhref('contact', 'Kontakt', ['lg'=>'de'])
zhref('https://example.com', 'External website')
zhref('https://example.com', 'External website', ['newtab'=>false])
```


### `ztokenhref($page, $text, $md=[])`

Returns a complete anchor tag with a ZTOKEN URL instead of an immediate URL.

Parameters:

```text
$page           page ID, for example 'contact'
$text           visible link text
$md['class']    optional CSS class for the anchor tag
$md['qsep']     set to '?' when extra query parameters will be appended later
```

Examples:

```php
ztokenhref('contact', 'Contact')
ztokenhref('contact', 'Contact', ['class'=>'button'])
ztokenhref('contact', 'Contact', ['qsep'=>'?'])
```

`ztokenhref('contact', 'Contact')` returns an anchor with `[@contact]` as the URL token. ZANACMS resolves the token during final output. This is useful when a PHP page builds translated body strings before final output.


### `zautop($tx)`

Converts a plain text block into simple paragraph HTML.

Parameters:

```text
$tx             plain text block
```

Single line breaks become `<br>`. Empty lines start a new paragraph.

Example:

```php
zautop("First line
Second line

Next paragraph")
```

### `zsetlangs($langs)`

Registers the available languages of the current page and selects the delivered content language.

Parameters:

```text
$langs          flat array of language codes, for example ['en','de']
```

Example:

```php
$lg=zsetlangs(['en','de']);
```

`zsetlangs()` is mainly used by ZANACMS internals and generated Rich/MD data when the page language list is already known. PHP page authors normally use `zautolg()`.

### `zautolg($tx)`

Selects the best available language for a PHP page and registers the page languages.

Parameters:

```text
$tx             array with language codes as keys
```

Typical PHP page use:

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

out_page();
```

The function returns the delivered language key. It also sets the internal page language list and delivered content language. In translated PHP pages, call `zautolg()` before building HTML that uses `zlink()` or `zhref()`.

Alternative PHP body strings can use ZTOKENS through `ztokenhref()` and let ZANACMS resolve the link during final output.

### `zvarlg($tx)`

Returns the value from a language array that best matches the current language candidate order. It does not register page languages and does not set the delivered content language.

Parameters:

```text
$tx             array with language codes as keys
```

Example for `/pages/zvars.php`:

```php
function zvar_get($key, $lg) {
	switch ($key) {
		case 'example_box':
			$tx=[
				'en'=>'Current server time: %s.',
				'de'=>'Aktuelle Serverzeit: %s.',
			];
			return '<div class="greenbox">'.sprintf(zvarlg($tx), date('Y-m-d H:i')).'</div>';
	}
	return '';
}
```

Use `zvarlg()` for translated helper arrays inside page variables or similar runtime output where only the matching value is needed. Use `zautolg()` for PHP pages that also need to register their page languages and set the delivered content language.

### `zquote($tx, $lg=null)`

Wraps text in language-dependent quotation marks.

Parameters:

```text
$tx             text to quote
$lg             optional language code; default is the current page language
```

Example:

```php
zquote('Example', 'de')
```


### `zoutimg($file, $md=[])`

Returns image HTML for an image from `/img` and optionally links to a larger image.

Parameters:

```text
$file           image file name below /img
$md['align']    right, left or pageimg; default right
$md['alt']      image alt text
$md['big']      true links to /img/big/<file>; 'self' links to the image itself
$md['ratio']    optional height ratio used for the generated height attribute
```

The image width comes from `imgwidth` in `/__config/conf.php`; the default is 200.

Examples:

```php
zoutimg('image.jpg')
zoutimg('image.jpg', ['align'=>'left','alt'=>'Example image'])
zoutimg('image.jpg', ['big'=>true,'ratio'=>0.75])
zoutimg('image.jpg', ['big'=>'self'])
```

The file name is reduced to its basename. Directory paths are not part of this interface.

## 7. Public data arrays

`$GLOBALS['zconf']` contains site configuration.

`$GLOBALS['zlangs']` contains translated site texts. Simple layout text variables for footer and `col2` are stored under `$GLOBALS['zlangs'][language]['vars']`.

`$GLOBALS['zdata']` contains the currently rendered page data:

```text
title           document title of the current page
h1              visible main heading
body            main HTML body
head            optional page-specific HTML for <head>
lg              delivered content language
layoutimg       optional page-specific layout image/design values
pageimg         optional page image value used by supported layouts
```

Generators and layouts read these values when building the final HTML output.

Admin layout and sidebar data are stored in `/__config/_admconf.php`, not in normal page files. The layout value is stored as top-level `'layout'`. Sidebar admin data is mode-specific. MD mode uses `_admconf.php['mode']['md']['col2']`; Rich mode uses `_admconf.php['mode']['rich']['col2']`; PHP mode uses `_admconf.php['mode']['php']['col2']`. The optional sidebar image is stored once as `col2img.src` and rendered with class `col2img` before the sidebar text in all languages.

Admin navigation data is also mode-specific in `/__config/_admconf.php`. PHP mode uses `_admconf.php['mode']['php']['navi']`, MD mode uses `_admconf.php['mode']['md']['navi']` and Rich mode uses `_admconf.php['mode']['rich']['navi']`. Manual `$GLOBALS['zconf']['navi']` in `/__config/conf.php` has priority and remains the main PHP developer configuration. Its standard form is `navikey=>['pageid']`, with optional nested subnavigation as the second array value. Labels belong in `$GLOBALS['zlangs'][language]['navi']`.

### Sitemap configuration

`/sitemap.xml` is controlled by `$GLOBALS['zconf']['sitemap']`. If the key is missing, the preset is `true`. To disable the sitemap manually in `/__config/conf.php`, set:

```php
'sitemap'=>false,
```

When `sitemap` is not set in `/__config/conf.php`, the Settings page can store the fallback value as `_admconf.php['sitemap']`. If `sitemap` is set in `conf.php`, the Settings checkbox is not shown.

The sitemap buffer is mode-separated. MD mode, Rich mode and PHP mode have separate page contents and therefore separate sitemap data. The active mode writes `/sitemap.xml`. Inactive mode buffers can be removed by maintenance after the sitemap max age.

Current sitemap rebuilding needs the admin area. `/admin/maint.php` keeps the sitemap buffer current through the admin maintenance pixel. MD and Rich editor saves can update the changed page immediately, but the general rebuild and PHP scan run only when `/admin/` is present and admin pages are used. If the admin area is removed or never opened, automatic sitemap rebuilding does not run.

## 8. Page variables

Optional page variables are defined in:

```text
/pages/zvars.php
```

Page variables are intended as support for normal website owners. A PHP developer can prepare reusable variables once, and the website owner can insert them in MD or Rich content without writing PHP.

Variables can be simple values such as an hourly rate:

```text
[VAR:hourly_rate_1]
```

They can also output complex generated HTML, for example a database-backed table, a current price list, an event list or a calculated form result.

The variable list is stored in `$ZVARS_PAGES`. The value is the label shown in the editor:

```php
$ZVARS_PAGES=[
	'hourly_rate_1'=>['en'=>'Hourly rate 1','de'=>'Stundensatz 1'],
	'price_table'=>['en'=>'Price table','de'=>'Preistabelle'],
];
```

The output is provided by `zvar_get($key, $lg)`. For translated local text arrays inside this function, use `zvarlg($tx)` when the helper should only read a matching value and must not change page language state:

```php
function zvar_get($key, $lg) {
	switch ($key) {
		case 'hourly_rate_1':
			return '120 EUR';
		case 'price_table':
			$tx=[
				'en'=>'Example',
				'de'=>'Beispiel',
			];
			return '<table><tr><td>'.zvarlg($tx).'</td><td>120 EUR</td></tr></table>';
	}
	return '';
}
```

MD mode and Rich mode can insert variable tokens such as:

```text
[VAR:price_table]
```

ZANACMS resolves allowed variable tokens at runtime. Only keys listed in `$ZVARS_PAGES` are replaced, and `zvar_get()` must exist. Unknown tokens remain visible.

## 9. ZTOKENS and MD additions/extensions for ZANACMS

ZTOKENS are ZANACMS replacement tokens in text content. They are not PHP function calls. They are written as text and resolved by the relevant ZANACMS runtime step.

Common ZTOKENS are:

```text
[@contact]
[[@contact]]
[footertext]
[VAR:contact_note]
```

Footer and config-based sidebar tokens are the ZTOKENS used in footer and `zconf['col2']` configuration. They work in all three modes because the footer is handled by shared runtime logic. Simple text variables such as `[footertext]` are read from `$GLOBALS['zlangs'][language]['vars']`. Page variable tokens such as `[VAR:name]` are used in MD and Rich content and are resolved from `/pages/zvars.php`.

MD additions/extensions for ZANACMS are the MD-mode additions to normal Markdown. They include internal `@page` links and compact image syntax for relative `/img` images. They are documented in [`guide/markdown-mode.md`]({{ '/guide/markdown-mode/' | relative_url }}).

## 10. CSS customisation

Use CSS files for visual customisation whenever possible.

### Editor classes

Optional editor-specific class settings can be placed in `/__config/customadmin.php`:

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

When `editorclasses` is set, the editor class select shows those entries instead of the built-in visible class entries. The remove option remains. Rich mode still cleans HTML classes; it allows built-in ZANACMS classes, configured editor classes, alignment classes and class names beginning with `zp`.

`editor_layout_css` is a file path below `/layout/`, for example `supercustom.css` or `html/own-design/editor.css`. It is loaded by the admin editors and is included by the built-in backup as that single configured file.

Global custom CSS:

```text
/layout/supercustom.css
```

Layout-specific custom CSS:

```text
/layout/<family>/<design>/custom.css
```

Do not change core CSS files such as `/layout/zpsuper.css` or supplied `/layout/<family>/<design>/zp.css`, because they can be overwritten by ZANACMS updates.

Generated variant files such as `zpdark.css` or `zpblue.css` belong to the supplied design. They can be recreated from `zp.css` and `design.ini`; use `custom.css`, `supercustom.css` or the documented extra variant file for local CSS.

Use `custom.css` when adjusting an existing supplied design. Use `supercustom.css` when the rule should apply to all selected designs.

## 11. HTML layouts

The `html` layout family allows simple layouts without PHP generator development. Supplied HTML designs and selectable CSS variants currently include `html/water`, `html/just-the-docs`, `html/just-the-docs.dark`, `html/z-airy` and `html/z-airy.blue`.

A design can be placed in:

```text
/layout/html/<design>/
```

For an own design, prefer a name starting with a hyphen so that it is clearly local and cannot collide with supplied update files:

```text
/layout/html/-owndesign/
```

Typical files are:

```text
design.html
zp.css        optional CSS for the normal ZANACMS CSS path
```

`custom.css` is not needed when the design itself is yours. Use `custom.css` mainly when you adjust an existing supplied design and want to keep those local adjustments separate from supplied CSS.

The HTML layout generator replaces documented placeholders in `design.html`. These HTML template replacement variables are called HVARS. Common HVARS are `~~ZHEAD~~`, `~~ZTITLE~~`, `~~ZH1~~`, `~~ZBODY~~`, `~~ZCOL2~~`, `~~ZNAVI~~`, `~~ZFOOT~~`, `~~ZBASEURL~~`, `~~ZDIRDESIGN~~` and `~~ZDIRSCRIPTS~~`. `~~ZCOL2~~` may come from `$GLOBALS['zdata']['col2']`, `$GLOBALS['zconf']['col2']` or from the sidebar files if no page or config value is present. The settings page is linked from the admin dashboard when `adminexposure >= 3`. To show the sidebar field there, the active design must define `columns=2` in `design.ini` and `col2` must not be set directly in `/__config/conf.php`; otherwise the default is `columns=1`. The full list is documented in [`layout/html/README.md`](https://github.com/gaxmann/zanacms/blob/master/layout/html/README.md). Framework CSS, JavaScript, fonts and images from the active design are referenced directly from `design.html` with `~~ZDIRDESIGN~~`; shared scripts use `~~ZDIRSCRIPTS~~`.

## 12. Layout families and generators

Layout families can provide their own generator.

Local project generator:

```text
/layout/<family>/_generator/zpgen.php
```

Supplied ZANACMS generator:

```text
/layout/_zpgen/<family>/zpgen.php
```

Generator development is documented separately in [`layout-generator-manual.md`]({{ '/guide/layout-generator-manual/' | relative_url }}).

This section only explains that generators are a public extension point. It does not replace the generator manual.

## 13. Admin language files

Admin interface translations are stored in:

```text
/admin/lang/
```

To use another admin interface language, set the standard language in `/__config/conf.php` and make sure the matching language file exists.

Example:

```php
'stdlang'=>'de',
```

This uses:

```text
/admin/lang/de.php
```

For a new translation, copy an existing language file to `/admin/lang/<language>.php` and translate the strings. The admin language directory is not scanned for all files; the selected language has to match the file name.

## 14. What is not a public interface

The following areas are not intended for normal site customisation:

```text
/zp/ runtime/core internals
/admin/ administration logic
internal editor helper functions
update and backup internals
generated cache files
generated Rich page output created by the editor
```

Change these only when working on ZANACMS itself.
