---
title: "Modes"
permalink: /help/modes/
parent: Help
nav_order: 8
---
# Modes

ZANACMS has three content modes. A website uses one mode, selected in `/__config/conf.php`.

## PHP mode

Use PHP mode when pages are written as PHP files. Each page prepares its own data and calls the output function directly. This is the most flexible mode and is aimed at developers who want full control over page structure and content.

PHP mode does not use the visual editor workflow for normal pages. The navigation/sidebar tools can still write fallback data to `/__config/_admconf.php` when the matching key is not defined in `/__config/conf.php`.

## MD mode

Use MD mode when pages should be stored as lightweight Markdown files. The MD editor in the admin area uses the same toolbar style as the Rich editor, but the content field contains Markdown text rather than a visual rendering.

The toolbar can insert common Markdown syntax such as bold, italic, lists, links and images. You can also edit the Markdown source directly at any time. Markdown files can alternatively be managed via FTP without using the admin editor at all.

## Rich mode

Use Rich mode when page content should be edited visually in the admin area. The Rich editor is the standard workflow for non-technical editors who maintain page content regularly. This is usually the most comfortable mode for day-to-day editing.

## Shared parts

All three modes share the same runtime, layout system, configuration directory, footer handling, navigation/sidebar fallback handling and URL generation. Switching mode requires only a change in `conf.php` and the appropriate page files.

## More details

- [Pages]({{ '/help/page-overview/' | relative_url }})
- [Configuration]({{ '/help/configuration/' | relative_url }})
- [Introduction]({{ '/guide/intro/' | relative_url }})
- [PHP mode]({{ '/guide/php-mode/' | relative_url }})
- [MD mode]({{ '/guide/markdown-mode/' | relative_url }})
- [Rich mode]({{ '/guide/rich-mode/' | relative_url }})
- [Settings]({{ '/help/settings/' | relative_url }})
