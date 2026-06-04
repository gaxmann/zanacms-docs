# ZANACMS: Layout Generator Manual

This file defines how layout generator work should be done in ZANACMS. It is written as an implementation manual for generator tasks, not as end-user documentation.

Layout generators are specialised maintenance code. They should be implemented directly, effectively and in a maintainable way. Do not over-defend internal project interfaces. Follow the existing project style, keep cache paths lean and avoid broad abstraction unless the generator family actually needs it.

## Generator or html?

If the task is not to support a complete layout family, but only to implement one design or adapt an existing HTML/theme design to ZANACMS, the `html` family is usually a better fit than a new generator.

A generator is useful when many designs from the same source must be processed by the same rules. Examples are the WordPress and WonderCMS generators. They map typical files, functions and structures of a whole theme family to ZANACMS.

For a single design, that is usually too much effort. In that case this is often enough:

```text
layout/html/<design>/design.html
layout/html/<design>/zp.css
```

`design.html` is the HTML shell of the design. ZP placeholders are inserted there. The full central placeholder list is in [HTML cache: standard](#html-cache-standard).

`zp.css` is included through the normal ZP CSS order if the file exists. Framework CSS, JavaScript, images, fonts and other files are referenced directly in `design.html`. The `html` generator does not automatically load `design.css` or `design.js`. `~~ZNAVI~~` outputs the complete neutral navigation. `~~ZNAVI_LI~~` outputs only the `<li>` elements so that `design.html` can provide its own navigation wrapper. `~~ZHOMEURL~~` is the language-dependent home page URL.

The usual process is:

1. Build the desired design once as a basic HTML structure.
2. Replace dynamic content areas with ZP placeholders.
3. Put CSS, JS, images and fonts into the design directory and reference them directly from `design.html`.
4. Only then check whether a dedicated family generator is really necessary.

If a design can be implemented cleanly through `html`, do not write a new generator. A generator normally belongs to a layout family, not into an individual design directory.

## Read before starting

For generator or cache work, first check these files in the current package:

```text
zp/zana.php
layout/_zpgen/wondercms/zpgen.php
zp/zpgen.php
layout/_zpgen/editorial/zpgen.php
layout/_zpgen/paradigm-shift/zpgen.php
layout/_zpgen/wordpress/gen2015.php
layout/_zpgen/wordpress/gen2026.php
layout/_zpgen/wordpress/genzp.php
zp/md.php
README.md
guide/index.md
guide/php-mode.md
guide/markdown-mode.md
```

`layout/_zpgen/wondercms/zpgen.php` together with `layout/wondercms/sky` is the reference for `zpcache.html` in template-based generators.

`zp/zpgen.php` is the generic generator for simple HTML designs based on `design.html`.

`gen2015.php` and `gen2026.php` are internal variants of the WordPress family generator. Do not use them as a general pattern because they emulate WordPress functions. `genzp.php` is a plain `zpdesign.html` reader for prepared WordPress designs.

`layout/_zpgen/editorial/zpgen.php` and `layout/_zpgen/paradigm-shift/zpgen.php` also use HTML cache, but they are not the primary reference for new template-based generators.

## Generator code rules

Keep generator code compact. Do not add unnecessary wrappers. Do not introduce functions that only pass through to one central function under a different name.

Do not add new SEO, meta or cache variables unless the task explicitly asks for them. Stay with the central ZP cache variables.

Do not scan folders to determine cache freshness. Cache checks stay lean.

Do not invent placeholders such as `navi_1`, `navi_2`, `meta_description`, `canonical`, `robots`, `css` and similar values. Use only central ZP cache variables.

Global functions go into `zp/zana.php` only when they are genuinely needed by several generators. Generator-specific functions stay in the generator and use the generator prefix.

When a file with a version header is changed, increase the patch version and update the date.

If a code file is changed, return a ZIP download at the end. Do not provide fonts from the container.

## Layout names

ZP layout names normally follow this form:

```text
<family>/<design>
```

Examples:

```php
'layout'=>'wondercms/sky',
'layout'=>'editorial/editorial',
'layout'=>'paradigm-shift/paradigm-shift',
'layout'=>'wordpress/graphy', # optional: wordpress.2015/graphy
'layout'=>'wordpress/twentytwentyfive', # optional: wordpress.2026/twentytwentyfive
'layout'=>'html/mydesign',
```

A layout name without a slash is generally read as `<name>/<name>`, for example `test` as `test/test`. WordPress layouts are explicitly configured as `wordpress/<design>`. Do not add extra special aliases for individual new families.

Internally, WordPress designs are stored under:

```text
layout/wordpress/<design>/
```

ZANACMS loads generators in this fixed order:

```text
layout/<family>/_generator/zpgen.php
layout/_zpgen/<family>/zpgen.php
```

The first path is an optional local family generator and has priority. The second path is the supplied ZP generator.

Internal generator variants are in the same generator directory and use this naming scheme:

```text
gen*.php
```

A variant hint in the layout name uses:

```text
<family>.<variant>/<design>
```

Example:

```php
'layout'=>'wordpress.2015/graphy',
```

`zlayout()` always returns exactly three internal values:

```php
[family, design, variant]
```

Without a variant hint, the third value is `false`. `zana.php` does not choose a generator variant. It only loads the generator entry point: first `layout/<family>/_generator/zpgen.php`, otherwise `layout/_zpgen/<family>/zpgen.php`. The respective family generator decides whether a variant hint is used or whether auto/preset logic applies.

`_generator` and `_zpgen` are internal and are not selectable designs. Do not build layout paths manually inside generators. Always use `zlayoutdir()` and `zlayoutfile()`.

## Generator types and cache decision

Before generator work, first decide which generator type is involved. This decision is important and must not be skipped for the sake of uniformity.

### Type A: lightweight generators for native ZP themes

Example:

```text
zp/zpgen.php
```

These generators are meant for native, direct ZP themes. They create HTML from prepared ZP data and should stay as lean as possible.

HTML cache is not mandatory for this type. Missing cache is not an error here. The decision must be made per generator. If the generator is very fast and does not need to load heavy template files, theme functions or foreign framework structures, HTML cache can add more complexity than benefit.

Do not add cache merely for uniformity. Add cache only when there is a concrete technical benefit or when the task explicitly requires it.

### Type B: generators for ready-made or imported themes

Examples:

```text
layout/_zpgen/wondercms/zpgen.php
layout/_zpgen/editorial/zpgen.php
layout/_zpgen/paradigm-shift/zpgen.php
layout/_zpgen/wordpress/zpgen.php
layout/_zpgen/wordpress/gen2015.php
layout/_zpgen/wordpress/gen2026.php
```

These generators adapt ready-made or imported themes to ZANACMS. Such themes are often heavier and contain more HTML structure, CSS, template logic or, in the WordPress case, emulated theme functions.

For this type, HTML cache must generally be considered and implemented. For new or adapted generators for ready-made/imported themes, cache support is the default requirement, not an optional later improvement.

The normal page request must use the fast cache path:

```text
check cache
read zpcache.html
replace ZP variables
output
```

Heavy theme initialisation belongs only into the cache build path. For WordPress special generators this means in particular: load `functions.php` only when `zpcache.html` must be rebuilt.

When building a new generator for an imported theme, read `layout/_zpgen/wondercms/zpgen.php` as the primary cache reference. `gen2015.php` and `gen2026.php` are internal WordPress variants because of WordPress emulation and are not the general pattern.

## HTML cache: standard

Slow or template-based generators normally use `zpcache.html` in the active design directory:

```text
layout/<family>/<design>/zpcache.html
```

The central cache functions are in `zp/zana.php` in this section:

```php
# --- html caching ---
```

Central constants:

```php
const ZP_HTML_CACHE = 'zpcache.html';

const ZP_HTML_CACHE_VARS = [
	'htmllang'=>'~~ZHTMLLG~~',
	'head'=>'~~ZHEAD~~',
	'headlast'=>'~~ZHEADLAST~~',
	'baseurl'=>'~~ZBASEURL~~',
	'sitetitle'=>'~~ZSITETITLE~~',
	'sitesub'=>'~~ZSITESUB~~',
	'title'=>'~~ZTITLE~~',
	'homeurl'=>'~~ZHOMEURL~~',
	'navi'=>'~~ZNAVI~~',
	'navi_li'=>'~~ZNAVI_LI~~',
	'naviafter'=>'~~ZNAVIAFTER~~',
	'menutx'=>'~~ZMENUTX~~',
	'h1'=>'~~ZH1~~',
	'body'=>'~~ZBODY~~',
	'layoutimg'=>'~~ZLAYOUTIMG~~',
	'pageimg'=>'~~ZPAGEIMG~~',
	'foot'=>'~~ZFOOT~~',
	'foot0'=>'~~ZFOOT0~~',
	'foot1'=>'~~ZFOOT1~~',
	'bodylast'=>'~~ZBODYLAST~~',
];
```

Do not add further cache placeholders without an explicit task.

For static layout resources in the HTML cache, layouts explicitly put the base path before local resources:

```html
<link rel="stylesheet" href="~~ZBASEURL~~layout/html/water/water.min.css">
<script src="~~ZBASEURL~~layout/html/water/theme.js"></script>
<img src="~~ZBASEURL~~img/logo.png" alt="">
```

Generators that create HTML cache use `zurl_html_resource($file)` for local resources. Normal PHP output may use `zurl_resource($file)`.

`zhtmlcachrepl()` replaces all values from `ZP_HTML_CACHE_VARS` and internally calls `zgen_hvars($var, $html, $ignore[$var])` for this. New central HTML/cache additions belong in `zgen_hvars()`, not directly in generators and not as special logic in `zhtmlcachrepl()`.

Use these functions:

```php
zhtmlcacheold()
zhtmlcacheold(basename(__FILE__, '.php'))
zhtmlcacheread()
zhtmlcachewrite($html)
zgencleanhtml($html)
zhtmlcachrepl($html, $vars, $ignore=[])
zgen_hvars($var, $html='', $ignore=[])
```

Do not reintroduce wrappers such as `zhtmlcachefile()`.

`zhtmlcacheold($generatorname='')` currently checks only:

```text
zgenerator()
layout/<family>/<design>/zp.css
```

`zgenerator()` is the actually selected generator entry point from `layout/<family>/_generator/zpgen.php` or `layout/_zpgen/<family>/zpgen.php`.

When `$generatorname` is empty, the selected generator entry point is checked. Therefore use:

```php
zhtmlcacheold()
```

for normal family generators such as `wondercms`, `editorial` and similar generators.

For WordPress variants, additionally check the current variant file:

```php
zhtmlcacheold(basename(__FILE__, '.php'))
```

This means the file name of the internal generator variant without `.php`, for example `gen2015` for `gen2015.php` or `gen2026` for `gen2026.php`. This is only one additional file for the cache age check. It is not a public layout name. The public layout configuration remains, for example:

```php
'layout'=>'wordpress/graphy',
```

Current rule: generators do not pass additional files for cache freshness. Do not check `functions.php`, `header.php`, `page.php`, `footer.php`, `theme.json`, `style.css`, template parts or whole directories.

`zgen_hvars($var, $html='', $ignore=[])` is the central interface for HTML/cache variables. It must accept all keys from `ZP_HTML_CACHE_VARS`. Individual values may initially only be passed through, but they must remain callable. `baseurl` returns the base path for layout resources and is used as `~~ZBASEURL~~` in HTML templates. `head` adds the central head block including `robots`, page title, generator meta, favicon, description, canonical, ZP CSS, hreflang and optional page-data head. `body` automatically adds `pageimg` if the generator does not use its own `pageimg` position in the template. `htmllang` returns the complete `lang="..."` attribute. `headlast` is optional HTML at the end of `<head>`, after the central ZP head block. `sitetitle` is the language-dependent website title. `sitesub` is the language-dependent site subtitle and has no fallback. `title` is the page title; if the page title is empty, `h1` is used. `h1` itself has no fallback. `menutx` is the language-dependent menu button text. `naviafter` is optional HTML directly after the navigation. `layoutimg` is optional layout image/design HTML for layouts that support such values. `foot` is the previous standard footer. `foot0` and `foot1` are optional separate footer areas for layouts with several footer positions. `bodylast` is optional HTML directly before `</body>`. When calling `zgen_hvars()` directly, `$ignore` is a simple list such as `['title', 'robots']`. With `zhtmlcachrepl()`, `$ignore` is grouped by area, for example `['head'=>['title'], 'body'=>['pageimg']]`. This makes it possible to suppress automatic additions deliberately.

## HTML cache: generator integration

The generator only decides how the cache HTML is built. The basic cache functions stay in `zana.php`.

Typical flow:

```php
function out_gen() {
	if (zhtmlcacheold()) {
		$html=<family>_cache_build();
		$html=zgencleanhtml($html);
		zcheckhtmlempty($html);
		zhtmlcachewrite($html);
	}
	$html=zhtmlcacheread();
	echo zhtmlcachrepl($html, <family>_vars());
}
```

This is only the principle. Adapt code to the existing style of the generator. `zgencleanhtml()` removes unknown PHP code and HTML comments from the cache template. The function belongs in the cache build path, not as an automatic change in `zhtmlcachewrite()`.

`zpcache.html` is not put into the ZIP.

The stored HTML cache starts with `ZP_HTML_CACHE_HEAD`, currently `ZCACHVERS:000001|`. `zhtmlcachewrite()` writes this header regardless of the cache path passed in. `zhtmlcacheread()` removes the header when it exists and caches the result in `MCACH_zhtmlcache`. A page build uses either the standard HTML cache or an alternative HTML cache; it does not switch between both within the same build. When the cache template schema in `zana.php` changes for a technical reason, this header is increased. `zp/zana.php` itself is not included in the HTML cache freshness check by file time.

If after an update no rebuild happens because of ZIP timestamps, do not delete automatically. Mention briefly in the final note that the existing `zpcache.html` can be deleted manually if needed.

## Cache template and placeholders

The cache template contains only the central values from `ZP_HTML_CACHE_VARS`.

When rebuilding a template, insert `~~ZHEAD~~` as generically as possible directly before `</head>`. Do not attach it to a specific CSS or meta line of a theme. The generator passes its head part to `zgen_hvars('head', $head)`. ZP CSS is added there centrally and after the passed head part so that ZP fixes can override design CSS rules. CSS already fixed in the cache template before `~~ZHEAD~~` is not affected by this.

In generators, avoid hard-coding:

```php
'~~ZHEAD~~'
```

Use this instead:

```php
ZP_HTML_CACHE_VARS['head']
```

The same applies to `baseurl`, `headlast`, `body`, `bodylast`, `h1`, `layoutimg`, `navi`, `naviafter`, `menutx`, `sitesub`, `sitetitle`, `foot`, `foot0`, `foot1`, `title` and `htmllang`.

The language switcher is deprecated. Do not use `~~ZLANGSWITCH~~`. Language links belong in the navigation if they exist.

## Layout image/design values

Layouts can read optional design values via `zgetconf('layoutimg', [])`. The function uses `$GLOBALS['zconf']` as default and `$GLOBALS['zdata']` as page-specific override. Arrays are merged; simple values such as strings, booleans or numbers are overwritten directly.

Layouts can read optional design values from `$GLOBALS['zconf']['layoutimg']`. The value is a small associative array. `paradigm-shift/paradigm-shift` currently uses:

```php
'layoutimg'=>['src'=>'img/default.jpg', 'pos'=>'center'],
```

`src` is the image path. `pos` is the image position of the layout. Pages may override individual values via `$GLOBALS['zdata']['layoutimg']`. Only the specified keys are replaced; all other values remain from `$GLOBALS['zconf']['layoutimg']` if the generator uses `zgetconf('layoutimg', [])`.

```php
$GLOBALS['zdata']['layoutimg']=['src'=>'img/page.jpg'];
```

Generators that use HTML cache must use the central placeholder `ZP_HTML_CACHE_VARS['layoutimg']` for page-dependent layout image output and replace it in the fast path via `zhtmlcachrepl()`. Do not store it as a fixed image in the cache template if the value is overrideable per page.

## HTML cache: fast path

For cache generators, the normal page request must be as short as possible:

```text
check cache
read zpcache.html
replace ZP variables
output
```

Load heavy theme initialisation only in the build path.

For `gen2015.php` and `gen2026.php`, this means: load `functions.php` only when `zpcache.html` must be rebuilt. Do not load it when the cache is fresh.

Do not load `functions.php` before the cache check for convenience.

## Head and CSS

Generators no longer call `zcss()` directly. CSS is added centrally via `zgen_hvars('head', $head)`. `zcss()` remains an internal helper and includes:

```text
layout/zpsuper.css
layout/<family>/<design>/zp.css
layout/supercustom.css
layout/<family>/<design>/custom.css
```

Generators do not include `zp.css` again by themselves.

For cache templates, the theme's own CSS head should stand before `~~ZHEAD~~` when the foreign theme already brings its own CSS in the template. CSS that the generator passes in `$head` comes before the central ZP CSS. If a generator exceptionally has to add something after that in the head, it uses `headlast` or `~~ZHEADLAST~~`.

`~~ZHEAD~~` contains the ZP head block, not individual SEO variables. `zgen_hvars('head', $head)` also adds automatic language alternatives (`<link rel="alternate" hreflang="...">`) when the head does not already contain `hreflang=`.

Do not add separate cache variables for:

```text
description
keywords
canonical
robots
generator
favicon
css
hreflang
```

## PHP and MD mode

Generators do not read pages directly and do not search `pages/`.

PHP mode and MD mode use the same prepared data:

```php
$GLOBALS['zdata']['title']
$GLOBALS['zdata']['h1']
$GLOBALS['zdata']['body']
$GLOBALS['zdata']['head']
$GLOBALS['zconf']['navi']
$GLOBALS['zconf']['foot']
```

In MD mode, `zp/md.php` prepares the data. Do not call internal MD helper functions directly in the generator.

## Existing interfaces

Do not invent new configuration or page-data interfaces when an existing interface is enough.

General existing interfaces:

```text
$GLOBALS['zconf']   configuration
$GLOBALS['zdata']   data of the current page
zfooter()           central footer preparation
znavi()             central navigation
zcss()              central CSS inclusion
```

`$GLOBALS['zdata']` contains the prepared page data. Generators only output this data and do not read page sources directly. Usual values are `title`, `h1`, `body` and `head`.

`$GLOBALS['zconf']['bodylast']` is optional HTML directly before `</body>`. All generators must output this interface at the right position. For cache generators, `~~ZBODYLAST~~` stands in the cache template and the fast path replaces `bodylast` through `zhtmlcachrepl()`.

CSS customisation is the official update interface for visual changes. `zcss()` includes the existing CSS files. Generators must not invent an additional CSS interface and must not load `zp.css` twice.

### Request memory cache

`MCACH_*` is only request-local storage for repeatedly calculated pure helper values. It replaces neither `zpcache.html` nor files in the MD cache.

Memory cache is useful when a function calculates the same data several times in the same request, checks files or reads image information. It is not useful for simple one-liners or generator functions that run only once per request.

After changing dependent globals or configuration values, reset the matching `MCACH_*` entry. Do not store persistent data in `MCACH_*`.

## Navigation

Do not build navigation directly from `$GLOBALS['zconf']['navi']`. Always use `znavi()`.

`znavi()` returns:

```text
key
url
label
label2
sub
```

Handle nesting recursively. For expandable navigation, a generator function may check whether a sub item is active.

Language links are part of the navigation when `znavi()` returns them. Do not add a separate `langswitch` output.

### Small navigation / mobile menu

If a design does not work with the normal desktop navigation in the small view, the generator may additionally output `naviafter`. This is optional HTML directly after the navigation and is replaced through `~~ZNAVIAFTER~~`.

The text for the menu button always comes from `zmenu_text()` or from `ZP_HTML_CACHE_VARS['menutx']`. Do not hard-code `Menu`, `MENÜ` or a custom language list in the generator.

Basic rule for expandable small navigation:

```text
menu button       may open on mouseover
language selector may open on mouseover
submenus          open only on click
```

Reason: if normal submenus open on mouseover, the navigation is hard to use with a mouse because hover areas and target links can interfere with each other. For items with `sub`, the parent in the small navigation should therefore be a real toggle element. The parent page remains reachable in the submenu as the first link.

For imported designs, do not modify the theme files to add this structure. The generator creates the matching mobile menu structure; `zp.css` only handles the visual presentation.

## Footer

Always resolve the footer through `zfooter()`. Do not rebuild footer logic in generators.

`zfooter()` takes no parameters and always returns an array:

```php
[$footer, $footer_parts]=zfooter();
```

`$footer` is the fully joined standard footer. `$footer_parts` contains the individually resolved footer lines so that a generator can wrap them itself if necessary.

If the footer is empty, output no footer. If only one footer part exists or the second part is empty, output no separator. This decision belongs in `zfooter()`, not in generators.

Do not hard-code footer separators in generators:

```text
 - 
&ndash;
&nbsp; - &nbsp;
```

Generators use either `$footer[0]` for the finished standard footer or `$footer[1]` if the layout has to output the individual footer parts structurally separated.

## Imported layouts

Do not modify imported theme files in the design directory when they might later be replaced by a new version. ZP adaptations belong in:

```text
layout/_zpgen/<family>/zpgen.php
layout/<family>/_generator/zpgen.php
layout/<family>/<design>/zp.css
zp/zana.php
```

Do not write into foreign theme templates such as `layout/wondercms/sky/theme.php`. If a design needs different navigation on mobile, the generator creates the matching structure and `zp.css` handles the presentation.

## WordPress variants

`gen2015.php` and `gen2026.php` also use `zpcache.html`, but they are internal variants. They are stored under `layout/_zpgen/wordpress/` and loaded by `layout/_zpgen/wordpress/zpgen.php`.

Important rules:

```text
load functions.php only during cache build
do not duplicate navigation
do not copy WordPress branding into the footer when the ZP footer is used
use zhtmlcacheold(basename(__FILE__, '.php')); `gen2015`/`gen2026` are only internal variant file names for cache checking
do not check additional cache dependencies
```

In `gen2015.php`, navigation had already been correct for a long time. When adding cache, make sure that only the previously supported menu positions become `~~ZNAVI~~` and that other theme menus stay empty as before. Menu button texts must not be hard-coded as `Menu`/`MENÜ` in the cache, but must use `~~ZMENUTX~~`.

In `gen2026.php`, take block navigation only once as `~~ZNAVI~~`. Do not output additional `wp:navigation` blocks as well.

## Generators without cache

`zp/zpgen.php` remains without HTML cache because it is fast and the cache is practically already the HTML template. Known approximate scale:

```text
pico < 1 ms
water ca. 1.2 ms
```

Do not change this for uniformity.

Generators without `zhtmlcachrepl()` must send their HTML parts directly through `zgen_hvars()`:

```php
$htmllang=zgen_hvars('htmllang');
$head=zgen_hvars('head', $head);
$body=zgen_hvars('body', $body);
$foot=zgen_hvars('foot', $foot);
$bodylast=zgen_hvars('bodylast', $bodylast);
```

If a generator does not want an automatic addition, it excludes it through `$ignore`:

```php
$head=zgen_hvars('head', $head, ['title']);
$body=zgen_hvars('body', $body, ['pageimg']);
```

With `zhtmlcachrepl()`, `$ignore` is grouped by cache variable:

```php
echo zhtmlcachrepl($html, <family>_vars(), ['head'=>['title'], 'body'=>['pageimg']]);
```

Generators should no longer set standard values themselves in `$vars`. Values such as `htmllang`, `sitetitle`, `sitesub`, `title`, `homeurl`, `menutx`, `body`, `bodylast`, `foot`, `foot0` and `foot1` come from `zgen_hvars()` unless the generator explicitly overrides them because of its own markup.

Currently useful supported ignore values are:

```text
title
robots
description
canonical
hreflang
favicon
css
generator
pageimg
bodylast
htmllang
foot
foot0
foot1
```

`zgen_hvars('body', $body)` automatically adds `pageimg` to the body. If a generator needs its own position for `pageimg`, it must handle that position itself and must not also trigger the automatic body addition.

## Tests

Check at least syntax:

```bash
php -l zp/zana.php
php -l layout/_zpgen/<family>/zpgen.php
```

If a cache generator was changed, additionally check the affected cache users:

```bash
php -l layout/_zpgen/wondercms/zpgen.php
php -l layout/_zpgen/editorial/zpgen.php
php -l layout/_zpgen/paradigm-shift/zpgen.php
php -l layout/_zpgen/wordpress/gen2015.php
php -l layout/_zpgen/wordpress/gen2026.php
```

Check in the generated HTML:

```text
<!DOCTYPE html>
<html ...>
~~ZHEAD~~ and then ~~ZHEADLAST~~ before </head> in the cache template, central head additions through zgen_hvars('head', ...)
theme CSS in the cache template and in the passed head before ZP CSS
no visible cache placeholder left
navigation only once
H1 correct
body correct
footer correct
aria-current="page" correct
```

For MD-relevant changes, additionally check:

```text
mode=md
Markdown H1 visible
Markdown body visible
MD navigation works
canonical for index and subpages is correct
footer tokens replaced
```

## ZIP output

Do not package unchanged layout data: the `/layout` directory normally does not need to be included completely in the ZIP. If generators under `layout/_zpgen/` or local generators under `layout/<family>/_generator/` were changed, those files must of course be included.

Do not include:

```text
zpcache.html
```

Keep the final note short:

```text
changed files
download ZIP
versions
which tests were run
what was not checked
```

Do not make unsupported claims.
