---
title: "Configuration"
permalink: /help/configuration/
parent: Help
nav_order: 9
---
# Configuration

ZANACMS configuration files are stored in:

```text
/__config/
```

## Main configuration file

The active website configuration is:

```text
/__config/conf.php
```

To create it, copy one of the example files from `/__config/` and adjust it for the chosen mode:

```text
conf.example-php.php
conf.example-md.php
conf.example-rich.php
```

## Common settings

Typical settings in `conf.php` include the website mode, languages, layout, sitemap switch, URL mode, footer lines, optional manual navigation/sidebar text and admin exposure level. Values set in `conf.php` have priority over admin-written fallback values from `/__config/_admconf.php`.

`adminexposure` controls how much of the admin surface is shown. Level 1 is the Editor level and cannot delete pages, languages or images, cannot use Settings nor backup the site. Level 2 is Foolproof and still cannot use Settings. Level 3 is the Standard level and shows Settings plus the GitHub status. Level 4 is technically proficient and can also use the update page and update actions. Level 5 is "Give all options" and currently unused in the normal public admin surface. Internal service/dev access uses level 8.


## Settings, design and sidebar

The admin page **Settings** can write fallback values to `/__config/_admconf.php`.

The design select is shown when `layout` is not set in `/__config/conf.php`. If `$GLOBALS['zconf']['layout']` is set there, the design is manually controlled by `conf.php` and the select is not shown.

Layouts can output a sidebar with `~~ZCOL2~~`. The sidebar is read through `zgetconf('col2')`, so page data can override the global `$GLOBALS['zconf']['col2']`. If `$GLOBALS['zconf']['col2']` is set in `/__config/conf.php`, that configuration text is used as the global runtime value.

The sidebar field in **Settings** is shown only when `col2` is not set directly in `/__config/conf.php` and the active design opts in with `design.ini` / `columns=2`. If the design is fixed in `conf.php` but `col2` is not fixed there and the active design supports `columns=2`, the settings page shows only the sidebar field. MD mode stores Markdown in `_admconf.php` and caches rendered HTML in `/zpcache/_meta.php`; Rich and PHP mode store HTML there, in separate mode branches. Without `design.ini`, the design is treated as `columns=1`.

## sitemap.xml

The sitemap switch is controlled by:

```php
'sitemap'=>false,
```

If the key is missing, the preset is `true`. The value `false` disables `/sitemap.xml`. When `sitemap` is not set in `/__config/conf.php`, the admin Settings page can store the fallback value in `/__config/_admconf.php`. If it is set in `conf.php`, the admin checkbox is not shown.

Current sitemap rebuilding depends on the admin maintenance pixel. This means `/admin/` must be present and admin pages must be used. If the admin area is removed or never opened, `/sitemap.xml` is not automatically rebuilt.

## Open Graph

Open Graph output is enabled by default. The central head block writes `og:title`, `og:description`, `og:image`, `og:url` and `twitter:card` from the current page and site data. `og:description` is built from a normalised excerpt of about 128 characters from the current page body after HTML has been removed. If the page body is empty, the site subtitle is used as fallback. `pageimg` is used as the image when the current page has one.

To disable Open Graph output in `/__config/conf.php`, set:

```php
'opengraph'=>false,
```

To keep it enabled and define a fallback image for pages without `pageimg`, set:

```php
'opengraph'=>['img'=>'img/defaultimg.jpg'],
```

The image value is a URL path, not a file-system path.

## Maintenance mode

Maintenance mode is controlled by:

```php
'maintenance'=>true,
```

If the key is missing, the preset is `false`. When maintenance mode is enabled, normal visitors see a simple maintenance page instead of the website. Admin devices can still view the website and get a red maintenance notice at the top of the page.

When `maintenance` is not set in `/__config/conf.php`, the admin Settings page can store the fallback value in `/__config/_admconf.php`. If it is set in `conf.php`, the admin checkbox is not shown.

## Optional local files

Temporary device setup access is controlled by:

```text
/__config/EDITACCESS_ENABLE.php
```

This file opens a short time window during which editing cookies can be created or deleted. See [Device management]({{ '/help/access-management/' | relative_url }}) for details.

Optional service access is controlled by:

```text
/__config/service.php
```

This file contains only the bcrypt hash of the service key. The service key must have at least 64 characters. It is used only through `/admin/d.php?isservice=1`. No example file is shipped. See [Service access in the Interfaces guide]({{ '/guide/interfaces/' | relative_url }}#service-access).

## Core and admin files

Do not place local configuration into `/zp/` or `/admin/`. `/zp/` contains runtime and core files; `/admin/` contains administration code. Both directories can be overwritten by updates.

## More details

- [Modes]({{ '/help/modes/' | relative_url }})
- [Footer]({{ '/help/footer/' | relative_url }})
- [Settings]({{ '/help/settings/' | relative_url }})
- [Installation]({{ '/guide/installation/' | relative_url }})
- [Configuration interface]({{ '/guide/interfaces/' | relative_url }}#3-configuration-in-__configconfphp)
