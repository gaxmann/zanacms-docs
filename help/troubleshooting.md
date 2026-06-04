---
title: "Troubleshooting"
permalink: /help/troubleshooting/
---
# Troubleshooting

## The admin area is not accessible

Check whether the device is activated for admin access. Access is controlled per device through an editing cookie. For the initial setup, temporary access is enabled via `/__config/EDITACCESS_ENABLE.php`. After the allowed number of device cookies has been created, temporary access disables itself automatically. See [Device management]({{ '/help/access-management/' | relative_url }}) for details.

## A page is missing in one language

Check whether a page file for that language actually exists. In MD mode, each language version is a separate Markdown file. In Rich mode, each language version is a separate entry in the page data file. ZANACMS can fall back to another available language depending on configuration and mode.

## A link points to the wrong page

Check whether the link uses the correct page ID. Do not mix filesystem paths, URL paths and page IDs — they are not interchangeable. See [Links]({{ '/help/links/' | relative_url }}) for the correct link syntax for each mode.

## The navigation entry is wrong

Open the navigation editor and check both the page ID and the visible menu label. If only the displayed text is wrong, change the label — not the page ID. Changing the page ID would also break any existing internal links that point to it.

## A design change disappeared after an update

Do not edit core layout files such as `zp.css` or `zpsuper.css` directly. Use `supercustom.css`, `custom.css` or a separate design folder for your own changes. See [Design and CSS]({{ '/help/design-and-css/' | relative_url }}) for the safe customisation paths.

## An update is needed

Create a backup before starting. Then open the Backup and update tool and, if your admin exposure level allows it, run the update dry run first. The Update tool shows what files would change before anything is installed, and offers the latest GitHub version, the stable policy version and Rollback when available.

## More details

- [Device management]({{ '/help/access-management/' | relative_url }})
- [Navigation]({{ '/help/navigation-editor/' | relative_url }})
- [Links]({{ '/help/links/' | relative_url }})
- [Design and CSS]({{ '/help/design-and-css/' | relative_url }})
- [Backup and update]({{ '/help/backup/' | relative_url }})
