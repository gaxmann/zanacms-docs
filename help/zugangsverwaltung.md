# ⚙ Device management

Device management controls which browsers or devices are allowed to access the ZANACMS admin area. Access is granted per device through an editing cookie, not through a shared login.

## When to use it

Use this page when you want to allow a new browser, phone, tablet or computer to access the admin area, or when access has been lost after deleting browser cookies.

## Initial setup

For the initial setup, temporary access to the device manager can be enabled by editing:

```text
/__config/EDITACCESS_ENABLE.php
```

Set the first value to the number of minutes the window should be open and the second value to the number of new device cookies that may be created. After all allowed cookies have been set, temporary access disables itself automatically. See the [Intro guide](../guide/index.md#editor-access-details) for the exact syntax.

## Typical use

You usually only need this page during the first setup, when adding a new device, or after browser cookies have been deleted. Day-to-day admin work does not require any interaction with this page.

## More details

- [Configuration](konfiguration.md)
- [Troubleshooting](fehlersuche.md)
- [Editor access details](../guide/index.md#editor-access-details)
