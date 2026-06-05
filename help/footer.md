---
title: "Footer"
permalink: /help/footer/
---
# Footer

Footer lines are configured in `/__config/conf.php`. Footer handling is shared by all three modes: PHP mode, MD mode and Rich mode.

## Footer tokens and text variables

Footer configuration must use footer tokens and simple text variables. Do not use PHP function calls such as `zlink()` or `zhref()` directly inside footer configuration.

Use a complete internal page link (renders as a full `<a>` tag):

```text
[[@contact]]
```

Use only the internal URL, for example inside your own HTML markup:

```text
[@contact]
```

Use translated footer text defined in the language configuration:

```text
[footertext]
```

Footer tokens are ZTOKENS. Simple text variables such as `[footertext]` are read from `$GLOBALS['zlangs'][language]['vars']`. Variables are replaced before link tokens, so a variable value may contain `[@page]` or `[[@page]]`.

## Why footer works this way

`/__config/conf.php` is configuration data. It is read in situations where the full page runtime is not necessarily loaded yet. Using footer tokens keeps footer configuration independent of runtime PHP function calls, which may not be available at that point.

## More details

- [Links]({{ '/help/links/' | relative_url }})
- [Configuration]({{ '/help/configuration/' | relative_url }})
- [Footer syntax]({{ '/guide/interfaces/' | relative_url }}#4-footer-syntax)
