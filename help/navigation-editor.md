---
title: "➔ Navigation"
permalink: /help/navigation-editor/
---
# ➔ Navigation

The navigation editor changes the website menu in MD mode and Rich mode.

## What you edit

You edit the menu structure and the visible menu labels. The targets are internal page IDs, for example:

```text
index
contact
legalnotice
```

Always use page IDs for navigation entries, not file paths or full URLs. Page IDs stay stable and work correctly with both query-style and rewrite-style URLs.

## MD mode

In MD mode, use the admin navigation editor to edit the menu from the ZANACMS admin area. The editor updates the navigation file used by the MD runtime.

If you prefer to work with files directly, you can also edit the navigation text file via FTP or a hosting file manager.

## Rich mode

In Rich mode, use the navigation editor to edit the menu. It writes the navigation data used by the Rich runtime.

## Good practice

Keep page IDs short and stable. If the displayed menu text needs to change, edit the visible label — not the page ID. Changing a page ID breaks existing internal links and navigation entries that point to it.

## More details

- [Pages]({{ '/help/page-overview/' | relative_url }})
- [Links]({{ '/help/links/' | relative_url }})
- [MD navigation file]({{ '/guide/markdown-mode/' | relative_url }}#7-navigation-file)
- [Rich navigation editor]({{ '/guide/rich-mode/' | relative_url }}#7-navigation-editor)
