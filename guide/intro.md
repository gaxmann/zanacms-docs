---
title: "Introduction"
permalink: /guide/intro/
parent: Guide
nav_order: 2
---
# ZANACMS Introduction

This page explains the shared concepts that apply to all three ZANACMS modes. Read it after the [Installation]({{ '/guide/installation/' | relative_url }}) and before starting with the mode-specific guide for your chosen mode.

## Where to find what

| Question | Document |
| --- | --- |
| I want to install ZANACMS | [Installation]({{ '/guide/installation/' | relative_url }}) |
| I want to understand the shared concepts | this page |
| I use PHP mode | [PHP mode]({{ '/guide/php-mode/' | relative_url }}) |
| I use MD mode | [MD mode]({{ '/guide/markdown-mode/' | relative_url }}) |
| I use Rich mode | [Rich mode]({{ '/guide/rich-mode/' | relative_url }}) |
| I need the full function and interface reference | [Interfaces]({{ '/guide/interfaces/' | relative_url }}) |
| I build or change layout generators | [Layout generator manual]({{ '/guide/layout-generator-manual/' | relative_url }}) |
| I have licence questions | [License FAQ]({{ '/guide/license-faq/' | relative_url }}) |

---

## Basic terms

A **page ID** is the internal name of a page, for example `index` or `contact`. Internal links use page IDs, not file paths or full URLs.

A **language code** is the short code used by ZANACMS for languages, normally two lowercase letters such as `en` or `de`.

A **layout** is the visual output of the website, written as `<family>/<design>`, for example `html/water`. The **layout family** is the first part (`html`), the **design** is the second part (`water`).

---

## Layouts and designs

The available layouts are stored in `/layout/`. The active layout is set in `/__config/conf.php`:

```php
'layout'=>'html/water',
```

Bundled layouts include:

```text
html/water              water.css based layout
html/just-the-docs      Just the Docs based layout (also available as html/just-the-docs.dark)
html/z-airy             ZANACMS HTML layout (also available as html/z-airy.blue)
wondercms/sky           WonderCMS Sky layout
```

Additional tested designs can be downloaded from `https://github.com/gaxmann/zp-cms-designs` and copied into `/layout/`.

A layout name without a slash, such as `foo`, is resolved as `foo/foo`.

Some layouts support optional design values. For example, `paradigm-shift/paradigm-shift` uses `layoutimg` for its intro image:

```php
'layoutimg'=>['src'=>'img/example.jpg', 'pos'=>'center'],
```

For more about layout families, generators, CSS variants and HTML designs, see [Advanced topics]({{ '/guide/advanced-topics/' | relative_url }}).

---

## Images

Website images are stored in:

```text
/img
```

Large image versions are stored in `/img/big` when available. The media gallery also reads this directory. Images can be uploaded through the admin media tool or placed there directly.

---

## Languages

The standard language is set in `/__config/conf.php`:

```php
'stdlang'=>'en',
```

The languages available on the website are defined in `$GLOBALS['zlangs']`:

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

Each language entry contains the site title and subtitle for that language. Navigation labels, footer text variables and other translated texts also go here. The mode-specific guides show the complete format.

