---
title: "ZANACMS Interfaces"
permalink: /guide/interfaces/
---
# ZANACMS Interfaces

This document describes the public customisation and extension interfaces of ZANACMS. It is intended for advanced users who build their own site structure, layout adjustments, local variables or small integrations. It is not a guide for changing ZANACMS core files.

For the first setup, use [`guide/index.md`]({{ '/guide/' | relative_url }}). For mode-specific page formats, use [`guide/php-mode.md`]({{ '/guide/php-mode/' | relative_url }}), [`guide/markdown-mode.md`]({{ '/guide/markdown-mode/' | relative_url }}) and [`guide/rich-mode.md`]({{ '/guide/rich-mode/' | relative_url }}).


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
/__config/EDITACCESS_ENABLE.php
$GLOBALS['zconf']
$GLOBALS['zlangs']
$GLOBALS['zdata']
/pages/zvars.php
/pages/__col2.<lg>.md
/pages/__col2.data.php
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
| Site configuration | `/__config/conf.php`, `$GLOBALS['zconf']` | mode, layout, footer, language defaults and site options |
| Site texts | `$GLOBALS['zlangs']` | site title, subtitle, navigation labels and layout text variables |
| Current page | `$GLOBALS['zdata']` | title, heading, body, page language and page image/layout data |
| PHP page helpers | `out_page()`, `zlink()`, `zhref()`, `ztokenhref()`, `zautop()`, `zsetlangs()`, `zautolg()`, `zquote()`, `zoutimg()` | normal PHP page output and internal links |
| MD/Rich variables | `/pages/zvars.php` | prepared values or generated output for normal editors |
| Sidebar files | `/pages/__col2.<lg>.md`, `/pages/__col2.data.php` | file-based content for `~~ZCOL2~~` when `zconf['col2']` is not set |
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
col2            optional config-based sidebar HTML for layouts with `~~ZCOL2~~`; if set, the sidebar editor is locked
headlast        optional HTML directly before </head>
bodylast        optional HTML directly before </body>
adminexposure   optional admin surface level, default 2; /zp/isdev.txt acts as 99
autoupdcheck   optional; false disables the cached GitHub status check in /admin/
favicon         optional favicon/head markup
```

Configuration values must be plain data. They must not depend on functions from `/zp/zana.php`. The device manager setup window is controlled by `/__config/EDITACCESS_ENABLE.php`.

The optional GitHub status check in the admin status table is enabled by default. It is only a cached information display. To disable it, set:

```php
'autoupdcheck'=>false,
```

The status check reads the latest version from GitHub and stable/warning policy data from `img/updatepolicy.json`. `warnings` can mark installed version ranges for which an update is recommended. `endofline` marks breaking versions; the admin dashboard will not present breaking updates as normal safe updates. Details are documented in [guide/update.md]({{ '/guide/update/' | relative_url }}).

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

`$GLOBALS['zconf']['col2']` uses the same text logic as footer lines. A layout can output it with `~~ZCOL2~~`; text variables are read from `$GLOBALS['zlangs'][language]['vars']` first, then link tokens are resolved. If `$GLOBALS['zconf']['col2']` is set, it has priority and the sidebar editor is locked. If it is not set, the editor can store file-based sidebar content in `/pages/__col2.<lg>.md` for MD mode or `/pages/__col2.data.php` for Rich/PHP mode.

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

Sidebar editor files store only sidebar data, not normal page data. MD mode uses `/pages/__col2.<lg>.md`. Rich mode and PHP mode use `/pages/__col2.data.php`. The optional sidebar image is stored once as `col2img.src` and rendered with class `col2img` before the sidebar text in all languages.

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

The output is provided by `zvar_get($key, $lg)`:

```php
function zvar_get($key, $lg) {
	switch ($key) {
		case 'hourly_rate_1':
			return '120 EUR';
		case 'price_table':
			return '<table><tr><td>Example</td><td>120 EUR</td></tr></table>';
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

Global custom CSS:

```text
/layout/supercustom.css
```

Layout-specific custom CSS:

```text
/layout/<family>/<design>/custom.css
```

Do not change core CSS files such as `/layout/zpsuper.css` or supplied `/layout/<family>/<design>/zp.css`, because they can be overwritten by ZANACMS updates.

Use `custom.css` when adjusting an existing supplied design. Use `supercustom.css` when the rule should apply to all selected designs.

## 11. HTML layouts

The `html` layout family allows simple layouts without PHP generator development. Supplied HTML designs currently include `html/water`, `html/just-the-docs`, `html/z-airy` and `html/z-airy.blue`.

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
zp.css
```

`custom.css` is not needed when the design itself is yours. Use `custom.css` mainly when you adjust an existing supplied design and want to keep those local adjustments separate from supplied CSS.

The HTML layout generator replaces documented placeholders in `design.html`. These HTML template replacement variables are called HVARS. Common HVARS are `~~ZHEAD~~`, `~~ZTITLE~~`, `~~ZH1~~`, `~~ZBODY~~`, `~~ZCOL2~~`, `~~ZNAVI~~`, `~~ZFOOT~~` and `~~ZBASEURL~~`. `~~ZCOL2~~` may come from `$GLOBALS['zconf']['col2']` or from the sidebar editor files if the config value is absent. To show the sidebar editor in the admin menu, the active design must define `columns=2` in `design.ini`; otherwise the default is `columns=1`. The full list is documented in [`layout/html/README.md`](https://github.com/gaxmann/zanacms/blob/master/layout/html/README.md). Framework CSS, JavaScript, fonts and images are referenced directly from `design.html` with `~~ZBASEURL~~`.

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
