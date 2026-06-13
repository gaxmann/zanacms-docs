---
title: "Advanced topics"
permalink: /guide/advanced-topics/
parent: Guide
nav_order: 5
---
# Advanced topics

This page covers shared topics in more detail. For the basic shared concepts, see the [Introduction]({{ '/guide/intro/' | relative_url }}). For public function signatures and data arrays, see [Interfaces]({{ '/guide/interfaces/' | relative_url }}).

---

## Language handling in detail

The standard language is selected with `stdlang` in `/__config/conf.php`. An optional fallback language can be set with `fallbacklg`:

```php
'stdlang'=>'en',
'fallbacklg'=>'en',
```

Languages in `$GLOBALS['zlangs']` are official website languages and can appear in the language selector. MD and Rich pages can additionally have local page languages through their page files. These local languages are added to the language selector only on that page.

If an official website language is requested but the current page does not exist in that language, ZANACMS outputs the configured fallback language, or `stdlang` if no fallback is configured. Canonical points to the delivered content language, while further internal links stay in the requested official language.

If an unknown, non-official language is requested and the current page does not have that local language, canonical and further internal links use the delivered fallback language.

In PHP mode, `zautolg($tx)` registers the page languages from `array_keys($tx)` and stores the delivered content language. Build language-dependent HTML with `zlink()` or `zhref()` only after `zautolg()` has selected the language, or use ZTOKENS such as `ztokenhref()` in body strings.

---

## Layout CSS loading order

Most generators load CSS files in this order (files are only included when they exist):

```text
/layout/zpsuper.css                          shared ZP CSS
/layout/<family>/<design>/zp.css             supplied layout CSS
/layout/supercustom.css                      optional global custom CSS
/layout/<family>/<design>/custom.css         optional layout-specific custom CSS
```

`zpsuper.css` and `zp.css` are supplied by ZANACMS and can be overwritten by updates. `supercustom.css` and `custom.css` are for your own changes and are not overwritten.

### CSS variants

Some designs support CSS variants, selected with a suffix after the design name:

```php
'layout'=>'html/just-the-docs.dark',
'layout'=>'html/z-airy.blue',
```

A variant keeps the same `design.html` but loads a different CSS file:

```text
html/z-airy              -> zp.css
html/z-airy.blue         -> zpblue.css
html/just-the-docs.dark  -> zpdark.css
```

Variant CSS can be generated from `zp.css` using colour replacement rules in `design.ini`. Details are in [HTML designs]({{ '/guide/html-designs/' | relative_url }}).

---

## Layout families, generators and third-party themes

ZANACMS separates layout support into a layout family and a design:

```text
<family>/<design>
```

The design directory contains the layout files under `layout/<family>/<design>/`. ZANACMS first checks for a local family generator at `layout/<family>/_generator/zpgen.php`. If that file is missing, it uses the supplied ZP generator at `layout/_zpgen/<family>/zpgen.php`.

This makes it possible to support different kinds of layouts without changing page content. Some layout families are simple and mostly load a classless CSS file (for example `html/water`). Others adapt complete theme structures (for example WordPress themes or WonderCMS themes).

Directories whose names start with `_`, such as `_generator` or `_zpgen`, are internal and are not selectable designs.

A layout family may include generator variants. If a variant hint is needed:

```php
'layout'=>'wordpress.2015/graphy',
```

ZANACMS still uses the design directory `layout/wordpress/graphy` but passes the variant hint to the family generator.

For generator implementation details, see the [Layout generator manual]({{ '/guide/layout-generator-manual/' | relative_url }}).

### HTML cache

Complex layout families (`wordpress`, `wondercms`, `editorial`, `paradigm-shift`) use HTML caching. The cache file `zpcache.html` is renewed when the generator or `zp.css` is newer than the cached file, or when `zpcache.html` is missing.

If cached HTML output must be rebuilt manually, use `/admin/d.php` and choose "Clear all HTML cache".

