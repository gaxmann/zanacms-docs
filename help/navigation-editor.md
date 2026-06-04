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

Always use page IDs for navigation entries, not file paths or full URLs. You can use the button to move the navigation (trees) up and down or move use the left and right arrow buttons the change the level of the navigation entry. Please put the cursor in the line you want to manipulate.

## Good practice

Keep page IDs short and stable. If the displayed menu text needs to change, edit the visible label — not the page ID. Changing a page ID breaks existing internal links and navigation entries that point to it.

## More details

- [Pages]({{ '/help/page-overview/' | relative_url }})
- [Links]({{ '/help/links/' | relative_url }})
- [MD navigation file]({{ '/guide/markdown-mode/' | relative_url }}#7-navigation-file)
- [Rich navigation editor]({{ '/guide/rich-mode/' | relative_url }}#7-navigation-editor)
