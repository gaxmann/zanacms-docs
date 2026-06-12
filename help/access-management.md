---
title: "⚙ Device management"
permalink: /help/access-management/
parent: Help
nav_order: 2
---
# ⚙ Device management

<p><img class="help-screenshot" src="{{ '/assets/help/a0.jpg' | relative_url }}" alt="Device management screenshot"></p>

Device management controls which browsers or devices are allowed to access the ZANACMS admin area. Access is granted per device through an editing cookie, not through a shared login.

## When to use it

Use this page when you want to allow a new browser, phone, tablet or computer to access the admin area, or when access has been lost after deleting browser cookies.

## Initial setup

For the initial setup, temporary access to the device manager can be enabled by editing:

```text
/__config/EDITACCESS_ENABLE.php
```

Set the first value to the number of minutes the window should be open, the second value to the number of new device cookies that may be created, and the third value to the optional `adminexposure` written into those new cookies. Typical examples are `return [5,1,0];` and `return [5,1,1];`. After all allowed cookies have been set, temporary access disables itself automatically. See the [Intro guide]({{ '/guide/intro/' | relative_url }}#editor-access-details) for the exact syntax.

## Typical use

You usually only need this page during the first setup, when adding a new device, or after browser cookies have been deleted. Day-to-day admin work does not require any interaction with this page.

When `adminexposure < 3`, the page stays reachable but shows a reduced view: the activated-device table is hidden, and the HTML-cache and delete-all actions are not available. The current device can still be activated when the temporary access window allows it.


## Admin exposure levels

`adminexposure` controls how much of the admin surface is shown:

- `1` = Editor — cannot delete pages, languages or images and cannot use Backup or Settings
- `2` = Foolproof — cannot use Settings
- `3` = Standard — default level
- `4` = Technically proficient — can use `update.php` and update actions
- `5` = All options — currently unused in the normal public admin surface

Internal service access uses level `8`.

## Service access

Service access is an optional agency/support access. The normal device manager stays locked as usual unless its normal setup window is open. A configured service access is opened explicitly with:

```text
/admin/d.php?isservice=1
```

A successful service login creates a normal editing cookie. It does not put the service password into the browser. For setup format, cookie behaviour and the temporary support level selector, see [Service access in the Interfaces guide]({{ '/guide/interfaces/' | relative_url }}#service-access).

## More details

- [Configuration]({{ '/help/configuration/' | relative_url }})
- [Troubleshooting]({{ '/help/troubleshooting/' | relative_url }})
- [Editor access details]({{ '/guide/intro/' | relative_url }}#editor-access-details)