Cached templates use central placeholders such as `~~ZBASEURL~~`, `~~ZDIRDESIGN~~` and `~~ZDIRSCRIPTS~~`. The full placeholder list is in the [Layout generator manual]({{ '/guide/layout-generator-manual/' | relative_url }}#html-cache-standard).

### WordPress themes

The `wordpress` family generator requires jQuery compatibility files in `/layout/_scripts/`. These are bundled for classic WordPress themes. See [`THIRD-PARTY-NOTICES.md`](https://github.com/gaxmann/zanacms/blob/master/layout/THIRD-PARTY-NOTICES.md) for licence information.

Many older WordPress themes work well because their HTML structure is relatively simple. Modern block themes or themes with heavy JavaScript or build pipelines may need extra adjustments.

Tested design snapshots are available at `https://github.com/gaxmann/zp-cms-designs`.

---

## Editor access details

The device manager is `/admin/d.php`. It is locked by `/__config/EDITACCESS_ENABLE.php`.

The default values are `return [0,0,0];` (locked). To open a temporary access window, edit the file:

```php
return [5,1,0];
```

The three values are:

| Position | Meaning |
| --- | --- |
| First | Access window time in minutes (capped at five) |
| Second | Number of new device cookies that may be created |
| Third | `adminexposure` level for new cookies (0 = use the global config level) |

On first access, the file adds a UTC/Unix expiry timestamp as a fourth value. After all allowed cookies are set, after deleting all access entries, or after expiry, the file resets to `return [0,0,0];`.

On the device manager page, choose "Activate this device now" to create an editing cookie for the current browser. The "Clear all HTML cache" action recursively deletes generated `zpcache.html` files.

The cookie data is stored in `/admin/_zdata.php`. The editor and media tools are not shown if the cookie data is missing or has expired.

---

## Admin exposure levels

`adminexposure` controls how much of the admin surface is shown. It is set in `/__config/conf.php`:

```php
'adminexposure'=>3,
```

| Level | Surface |
| --- | --- |
| 1 | Redakteur — cannot delete pages, languages or images; no Settings, no GitHub status, no updater |
| 2 | Foolproof — broader tools, but no Settings |
| 3 | Default — Settings visible, cached GitHub status visible |
| 4 | Technically proficient — update page and update actions available |
| 5 | Reserved for a fuller admin surface |
| 8 | Internal service/dev level (set through `/__config/isdev.txt` or service access) |

The admin maintenance pixel can run on signed-in admin pages at any level so that background tasks such as sitemap rebuilding continue.

---

## Admin interface language

The admin interface ships with built-in language files in `/admin/lang/`. If no admin `lg` parameter is set, the admin interface uses the configured `stdlang`.

If the selected language is not one of the built-in admin languages, ZANACMS checks whether `/admin/lang/<language>.php` exists. If it does, that language is loaded. If not, the fallback order is: requested language → `fallbacklg` → `stdlang` → English.

To add a new admin translation, copy an existing file to `/admin/lang/<language>.php` and translate the strings.

---

## URL rewrite

ZANACMS works without URL rewrite by default. Rewrite is optional and can be enabled in `/__config/conf.php`:

```php
'urlrewrite'=>['/', true],
```

For a site installed in `/cms/`:

```php
'urlrewrite'=>['/cms/', true],
```

The matching `.htaccess` example must also be copied to `.htaccess`.

With rewrite enabled, `zlink('contact')` produces URLs such as `/en/contact`. Without rewrite, it produces query URLs. Internal links should always use `zlink()`, `zhref()`, `@page` or `[@page]` so they work in both modes. The `lay` design selector remains a query parameter.

To prepare root-based URLs without rewrite, set only the base path:

```php
'urlrewrite'=>['/'],
```

---

## HTML status comment

ZANACMS can write a performance comment at the end of generated pages:

```html
<!-- RAM: 2.00 MiB (685 KiB) | DB: 0 | Render: 1.26 ms -->
```

To hide it, set in `/__config/conf.php`:

```php
'showstat'=>false,
```

---

## Sitemap

ZANACMS can write a static `/sitemap.xml`. The feature is enabled by default. To disable it:

```php
'sitemap'=>false,
```

If `sitemap` is not set in `/__config/conf.php`, the admin Settings page can enable it as a fallback value.

Sitemap rebuilding depends on the admin area. The admin maintenance pixel in `/admin/maint.php` keeps the sitemap current. If `/admin/` is removed or never opened, automatic sitemap rebuilding does not run.

---

## Service access

Optional service access for agencies or support is available through `/__config/service.php`. It creates a normal editing cookie with admin exposure level 8. See [Interfaces]({{ '/guide/interfaces/' | relative_url }}#service-access) for the setup procedure.

---

## Historical note

ZP originally stood for ZANAPRESS, from when the project started as a WordPress theme adapter in 2014.