For details on fallback languages, language sets per page and canonical behaviour, see [Advanced topics]({{ '/guide/advanced-topics/' | relative_url }}#language-handling-in-detail).

---

## Navigation

The website navigation is defined in `/__config/conf.php` with page IDs as targets:

```php
'navi'=>[
    'index'=>['index'],
    'contact'=>['contact'],
],
```

The visible navigation labels are stored per language in `$GLOBALS['zlangs']`:

```php
'en'=>[
    'navi'=>[
        'index'=>'Home',
        'contact'=>'Contact',
    ],
],
```

Subnavigation is supported as a second array value:

```php
'navi'=>[
    'about'=>['about', [
        'team'=>['team'],
        'history'=>['history'],
    ]],
    'contact'=>['contact'],
],
```

If `navi` is not set in `/__config/conf.php`, the admin navigation editor becomes available and stores the navigation in `/__config/_admconf.php`.

---

## Footer

Footer lines are configured in `/__config/conf.php`:

```php
'foot'=>[
    "<span style='font-size:0.65em'>[footertext]</span>",
],
```

Footer configuration uses tokens, not PHP function calls:

`[[@contact]]` creates a complete internal link (full `<a>` tag).

`[@contact]` creates only the URL, for use inside custom HTML such as `<a href="[@contact]">Text</a>`.

`[footertext]` inserts translated text from `$GLOBALS['zlangs'][language]['vars']`:

```php
'vars'=>[
    'footertext'=>'Powered by ZANACMS',
],
```

If extra query parameters are needed, use `[@contact?]value=12`.

Do not use `zlink()` or `zhref()` directly inside footer configuration.

---

## Internal links

The link syntax depends on the context:

| Context | Syntax | Example |
| --- | --- | --- |
| PHP page code | `zlink()` or `zhref()` | `zhref('contact', 'Contact')` |
| MD body text | `[Text](@page)` | `[Contact](@contact)` |
| Rich editor body | `[@page]` in href | `<a href="[@contact]">Contact</a>` |
| Footer (all modes) | `[[@page]]` or `[@page]` | `[[@contact]]` |

Always use page IDs for internal links, not file paths. The mode-specific guides explain each syntax in detail. For the complete function reference, see [Interfaces]({{ '/guide/interfaces/' | relative_url }}).

---

## Admin area

The admin area is at `/admin/`. It provides these tools:

| Tool | Purpose |
| --- | --- |
| Dashboard | Site status, version, mode overview |
| Device management | Activate browsers/devices for admin access |
| Settings | Choose the design, edit the global sidebar |
| Pages | Page overview, open the editor |
| Navigation | Edit the website menu |
| Media | Upload images, copy image code |
| Backup | Create backups, run updates |

Not all tools are visible at every admin exposure level. The `adminexposure` setting in `/__config/conf.php` controls how much of the admin surface is shown:

| Level | Description |
| --- | --- |
| 1 | Editor — cannot delete pages, languages or images; no Settings, no Backup |
| 2 | Foolproof — broader tools, Backup, but no Settings |
| 3 | Standard — Settings and GitHub status visible |
| 4 | Technically proficient — update page and update actions available |
| 5 | All Options – Reserved for future use |

Example:

```php
'adminexposure'=>4,
```

If `adminexposure` is not set, the default level is 3.

---

## Editor access

Admin access is granted per browser or device through an editing cookie. There is no shared login with username and password.

Access is managed through the device manager at `/admin/d.php`. This page is locked by default and can only be opened through a temporary access window in `/__config/EDITACCESS_ENABLE.php`. After the allowed cookies are created, the temporary access window is closed automatically.

When access is active, Rich and MD mode show a small `[e]` link in the page footer to open the editor. PHP mode shows a `[i]` link to the media gallery.

For the full mechanics of `EDITACCESS_ENABLE.php`, see [Advanced topics]({{ '/guide/advanced-topics/' | relative_url }}#editor-access-details).

---

## CSS customisation

For update-safe CSS changes, use these files:

```text
/layout/supercustom.css                     global CSS for all layouts
/layout/<family>/<design>/custom.css        CSS for the selected layout only
```

Both files are loaded after the supplied layout CSS and are not overwritten by updates.

Do not change supplied core CSS files such as `zp.css` or `zpsuper.css`. They can be overwritten by ZANACMS updates.

---

## Page variables

MD mode and Rich mode can use prepared variables from `/pages/zvars.php`. A PHP developer defines the variables, and an editor inserts them into page content with:

```text
[VAR:name]
```

This is useful for values such as prices, opening hours, or generated output like database tables. The editor toolbar shows available variables for easy insertion. See the mode-specific guides and [Interfaces]({{ '/guide/interfaces/' | relative_url }}#8-page-variables) for details.

---

## Sidebar

Some layouts support a sidebar through `{% raw %}{{ZCOL2}}{% endraw %}`. The sidebar content can come from three places, checked in this order:

1. Page data (`$GLOBALS['zdata']['col2']`) — per-page override
2. Manual config (`$GLOBALS['zconf']['col2']` in `/__config/conf.php`) — global value
3. Admin settings (`/__config/_admconf.php`) — editable through the Settings page

The Settings page sidebar field is shown only when `col2` is not set in `/__config/conf.php` and the active design defines `columns=2` in `design.ini`.

---

## Next steps

Continue with the guide for your chosen mode:

- [PHP mode]({{ '/guide/php-mode/' | relative_url }}) — developer-controlled PHP pages
- [MD mode]({{ '/guide/markdown-mode/' | relative_url }}) — Markdown files and the Markdown editor
- [Rich mode]({{ '/guide/rich-mode/' | relative_url }}) — page content through the rich text editor

For the full function and interface reference, see [Interfaces]({{ '/guide/interfaces/' | relative_url }}).

For layout CSS details, language fallback handling, URL rewrite and other advanced configuration, see [Advanced topics]({{ '/guide/advanced-topics/' | relative_url }}).
