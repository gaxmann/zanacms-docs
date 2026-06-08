---
title: "Page variables"
permalink: /help/page-variables/
---
# Page variables

Page variables are prepared values that can be inserted into MD or Rich page content without writing PHP.

They are intended for cases where a PHP developer prepares specific values or output — such as prices, opening hours or data from a database table — that an editor should be able to use in normal page content.

## What the editor uses

Insert a variable in a page with:

```text
[VAR:name]
```

Examples:

```text
[VAR:hourly_rate_1]
[VAR:price_table]
```

When page variables are configured, the Rich editor and MD editor toolbar can show the available variable names for easy insertion, so you do not have to type the token by hand.

## Where they come from

Page variables are defined by a developer in:

```text
/pages/zvars.php
```

A variable can return a simple text value or complex generated HTML. Editors only use the prepared variable names; they do not need to know the PHP behind them. For translated helper strings inside `/pages/zvars.php`, developers can use `zvarlg($tx)` to read the matching array value without changing the page language.

## More details

- [Pages]({{ '/help/page-overview/' | relative_url }})
- [Page variables in interfaces]({{ '/guide/interfaces/' | relative_url }}#8-page-variables)
- [Intro page variables]({{ '/guide/' | relative_url }}#page-variables)
