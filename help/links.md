---
title: "Links"
permalink: /help/links/
---
# Links

Internal links should use page IDs, not file paths or full URLs.

A page ID is the internal page name, for example:

```text
contact
```

## Rich editor

In Rich mode, select the text you want to link, then use the page/link selector in the editor toolbar and choose the target page, for example `contact`. The editor creates the correct internal link.

The HTML source view is available for special cases, but it is not the normal way to create links in Rich mode.

## MD editor and Markdown files

In MD mode, the editor toolbar can create links for you. Since the content field contains Markdown, you can also write links directly:

```md
[Contact](@contact)
```

## PHP pages

In PHP mode, use `zlink()` for URLs and `zhref()` for complete anchor tags. These functions require that the page language has already been selected with `zautolg()` or `zsetlangs()`:

```php
zhref('contact', 'Contact >')
```

If a link is needed before the page language has been selected, use `ztokenhref()` instead. The link is then kept as a token and resolved by ZANACMS later during output.

## Footer configuration

Footer configuration must use footer tokens:

```text
[[@contact]]
[@contact]
[footertext]
```

Direct PHP function calls such as `zlink()` or `zhref()` are not allowed in `/__config/conf.php`. See [Footer]({{ '/help/footer/' | relative_url }}) for details.

## External links

External `http://` and `https://` links can be used wherever the editor or a PHP helper supports them. In PHP pages, `zhref()` can create external links directly and opens them in a new tab by default.

## More details

- [Footer]({{ '/help/footer/' | relative_url }})
- [Pages]({{ '/help/page-overview/' | relative_url }})
- [PHP internal links](../guide/php-mode.md#6-internal-links)
- [MD internal links](../guide/markdown-mode.md#8-internal-links)
- [Rich internal links](../guide/rich-mode.md#8-internal-links)
- [Link interfaces](../guide/interfaces.md#5-internal-links-by-context)
