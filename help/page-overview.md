---
title: "@ Pages"
permalink: /help/page-overview/
parent: Help
nav_order: 5
---
# @ Pages

<p><img class="help-screenshot" src="{{ '/assets/help/a4.jpg' | relative_url }}" alt="Pages overview screenshot"></p>

The Pages tool gives an overview of all website pages and opens the editor workflow for the current mode.

## PHP mode

PHP mode does not use the admin page editor. PHP pages are `.php` files and are written and maintained by a developer directly.

## MD and Rich mode

MD mode stores pages as Markdown files in `/pages/`. Rich mode stores them as PHP data files in the same directory. 

In the Pages tool you can delete and edit any language of your pages, and you can also add new languages to your pages. Click on the page ID to visit the page.

## Good practice

Keep page IDs short and stable. Changing a page ID also changes the targets of any internal links and navigation entries that point to it. If you only want to change how a page is titled in the menu, change the navigation label instead.

## More details

- [Modes]({{ '/help/modes/' | relative_url }})
- [Links]({{ '/help/links/' | relative_url }})
- [MD editor and media gallery]({{ '/guide/markdown-mode/' | relative_url }}#13-md-editor-and-media-gallery)
- [Rich editor]({{ '/guide/rich-mode/' | relative_url }}#10-rich-editor)
