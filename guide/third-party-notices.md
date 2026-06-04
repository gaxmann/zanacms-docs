# Third-party components and external design support

This file lists third-party components that are bundled with ZANACMS and
separately notes external design families that ZANACMS can support through
included generators.

Third-party components are not licensed under the ZANACMS Community License.
They remain under their respective original licenses. The ZANACMS commercial
site license does not replace or override any third-party license terms,
including attribution requirements.

External design packages listed as "not included" are not part of this
ZANACMS package. ZANACMS only contains support code or a generator for them.
If you install such a design under `/layout/<family>/<design>/`, the original
design files remain under their original upstream license.

---

# Bundled third-party components

## water.css

Component: water.css

Project: https://github.com/kognise/water.css

License: MIT

Modifications: none

## Pico CSS

Component: Pico CSS

Project: https://picocss.com/

License: MIT for code; documentation is licensed separately by Pico CSS

Modifications: none

## WonderCMS Sky

Component: Sky WonderCMS Theme

Source: https://github.com/robiso/sky

Author: Robert Isoski

License: MIT

Version: 3.5.0

ZANACMS bundles the Sky theme files as a WonderCMS theme under
`/layout/wondercms/sky/`. The original Sky files should remain unchanged.
ZP-specific rendering is handled by `layout/_zpgen/wondercms/zpgen.php`;
ZP-specific CSS adjustments are kept in `/layout/wondercms/sky/zp.css`.

## Catamaran

Component: Catamaran Font Software

Source: https://fonts.google.com/specimen/Catamaran

Author: Pria Ravichandran

License: SIL Open Font License 1.1

Included font files, when the WonderCMS Sky theme is bundled locally:

- `/layout/wondercms/sky/css/fonts/catamaran-v7-latin-ext_latin-regular.woff2`
- `/layout/wondercms/sky/css/fonts/catamaran-v7-latin-ext_latin-700.woff2`

Modifications: none

## jQuery 1.12.4

Files:

- `layout/scripts/1.12.4/jquery.min.js`

Copyright:

Copyright OpenJS Foundation and other contributors, https://openjsf.org/

License:

MIT License

Project:

https://jquery.com/

## jQuery Migrate 1.4.1

Files:

- `layout/scripts/jquery-migrate-1.4.1.min.js`

Copyright:

Copyright OpenJS Foundation and other contributors, https://openjsf.org/

License:

MIT License

Project:

https://github.com/jquery/jquery-migrate

## Squire

Component: Squire HTML5 rich text editor

Files:

- `zp/scripts/squire/squire.js`
- `zp/scripts/squire/LICENSE`
- `zp/scripts/squire/version.txt`

Author: Neil Jenkins

License: MIT

Version: Release v2.4.5

Project:

https://github.com/fastmail/Squire

Source used for bundled file:

https://raw.githubusercontent.com/fastmail/Squire/refs/heads/master/dist/squire.js

Modifications: none

---

# Supported external design packages, not bundled

The following design families are supported by ZANACMS generators, but their
original theme, template or design files are not bundled in this package.
They must be downloaded separately and copied into the matching
`/layout/<family>/<design>/` directory.

## Editorial

Design: Editorial by HTML5 UP

Source: https://html5up.net/editorial

Author: AJ / @ajlkn

License of original design files: Creative Commons Attribution 3.0
(CC BY 3.0), unless you obtained a different license from HTML5 UP

ZANACMS includes only the Editorial family generator
(`layout/_zpgen/editorial/zpgen.php`). The original HTML5 UP Editorial
template files are not included in this package. To use this design, place
the Editorial design files under `/layout/editorial/editorial/`.

## Paradigm Shift

Design: Paradigm Shift by HTML5 UP

Source: https://html5up.net/paradigm-shift

Author: AJ / @ajlkn

License of original design files: Creative Commons Attribution 3.0
(CC BY 3.0), unless you obtained a different license from HTML5 UP

ZANACMS includes only the Paradigm Shift family generator
(`layout/_zpgen/paradigm-shift/zpgen.php`). The original HTML5 UP Paradigm
Shift template files are not included in this package. To use this design,
place the Paradigm Shift design files under
`/layout/paradigm-shift/paradigm-shift/`.

## WordPress-based external design snapshots

ZANACMS includes only the WordPress family generator and compatibility files
needed by that generator. It does not bundle the original WordPress themes
listed below in this package.

Examples of supported external WordPress-based design snapshots:

- `wordpress/graphy`
- `wordpress/syntax`
- `wordpress/independent-publisher-2`
- `wordpress/editor`
- `wordpress/minnow`
- `wordpress/sosimple`
- `wordpress/noto-simple`
- `wordpress/seedlet`
- `wordpress/generatepress`
- `wordpress/twentytwentyfive`

If these external design snapshots are used, each design remains under its
original upstream license. Tested snapshots, when available, are kept
separately at:

```text
https://github.com/gaxmann/zp-cms-designs
```

---

# MIT License text

The MIT License text below applies to the MIT-licensed bundled third-party
components listed above, unless the component ships its own license file with
more specific copyright information.

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
"Software"), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
