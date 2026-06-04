---
title: "ZANACMS update system"
permalink: /guide/update/
---
# ZANACMS update system

The ZANACMS update system is an admin tool for checking, inspecting and installing ZANACMS code updates from GitHub. It is conservative by design: the admin dashboard may show update information, but no update is installed automatically.

## Admin dashboard status

The admin dashboard shows the locally installed ZANACMS version from `zp/version.php`.

If the cached GitHub check says that the local version is current, the ZANACMS status line can show:

```text
ZANACMS    V2.0.5.18 · 2026-06-02 · aktuell [+]
```

If a newer safe target is known, the dashboard can show a separate GitHub line:

```text
GitHub     V2.0.5.19 · verfügbar [+]
```

The `[+]` link opens the GitHub changelog. It is always shown on the dashboard. If no separate GitHub line is shown, the link is placed in the ZANACMS line.

If no valid cached GitHub result is available, the dashboard can show `unbekannt` together with the cached error message.

The GitHub status and the maintenance pixel are only active when `adminexposure >= 2`. The actual update page is only available when the configured admin exposure level allows it.

## Background check

The GitHub status is refreshed by a small maintenance pixel in the admin layout:

```html
<img src="maint.php?admin=1" width="1" height="1">
```

The admin pixel uses a short browser cache. The server-side check is cached and does not run on every page view. The dashboard itself reads the local cache only; it does not wait for GitHub while rendering the admin index.

The local cache file is:

```text
admin/_updcache.php
```

A clean package ships this file empty:

```php
<?php
return [];
```

The GitHub check is enabled by default. It can be disabled in `__config/conf.php`:

```php
'autoupdcheck'=>false,
```

When the check runs, ZANACMS removes a local `img/updatepolicy.json` if it exists. The update policy is read from GitHub, not from a stale local file.

## Version source

The installed ZANACMS version is stored in:

```text
zp/version.php
```

The file contains only the version data:

```php
<?php
return ['2.0.5.18', '2026-06-02']; // Please update with any change in ZANACMS' code
```

The latest GitHub version is read from the same file in the GitHub repository.

## Update policy

Stable versions, warnings and breaking update boundaries are described in:

```text
img/updatepolicy.json
```

The file is read from GitHub by the maintenance check and by the update page.

Example:

```json
{
	"stables": [
		{"version": "2.0.5.12", "recommended": true, "note": "Update check reintroduced with warnings und endofline"},
		{"version": "2.0.1.100", "recommended": false, "note": "ok. was V2.1.100"}
	],
	"warnings": [
		{"affected": "2.0.4.63", "fixedin": "2.0.4.65", "severity": 3, "reason": "Recursion error"}
	],
	"endofline": [
		{"breakingversion": "2.0.4.68", "reason": "Config moved to /__config"}
	]
}
```

Only valid version numbers are used for update logic. Historical notes can be kept in `note` or `reason`, but version fields must remain machine-readable.

## Stable versions

`stables` contains versions that are known stable.

Only entries with `"recommended": true` are offered as stable update targets.

The update page tries to show at least one recommended stable version if one exists. This can also be an older version. This is intentional: when the latest version is not suitable, an administrator may want to go back to a known stable target.

If `latest` and the newest stable are the same version, the select shows one combined entry:

```text
latest (2.0.5.18 stable)
```

An additional older stable can still be shown if it is a useful separate target.

## Warnings

`warnings` describe installed version ranges for which an update is recommended.

A warning applies when:

```text
local >= affected
local < fixedin
```

Warnings are shown in the ZANACMS dashboard line before the installed version:

```text
ZANACMS    ⚠️ V2.0.5.10 · 2026-06-02
```

The tooltip text is translated by the admin UI, for example:

```text
Ein Update Ihres Systems wird empfohlen (Schwere 3/3)
```

The policy `reason` is stored with the warning but is not used as public dashboard text.

## Breaking update boundaries

`endofline` describes update boundaries that are not backwards-compatible.

A breaking warning applies when an update or downgrade crosses the boundary:

```text
local < breakingversion and target >= breakingversion
local >= breakingversion and target < breakingversion
```

Breaking updates are not shown as normal safe updates on the admin dashboard. If no update target without manual adjustments is available, the dashboard shows:

```text
Keine Updates ohne manuelle Anpassungen verfügbar
```

On the update page, breaking targets are marked in the select with: 💥. The relevant breaking warnings are listed above the dry run button.

When the target is exactly the breaking version:

```text
💥 stable (2.0.4.68) will break your system: Config moved to /__config
```

When the target crosses a breaking boundary introduced by another version:

```text
💥 stable (2.0.4.41) will break your system, because V2.0.4.68 introduced: Config moved to /__config
```

If several breaking boundaries are crossed, all relevant warnings are shown.

Breaking warning texts are English-only because they come from the public `updatepolicy.json`.

## Update page

The update page can offer targets such as:

```text
latest (2.0.5.18)
latest (2.0.5.18 stable)
stable (2.0.5.12)
Rollback (2.0.5.11) · 2026-06-02 20:59:22
```

Breaking targets are marked directly in the select:

```text
stable (2.0.5.12) 💥
```

The update page always performs a dry run before installing files. The dry run shows what would be copied, skipped or left unchanged. The actual copy step starts only after confirmation.

## Rollback

Before installing files, the updater can create rollback data for the previous state. Rollback is a one-step return path to the previous installed version.

A rollback target can appear as:

```text
Rollback (2.0.5.11) · 2026-06-02 20:59:22
```

Rollback files are temporary update data and are not part of normal site backups.

## Admin exposure

The update system separates visibility from write access.

```text
1 = no GitHub status and no updater
2 = show GitHub status
3 = allow update page and update actions
```

## Files

Relevant files:

```text
admin/index.php       dashboard status display
admin/maint.php       maintenance pixel
admin/_maintdo.php     GitHub check and cache writer
admin/_updcache.php    local update check cache
admin/update.php     update, dry run and rollback UI
zp/version.php        local version source
img/updatepolicy.json GitHub update policy
```

## No automatic updates

The update check does not install updates automatically.

The admin dashboard only displays status information. Updates are started explicitly from the update page.
