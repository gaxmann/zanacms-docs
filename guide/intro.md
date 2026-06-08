---
title: "ZANACMS Quick Start"
permalink: /guide/intro/
---
# ZANACMS Quick Start
## 1. General information

This guide explains the basic file structure and setup of ZANACMS. For license terms, see [ZANACMS license](https://github.com/gaxmann/zanacms/blob/master/LICENSE) and [`license-faq.md`]({{ '/guide/license-faq/' | relative_url }}). Bundled third-party components and notes about supported external design packages are listed in [`THIRD-PARTY-NOTICES.md`](https://github.com/gaxmann/zanacms/blob/master/layout/THIRD-PARTY-NOTICES.md).

ZANACMS supports three operating modes:

* **PHP mode** uses PHP files for page content and page data.
* **MD mode** uses Markdown files and is a regular lightweight content mode.
* **Rich mode** uses page data files and the rich text editor.

All modes use the same ZP system files, the same layout system and the same central configuration file, but they use different website structures. This file also explains shared topics that apply to all three modes.

## Where to find what

| Question | Document |
| --- | --- |
| I want to install ZANACMS | [Documentation start]({{ '/' | relative_url }}), then this file |
| I want to choose the right mode | this file |
| I write PHP pages | [`guide/php-mode.md`]({{ '/guide/php-mode/' | relative_url }}) |
| I write Markdown pages | [`guide/markdown-mode.md`]({{ '/guide/markdown-mode/' | relative_url }}) |
| I use the Rich editor | [`guide/rich-mode.md`]({{ '/guide/rich-mode/' | relative_url }}) |
| I need links, footer syntax, CSS, page variables or public functions | [`guide/interfaces.md`]({{ '/guide/interfaces/' | relative_url }}) |
| I build or change layout generators | [`layout-generator-manual.md`]({{ '/guide/layout-generator-manual/' | relative_url }}) |
| I have practical licence questions | [`license-faq.md`]({{ '/guide/license-faq/' | relative_url }}) |

---

## Which mode should I choose?

Use [**PHP mode**]({{ '/guide/php-mode/' | relative_url }}) if you want to write pages with PHP and arrays. Use [**MD mode**]({{ '/guide/markdown-mode/' | relative_url }}) if you want lightweight Markdown files with automatic cache building. Use [**Rich mode**]({{ '/guide/rich-mode/' | relative_url }}) if you want the normal visual editor workflow. In this file you can read about the shared parts. For public customisation and extension interfaces, see [`guide/interfaces.md`]({{ '/guide/interfaces/' | relative_url }}).

---

## Basic terms

A **page ID** is the internal name of a page, for example `index` or `contact`. Internal links use the page ID, not a full file path.

A **language code** is the short language code used by the website, normally two lowercase letters such as `en` or `de`. It is used in `$GLOBALS['zlangs']`, in MD filenames such as `index.en.md`, in Rich page language arrays and in URLs with `lg=en` when needed.

A **layout family** is the first part of a layout name, for example `html` in `html/water`. A **design** is the second part, for example `water`.

---

## Layout directory

The available layouts are stored in:

```text
/layout
```

Bundled layout directories in the core ZIP include for example:

```text
/layout/html/water
/layout/html/just-the-docs
/layout/html/z-airy
/layout/wondercms/sky
```

Additional tested design snapshots can be downloaded separately from `https://github.com/gaxmann/zp-cms-designs` and copied into `/layout`. Examples are:

```text
/layout/editorial/editorial
/layout/paradigm-shift/paradigm-shift
/layout/wordpress/graphy
/layout/wordpress/syntax
```

`water` is a water.css based layout. `just-the-docs` is a Just the Docs based HTML layout. `z-airy` is a bundled ZANACMS HTML layout. `sky` is the bundled WonderCMS Sky layout. `graphy` and `syntax` are older WordPress-based layouts available as tested external design snapshots. `editorial` is the HTML5 UP Editorial layout. `paradigm-shift` is a reduced HTML5 UP Paradigm Shift content-page layout.

Directories whose names start with `_`, such as `_generator` or `_zpgen`, are internal and are not selectable designs. _(In the `/layout/_zpgen` directory the supplied generators for the design families are stored. Don't touch this directory.)_ 

The active layout is selected in `/__config/conf.php`. Example:

```php
'layout'=>'html/water',
```

The first part is the layout family. The second part is the design. A layout name without slash, such as `foo`, is resolved as `foo/foo` (shortcut). Examples:

```php
'layout'=>'html/water',
'layout'=>'html/just-the-docs',
'layout'=>'wondercms/sky',
'layout'=>'editorial/editorial',
'layout'=>'paradigm-shift/paradigm-shift',
'layout'=>'wordpress/graphy',
'layout'=>'wordpress/syntax',
'layout'=>'wordpress/twentytwentyfive',
```

The examples from `editorial`, `paradigm-shift` and `wordpress` are additional tested designs. They are not necessarily included in the core ZIP. Download tested design snapshots separately from `https://github.com/gaxmann/zp-cms-designs` when needed.

Some layouts can use optional design values. `paradigm-shift/paradigm-shift` uses `layoutimg` for its large intro image:

```php
'layoutimg'=>['src'=>'img/example.jpg', 'pos'=>'center'],
```

`src` is the image path. `pos` is passed to the layout as image position, for example `center`, `top`, `bottom`, `left`, `right`, `top left` or `bottom right`. In PHP mode, the current page may override individual values through `$GLOBALS['zdata']['layoutimg']`; values not mentioned there keep the global `zconf` default.

A layout family may also include generator variants. The normal layout form is still:

```text
<family>/<design>
```

If a variant hint is needed, use:

```text
<family>.<variant>/<design>
```

Example:

```php
'layout'=>'wordpress.2015/graphy',
```

ZANACMS still uses the design directory `layout/wordpress/graphy`. It first checks for `layout/wordpress/_generator/zpgen.php` and otherwise loads the supplied generator `layout/_zpgen/wordpress/zpgen.php`. The family generator decides whether a variant hint is used or whether automatic selection is used.

_Layout families can ship together with a matching generator in `layout/<family>/_generator/zpgen.php`; this replaces the supplied ZP generator for full compatibility._

---

## Layout CSS

ZANACMS can load optional CSS files from the `layout` directory. Files are only included when they exist. Most generators use this order:

```text
/layout/zpsuper.css
/layout/<family>/<design>/zp.css
/layout/supercustom.css
/layout/<family>/<design>/custom.css
```

`/layout/supercustom.css` is optional global CSS for all layouts. Use it for CSS that should apply regardless of the selected layout. `/layout/<family>/<design>/zp.css` belongs to the supplied layout. It contains ZANACMS or theme-specific CSS and may be changed by ZANACMS updates. `/layout/<family>/<design>/custom.css` is optional user CSS for the selected layout. Use this file for your own changes. It is loaded after the supplied layout CSS. For example, with this setting:

```php
'layout'=>'html/water',
```

If you want update-safe CSS changes, put them into `custom.css` or `supercustom.css`, not(!) into `zp.css` or `zpsuper.css`. Supplied core CSS files such as `zp.css` and `zpsuper.css` can be overwritten by ZANACMS updates.

---

## Image directory

Your images go here:

```text
/img
```

The optional media gallery also reads this directory. Uploaded images are saved under `/img`, and large versions are saved under `/img/big` when available.

---

## Optional editor, media gallery and device manager

The editor, sidebar editor and media gallery are safety-disabled by default. Access can be enabled through the device manager `/admin/d.php` for single devices/browsers. PHP mode does not need `/admin/` for runtime; it needs `/admin/` only if image upload through the optional media gallery or device access tools are used. MD mode needs `/admin/` only when the built-in Markdown editor or media tools are used. If Markdown files are edited directly, for example via FTP, `/admin/` can be removed and MD runtime still rebuilds `/zpcache/`. Rich mode needs `/admin/`; without the Rich editor, the Rich mode concept is not useful. 

When access is active and the current device has the editing cookie, Rich mode shows a small `[e]` link to the rich text editor in the footer. MD mode uses the same `[e]` link for the Markdown editor. PHP mode shows a small `[i]` link to the media gallery in the footer if the admin tools are installed. For setup details, see [Editor access details](#editor-access-details).

In Rich mode, the editor can edit page content, create missing language versions, upload images into `/img`, insert images into the rich text body, and insert `[VAR:...]` variables from `/pages/zvars.php`. The Markdown editor can edit existing Markdown pages, create new Markdown pages, create missing language versions, upload images into `/img`, insert image code into Markdown text, insert page links, apply simple Markdown/HTML toolbar actions, and insert `[VAR:...]` variables from `/pages/zvars.php`. The sidebar editor edits global `~~ZCOL2~~` content when the active design defines `columns=2` in `design.ini` and `$GLOBALS['zconf']['col2']` is not set.

The media gallery displays images and can also delete images. The small red `×` button in the top-left corner of an image deletes the image after confirmation. If a matching large version exists in `/img/big`, both files are deleted.

### Admin overview page

The admin overview page is available at:

```text
/admin/
```

It shows the system status and links to the device manager. The other tools are available from the top admin navigation. 

The admin status table can also show a cached GitHub version check. This is only an information display; ZANACMS does not install updates automatically from this check. The check runs through a small admin maintenance pixel and uses a local cache, so the admin page itself does not wait for GitHub. The check is enabled by default and can be disabled in `/__config/conf.php`:

```php
'autoupdcheck'=>false,
```

The update status, update policy, warnings and breaking update boundaries are documented in [guide/update.md]({{ '/guide/update/' | relative_url }}).

The media gallery uses the ZP admin layout. It lists supported images from `/img`, allows uploads, creates resized files used by ZANACMS, and copies the matching image code when an image is clicked. In PHP mode this is a PHP image snippet. In MD mode this is Markdown image code. Rich mode normally inserts images directly through the rich text editor.

### Admin interface language

The admin interface ships with built-in language files in:

```text
/admin/lang
```

If no admin `lg` parameter is set, the admin interface uses the configured `stdlang` from `/__config/conf.php`. If the selected language is not one of the built-in admin languages, ZANACMS checks whether a matching file exists in `/admin/lang/<language>.php`.

If the file exists, that language is loaded and temporarily added to the admin language selector. If it does not exist, the normal ZANACMS language fallback order is used: requested language, `fallbacklg`, then `stdlang`; if no matching admin language file exists, the admin interface falls back to English. The admin language directory is not scanned. A custom admin language becomes available only when it is selected through `stdlang` or an explicit `lg` parameter and the matching file exists.

---

## Languages

The standard language is selected in `/__config/conf.php`. Example:

```php
'stdlang'=>'en',
```

The languages available in the language selector are defined by `$GLOBALS['zlangs']`. Example:

```php
$GLOBALS['zlangs']=[
	'en'=>[
		'sitetitle'=>'ZANACMS Example',
		'sitesub'=>'Minimal website',
	],
	'de'=>[
		'sitetitle'=>'ZANACMS Beispiel',
		'sitesub'=>'Minimale Webpräsenz',
	],
];
```

If a language exists in `$GLOBALS['zlangs']`, it is an official website language and can be shown in the language selector. MD and Rich pages can additionally have local page languages through existing page files or `langs` entries. These local languages are added to the language selector only on that page.

If an official website language is requested but the current page does not exist in that language, ZANACMS outputs the configured fallback language, or `stdlang` if no fallback is configured. Canonical points to the delivered content language, while further internal links stay in the requested official website language. If an unknown, non-official language is requested and the current page does not have that local language, canonical and further links use the delivered fallback language.

In PHP mode, `zautolg($tx)` registers the page languages from `array_keys($tx)` and stores the delivered content language. Build language-dependent HTML with `zlink()` or `zhref()` only after `zautolg()` has selected the language, or use ZTOKENS such as `ztokenhref()` in body strings. Old PHP pages that do not use `zautolg()` remain responsible for their own language handling. They may set `$GLOBALS['zdata']['lg']` manually when they know which content language is delivered.

---
---

## 2. Selecting the mode

The mode is selected in `/__config/conf.php`.

For PHP mode:

```php
'mode'=>'php',
```

For MD mode:

```php
'mode'=>'md',
```

For Rich mode:

```php
'mode'=>'rich',
```

---

## PHP mode structure

A minimal PHP mode website has this structure:

```text
/
├── __config/
│   └── conf.php
├── index.php
├── contact.php
├── layout/
└── zp/
```

PHP mode does not need a `pages` directory. PHP mode does not need `zpcache`. PHP mode does not need `/admin/` for runtime; it needs `/admin/` only if image upload through the optional media gallery or device access tools are used.

Read the PHP mode guide for details: [`guide/php-mode.md`]({{ '/guide/php-mode/' | relative_url }})

---

## MD mode structure

A minimal MD mode website has this structure:

```text
/
├── __config/
│   ├── conf.php
│   └── _admconf.php          optional admin-written navigation/sidebar data
├── index.php
├── pages/
│   ├── index.en.md
│   └── index.de.md
├── layout/
├── zp/
├── admin/                  optional editor/media tools
└── zpcache/
```

`pages` contains the Markdown files. `zpcache` contains generated cache files for MD mode and is still needed when Markdown files are edited directly by FTP. MD mode can run without `/admin/` when Markdown files are edited directly, for example via FTP.

Read the MD mode guide for details: [`guide/markdown-mode.md`]({{ '/guide/markdown-mode/' | relative_url }})

---

## Rich mode structure

A minimal Rich mode website has this structure:

```text
/
├── __config/
│   ├── conf.php
│   └── _admconf.php          optional admin-written navigation/sidebar data
├── index.php
├── pages/
│   └── index.data.php
├── layout/
├── zp/
└── admin/
```

`pages` contains `.data.php` files. Each page file returns page-wide values such as `mode` and `noindex`, plus the language data below `langs`. Admin-written navigation and sidebar data are stored in `/__config/_admconf.php` unless the matching key is set in `/__config/conf.php`. Navigation targets should use internal page IDs. Rich mode does not use `/zpcache/`. Rich mode needs `/admin/`; without the Rich editor, the Rich mode concept is not useful.

Read the Rich mode guide for details: [`guide/rich-mode.md`]({{ '/guide/rich-mode/' | relative_url }})

---

## URL rewrite prepared

ZANACMS works without URL rewrite by default. Optional URL rewrite is prepared and can be enabled later through `urlrewrite` in `/__config/conf.php` plus the matching root `.htaccess` example. Internal PHP links should use `zlink('page')` or `zhref('page', 'Text')`. Optional PHP link behaviour is passed as an array, for example `zlink('page', ['qsep'=>'?'])`. Navigation uses page IDs such as `contact`; Markdown uses `@page` link targets; the Rich editor uses `[@page]` while editing and exports `zlink()` calls; footer text uses `[@page]`/`[[@page]]` because `/__config/conf.php` should not contain function calls. Then the same content works with classic query URLs and with rewrite URLs such as `/en/contact`.

---

## Example files

ZANACMS includes example files for the three modes. Configuration examples and optional local access files are stored below `/__config/`.

For PHP mode:

```text
__config/conf.example-php.php
index.example-php.php
```

For MD mode:

```text
__config/conf.example-md.php
index.example-rich-or-md.php
/pages/example/md/
```

For Rich mode:

```text
__config/conf.example-rich.php
index.example-rich-or-md.php
/pages/example/rich/
```

To start a real website, copy the matching example configuration to `/__config/conf.php` and the matching index example to `index.php`:

```text
/__config/conf.php
index.php
```

PHP mode does not need example page files below `/pages/`. For MD mode, copy the Markdown example page files from `/pages/example/md/` to `/pages/`. For Rich mode, copy the Rich example page data from `/pages/example/rich/` to `/pages/`. Copy the contents of the selected example directory, not the directory itself, so the target files are for example `/pages/index.en.md` in MD mode or `/pages/index.data.php` in Rich mode.

---

## Next steps

After the basic setup, continue with the mode-specific guide:

* [`guide/php-mode.md`]({{ '/guide/php-mode/' | relative_url }}) for PHP mode
* [`guide/markdown-mode.md`]({{ '/guide/markdown-mode/' | relative_url }}) for MD mode
* [`guide/rich-mode.md`]({{ '/guide/rich-mode/' | relative_url }}) for Rich mode
* [`guide/interfaces.md`]({{ '/guide/interfaces/' | relative_url }}) for public customisation and extension interfaces

---

## 3. More Detailed Information

## Editor access details

The device manager (setup helper) is `/admin/d.php`, and it is locked by `/__config/EDITACCESS_ENABLE.php`. You can use `/__config/EDITACCESS_ENABLE.php` to open a short setup period during which editing cookies can be created or deleted.

The default values in `/__config/EDITACCESS_ENABLE.php` are `return [0,0];`. To open access to the device manager for a short setup step, edit the first two values in that file, for example to `return [5,0];`, `return [5,1];` or `return [5,2];`. The first number is the access time in minutes (capped at five minutes), and the second number is the number of device cookies that may be created during this setup period. After all allowed cookies are set, the access is immediately canceled.

If only one number is set, or if the second number is `0`, you can only view the device list and delete access cookies. - _On first access, the helper adds an internal UTC/GMT Unix expiry timestamp as third value, for example `return [5,1,1778681234];`. The timestamp is not extended by reloading. After the last allowed new device cookie, after deleting all access entries, or after expiry, the file is reset to `return [0,0];`._

On the helper page, choose `Add new device` to create an editing-cookie access entry for the current browser, or delete all access entries. Expired entries are removed automatically. The device manager also provides a “Clear all HTML cache” action. It recursively deletes generated `zpcache.html` files from the website tree. This is useful after layout or generator changes when cached template output didn't rebuild automatically.

The editor and gallery are not shown if the cookie data is missing, has no current access entry, or the matching entry has expired. The edit cookie data is stored in `/admin/_zdata.php`.

---

## HTML status comment

ZANACMS writes a small HTML comment at the end of generated pages if enabled:

```html
	<!-- RAM: 2.00 MiB (685 KiB) | DB: 0 | Render: 1.26 ms -->
```

The first RAM value is the memory block reserved by PHP, including allocator overhead. The second RAM value is the current script memory usage. `DB` shows the counted database queries. `Render` shows the generation time for the request. To hide the status comment, set this in `/__config/conf.php` or remove the entry:

```php
'showstat'=>false,
```

---

## Layout families, generators and third-party themes

ZANACMS separates layout support into a layout family and a design:

```text
<family>/<design>
```

The design directory contains the layout or original theme files under `layout/<family>/<design>/`. ZANACMS first looks for a local family generator under `layout/<family>/_generator/zpgen.php`. If that file is missing, it uses the supplied ZP generator under `layout/_zpgen/<family>/zpgen.php`.

This makes it possible to support very different kinds of layouts without changing the page content itself. Some layout families are simple and mostly load a classless CSS file, for example `html/water` or `html/just-the-docs`. Other layout families adapt complete theme structures, for example WordPress themes or WonderCMS themes.

Many older WordPress themes work well because their HTML structure is still relatively simple. Some newer WordPress themes may also work, but modern block themes or themes with heavy JavaScript, complex PHP logic or build pipelines may need extra adjustments.

The easiest way to get started is to download one of the tested design snapshots from:

```text
https://github.com/gaxmann/zp-cms-designs
```

## HTML cache

The complex layout families (`wordpress`, `wondercms`, `editorial`, `paradigm-shift`) use HTML caching. The cache is renewed when the selected generator entry point, a relevant variant like `gen*.php`, or `zp.css` is newer than `zpcache.html` (or if `zpcache.html` is missing). The selected generator is either a local family generator under `layout/<family>/_generator/` or a supplied ZP generator under `layout/_zpgen/<family>/`. Cached generators can use the central `zgencleanhtml()` helper to remove unknown PHP code and HTML comments from the generated cache template.

If cached HTML output must be rebuilt manually, use `/admin/d.php` and choose “Clear all HTML cache”.

### References

`layout/_zpgen/wondercms/zpgen.php` is the reference implementation for a generator with HTML caching. `zp/zpgen.php` is the standard HTML template generator for minimal HTML designs without caching. Not caching is only advised when the HTML generation is simple.

The `wondercms` family generator takes a complete WonderCMS `theme.php`, reads it, replaces known WonderCMS output positions with ZANACMS content and creates a cleaned `zpcache.html` cache. The included `wondercms/sky` layout uses the original WonderCMS Sky theme files. Other WonderCMS themes may work with the same generator if they use the usual WonderCMS theme placeholders. They are not guaranteed to work unchanged, but they are usually easy to test: copy the theme into `/layout/wondercms/<design>/` and select it as:

```php
'layout'=>'wondercms/<design>',
```

Cached templates use central placeholders. `~~ZBASEURL~~` is the explicit basepath placeholder for local layout resources such as CSS, JavaScript and images. The full placeholder list is in [`layout-generator-manual.md`]({{ '/guide/layout-generator-manual/' | relative_url }}#html-cache-standard).

## WordPress 2015 generator scripts

The WordPress variants (`layout/_zpgen/wordpress/gen2015.php` and `layout/_zpgen/wordpress/genzp.php`) require the following local JavaScript files for compatibility with classic WordPress themes, which are listed in the `/layout/scripts` directory:

- `1.12.4/jquery.min.js`
- `jquery-migrate-1.4.1.min.js`

These files are required for classic WordPress themes that expect WordPress to provide jQuery. See [`THIRD-PARTY-NOTICES.md`](https://github.com/gaxmann/zanacms/blob/master/layout/THIRD-PARTY-NOTICES.md) for license information about the bundled script files and notes about external design packages.

## Historical info

ZP originally stood for ZANAPRESS, from when the project started as a WordPress theme adapter with version 1 in 2014.

---

## 4. Customisation and public interfaces

ZANACMS should be customised through documented interfaces, not by changing core files. The detailed reference is [`guide/interfaces.md`]({{ '/guide/interfaces/' | relative_url }}). The most important points are kept here because they affect normal setup work.

Public customisation areas are:

```text
/__config/conf.php                         central site configuration
$GLOBALS['zconf']                  configuration values
$GLOBALS['zlangs']                 language-dependent site texts
$GLOBALS['zdata']                  data of the currently rendered page
/pages/zvars.php                   optional variables for MD and Rich content
/layout/supercustom.css            optional global custom CSS
/layout/<family>/<design>/custom.css
                                   optional CSS for an existing selected design
/layout/html/<design>/design.html  own HTML design shell
/admin/lang/<language>.php         optional admin translation file
```

Core files in `/zp/` are runtime/core files. Admin files in `/admin/` are administration files. They are not normal customisation files.

## Internal links and footer links

Use the right link syntax for the current context:

```text
PHP page code:      zlink('contact') or zhref('contact', 'Contact')
MD body text:       [Contact](@contact)
Rich editor body:   <a href="[@contact]">Contact</a>
Footer in all modes: [[@contact]] or <a href="[@contact]">Contact</a>
```

Footer text is configured in `/__config/conf.php`, but `/__config/conf.php` must not depend on runtime function calls. Therefore footer links use the token syntax `[@page]` and `[[@page]]`, not direct `zlink()` or `zhref()` calls. Simple text variables such as `[footertext]` are read from `$GLOBALS['zlangs'][language]['vars']` before link tokens are resolved.

## Page variables

MD mode and Rich mode can use variables from `/pages/zvars.php`:

```text
[VAR:price_table]
```

This is mainly intended for normal users. A PHP developer can prepare variables that output simple values such as an hourly rate or complex generated HTML such as database-backed tables. The website owner can then insert the prepared variable through the editor without writing PHP. Inside `/pages/zvars.php`, `zvarlg($tx)` can be used to read the matching value from a translated array without changing the current page language state.

## CSS and own designs

For update-safe CSS changes, use:

```text
/layout/supercustom.css
/layout/<family>/<design>/custom.css
```

Do not change supplied core CSS files such as `/layout/zpsuper.css` or supplied `/layout/<family>/<design>/zp.css`, because they can be overwritten by ZANACMS updates.

For an own HTML design, use a local design directory, preferably with a leading hyphen:

```text
/layout/html/-owndesign/design.html
/layout/html/-owndesign/zp.css
```

`custom.css` is not needed when the whole design is yours. It is mainly useful when adjusting an existing supplied design.

## Advanced interface reference

For complete details, including all parameters of `zlink()`, `zhref()`, `ztokenhref()`, `zautop()`, `zsetlangs()`, `zautolg()`, `zvarlg()`, `zquote()`, `zoutimg()` and `out_page()`, see [`guide/interfaces.md`]({{ '/guide/interfaces/' | relative_url }}).

---

## 5. Glossary

This glossary explains common terms used in the ZANACMS documentation.

**admin file**  
An administration file below `/admin/`, for example the editor, sidebar editor, navigation editor, media gallery or update tools.

**configuration directory**  
The project directory `/__config/`. It contains configuration examples, the active `conf.php`, optional admin-written `_admconf.php`, `hash.php` when needed and `EDITACCESS_ENABLE.php`.

**configuration file**  
The project file `/__config/conf.php`. It contains manual website configuration such as mode, layout, languages, navigation and footer text. Values set here have priority over admin-written values from `_admconf.php`. It should contain plain configuration data and should not depend on runtime function calls.

**core file**  
A runtime file below `/zp/`. Core files are not normal website customisation files.

**design**  
The second part of a layout name, for example `water` in `html/water`.

**edit access enable file**  
The local setup file `/__config/EDITACCESS_ENABLE.php`. It opens a short setup window for creating or deleting editor access cookies.

**fallback language**  
The optional language from `fallbacklg` in `/__config/conf.php`. It can be used when a requested page language is missing.

**footer tokens**  
ZTOKENS and simple text variables used in footer and layout text, for example `[[@contact]]`, `[@contact]` or `[footertext]`.

**generator**  
A PHP file that turns the prepared page data into final HTML output. Most users only select layouts; generator details are for layout and theme work.

**HTML cache**  
A generated layout cache file, usually `zpcache.html`, used by some complex layout families.

**HTML layout**  
A layout from the `html` family. It uses `/layout/html/<design>/design.html` and HVARS instead of a custom PHP generator.

**HVARS**  
HTML template replacement variables used by HTML layouts. HVARS are placeholders in `/layout/html/<design>/design.html` that the HTML layout generator replaces with page, navigation, footer or asset output.

**language code**  
The short website language code, normally two lowercase letters such as `en` or `de`. It is used in `$GLOBALS['zlangs']`, MD filenames, Rich page language arrays and URLs with `lg=en` when needed.

**language set**  
The flat list of language codes available for the current page, for example `['en','de']`. ZANACMS uses this list to select the delivered content language.

**layout**  
The selected visual output, written as `<family>/<design>`, for example `html/water`.

**layout family**  
The first part of a layout name, for example `html` in `html/water`.

**layout image**  
An optional image or image setting used by some layouts through `layoutimg`. It is part of the layout/design output, not necessarily part of the page body.

**MD additions/extensions for ZANACMS**  
Small ZANACMS additions to normal Markdown in MD mode, for example internal `@page` links and compact image syntax for files from `/img`.

**MD editor**  
The optional admin editor for Markdown pages in MD mode.

**MD mode**  
A mode where page content is stored in Markdown files below `/pages/`. ZANACMS builds generated cache files in `/zpcache/`.

**mode**  
The content mode selected in `/__config/conf.php`: `php`, `md` or `rich`. The mode decides where page content is stored and how it is loaded.

**navigation**  
The page menu structure of the website. It is configured manually in `/__config/conf.php` or, when `navi` is not set there, by the admin editor in `/__config/_admconf.php`.

**page ID**  
The internal name of a page, for example `index` or `contact`. Internal links use the page ID, not a full file path.

**page image**  
An optional image attached to the current page through `pageimg`. Supported layouts or output helpers can place it into the page body.

**page variable**  
A reusable variable from `/pages/zvars.php` that can be inserted into MD or Rich content with `[VAR:name]`. Page variables are also called ZVARS.

**PHP mode**  
A mode where each page is a PHP file in the website root. The page prepares `$GLOBALS['zdata']` and calls `out_page()`.

**public interface**  
A documented file, array, function or directory intended for normal customisation, for example `/__config/conf.php`, `zlink()` or `/layout/supercustom.css`.

**Rich editor**  
The admin visual editor used for Rich mode pages.

**Sidebar editor**  
The admin editor for file-based `~~ZCOL2~~` content. It is available when `zconf['col2']` is not set.

**Rich mode**  
A mode where page content is stored in `.data.php` files below `/pages/` and is normally edited through the Rich editor.

**standard language**  
The default website language from `stdlang` in `/__config/conf.php`.

**URL rewrite**  
Optional URL handling that can turn internal links into path-style URLs such as `/en/contact` instead of query-style URLs.

**zpcache**  
The generated Markdown cache directory used by MD mode.

**ZTOKEN link**  
An internal link that keeps `[@page]` as a token until final output, for example an anchor created with `ztokenhref('contact', 'Contact')`.

**ZTOKENS**  
ZANACMS replacement tokens in text content. Some ZTOKENS and simple text variables work in shared places such as footer and layout text, for example `[[@contact]]`, `[@contact]` or `[footertext]`. Other tokens are used in MD or Rich content, for example `[VAR:name]` for page variables. PHP pages can create token links with `ztokenhref()`.

**ZVARS**  
The page variable system in `/pages/zvars.php`. ZVARS can provide simple values such as `price_per_hour_1`, or complex prepared output such as generated tables. They are meant to let normal editors use prepared values without writing PHP.
