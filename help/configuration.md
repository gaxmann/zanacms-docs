---
title: "Configuration"
permalink: /help/configuration/
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

Typical settings in `conf.php` include the website mode, languages, layout, URL mode, footer lines, optional config-based sidebar text and admin exposure level.


## Sidebar

Layouts can output a sidebar with `~~ZCOL2~~`. If `$GLOBALS['zconf']['col2']` is set in `/__config/conf.php`, that configuration text is used and the sidebar editor is locked.

If `col2` is not set in `/__config/conf.php`, and the active design opts in with `design.ini` / `columns=2`, the admin tool **Sidebar editor** can write file-based sidebar content: `/pages/__col2.<lg>.md` in MD mode and `/pages/__col2.data.php` in Rich/PHP mode. Without `design.ini`, the design is treated as `columns=1` and the admin menu does not show the sidebar editor.

## Optional local files

Temporary device setup access is controlled by:

```text
/__config/EDITACCESS_ENABLE.php
```

This file opens a short time window during which editing cookies can be created or deleted. See [Device management]({{ '/help/access-management/' | relative_url }}) for details.

## Core and admin files

Do not place local configuration into `/zp/` or `/admin/`. `/zp/` contains runtime and core files; `/admin/` contains administration code. Both directories can be overwritten by updates.

## More details

- [Modes]({{ '/help/modes/' | relative_url }})
- [Footer]({{ '/help/footer/' | relative_url }})
- [Sidebar editor]({{ '/help/sidebar-editor/' | relative_url }})
- [Intro guide]({{ '/guide/' | relative_url }}#example-files)
- [Configuration interface]({{ '/guide/interfaces/' | relative_url }}#3-configuration-in-confconfphp)
