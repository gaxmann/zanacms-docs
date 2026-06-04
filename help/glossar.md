# Glossary

## configuration directory

The directory `/__config/`. It contains the active configuration file `conf.php`, the file to enable access to the Device manager (or Editing cookie setter) `EDITACCESS_ENABLE.php`, and the example configuration files.

## footer tokens

ZTOKENS used in footer configuration: `[[@contact]]` renders a full link, `[@contact]` gives the URL only, and `[footertext]` inserts translated footer text.

## HTML layout

A layout from the `html` family. Instead of a PHP generator, it uses a `design.html` template file with HVARS replacement variables.

## HVARS

HTML template replacement variables used by HTML layouts. HVARS are named placeholders in `/layout/html/<design>/design.html` that ZANACMS replaces with page content, navigation, footer or asset output.

## language code

A short language key such as `en` or `de`. Used in `$GLOBALS['zlangs']`, in MD filenames such as `index.en.md`, in Rich page language arrays and in URLs with `lg=en` or `/en/contact` when needed.

## MD editor

The optional admin editor for Markdown pages in MD mode. It uses an editor toolbar and a Markdown text field. Markdown files can also be edited directly, for example via FTP.

## mode

The content mode of the website: PHP mode, MD mode or Rich mode. The mode is set in `/__config/conf.php` and determines where page content is stored and how it is edited.

## page ID

The internal page name used by ZANACMS links and navigation, for example `contact` or `legalnotice`. Internal links use page IDs, not file paths or full URLs.

## page variables

Prepared values from `/pages/zvars.php` that can be inserted into MD or Rich page content with `[VAR:name]`. Also called ZVARS.

## Rich editor

The visual editor used in Rich mode for editing page content in the admin area.

## Rollback

A one-step rollback file created automatically before an update installs any files. It allows returning to the previous installed state with a single action.

## ZTOKENS

ZANACMS replacement tokens written as plain text and resolved by ZANACMS at render time. Examples include footer tokens such as `[[@contact]]` and page variable tokens such as `[VAR:name]`.

## ZVARS

The page variable system based on `/pages/zvars.php`. ZVARS let a PHP developer prepare values — from simple strings to generated HTML tables — that editors can insert into pages without writing PHP.

## More details

- [Intro glossary](../guide/index.md#5-glossary)
