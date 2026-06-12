---
title: "Images and media"
permalink: /help/images-and-media/
parent: Help
nav_order: 12
---
# Images and media

Uploaded images are stored in `/img/` and can be used in any page.

## Upload first

Before inserting an image into a page, upload it through the [Media]({{ '/help/media/' | relative_url }}) tool. The tool prepares the stored image files for use in pages and, where needed, creates resized versions.

## Rich mode

In Rich mode, use the image picker in the editor. Select an uploaded image from the media grid and insert it directly into the current page. The editor handles the image markup for you.

## MD mode

In MD mode, use the image picker via the MD editor toolbar. The editor inserts the correct Markdown image code into the text field at the current cursor position.

You can also write Markdown image syntax directly if you prefer to edit the source.

## PHP mode

In PHP mode, you can use standard HTML image tags or the `zoutimg()` helper function.

If admin access is set up, the media gallery simplifies this: click an image in the media grid and ZANACMS copies the matching `zoutimg()` snippet to the clipboard, ready to paste into a PHP page.

## Page image and layout image

Some layouts support a **page image** or a **layout image**. A page image is part of the page content and belongs to the individual page. A layout image is part of the selected design and affects the overall layout output, for example a large header or background image.

## More details

- [Media]({{ '/help/media/' | relative_url }})
- [Pages]({{ '/help/page-overview/' | relative_url }})
- [PHP media gallery]({{ '/guide/php-mode/' | relative_url }}#8-media-gallery)
- [MD editor and media gallery]({{ '/guide/markdown-mode/' | relative_url }}#13-md-editor-and-media-gallery)
- [Rich editor]({{ '/guide/rich-mode/' | relative_url }}#10-rich-editor)
- [zoutimg()]({{ '/guide/interfaces/' | relative_url }}#zoutimgfile-md)
