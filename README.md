# Footnotes (UC Law SF Fork)

This is a customized fork of the [Drupal Footnotes module](https://www.drupal.org/project/footnotes) (based on `4.0.0-beta7`), patched to work with the UC Law SF Drupal site. It is distributed under the package name `sandstormdesign/footnotes`.

* Original module: https://www.drupal.org/project/footnotes
* Bug reports for the upstream module: https://www.drupal.org/project/issues/footnotes

---

# Changes from Upstream (4.0.0-beta7)

## composer.json

The package name has been changed from `drupal/footnotes` to `sandstormdesign/footnotes` to distinguish this fork from the upstream package and allow it to be required independently via Composer.

## footnotes.install

### `footnotes_update_10002` — Updated docblock

The docblock for `footnotes_update_10002` has been clarified to note that this update also handles upgrading 3.x footnote content to 4.x. The prerequisites are now explicitly documented: the module must already be on the 4.x branch with CKEditor 5, and the footnotes button must be added to the toolbar before running this update hook.

### `footnotes_update_10004` — New update hook

A new database update hook `footnotes_update_10004` has been added. It ensures that the `footnote` text format and its corresponding CKEditor editor configuration are installed. If the `footnote` format does not exist, it reads the config from `config/optional/filter.format.footnote.yml` and `config/optional/editor.editor.footnote.yml` and installs them programmatically. If the format already exists, it skips installation and returns a notice.

## templates/footnote-link.html.twig

The CSS class on the citation anchor element has been changed:

- **Before:** `class="footnote__citation js-footnote-citation"`
- **After:** `class="see-footnote"`

This aligns the markup with UC Law SF's existing CSS conventions and removes the JavaScript hook class that is not used in this deployment.

## templates/footnote-links.html.twig

The wrapping `<span class="footnote__citations-wrapper">` element that surrounded the list of inline citation links has been removed. The citation links are now rendered directly without this wrapper, simplifying the markup for theming purposes.

## templates/footnote-list.html.twig

Backlink markup has been removed from the footnote list output. Specifically:

- The `<span class="footnotes__item-backlinks">` wrapper and the `<a class="footnotes__item-backlink ...">` anchor elements have been removed from both the single-footnote and grouped-footnote rendering branches.
- Footnotes now render only the footnote text (`<span class="footnotes__item-text ...">`) without any backlinks.

This change removes the back-navigation links from the footnotes list section entirely, which is the desired behavior for the UC Law SF site.

---

# Introduction

The Footnotes module is used to easily create automatically numbered footnote references in an article or post (such as a reference to a URL). It provides a toolbar icon which opens a modal window containing its own toolbar, allowing the user to add bold, italics, and links within a footnote. It automatically creates citations and footnotes, along with links between them.

# Installation

Install the module as you would normally install a contributed Drupal module. Visit https://www.drupal.org/docs/extending-drupal/installing-modules for further information.

To install this fork via Composer, require `sandstormdesign/footnotes` instead of `drupal/footnotes` and add the appropriate repository entry to your `composer.json`.

# Configuration

* Go to **Configuration → Text formats** to enable the footnotes filter in an input format.
* Drag the footnotes toolbar icon into the active toolbar.
* Enable the **Footnotes Filter** and ensure it appears before **"Correct faulty and chopped off HTML"** and **"Limit allowed HTML tags and correct faulty HTML"** (if enabled).

## Grouping Multiple Formatted Texts into a Single Footnote Reference Area

To group footnotes from multiple fields into one block, use the **Footnotes Group** block.

* Ensure the block appears after the **"Main page content"** block.
* Set **"Current entity context for caching"** to `node` to prevent caching issues.
* Check **"Disable output of the footnotes footer"** in the filter configuration (**Configuration → Text formats**).

If content is loaded via lazy builder and footnotes are not all appearing:

* Uncheck **"Disable output of the footnotes footer"** in the filter configuration.
* Check **"Group footnotes using JavaScript"** in the block configuration.

Note: If footnotes are manually numbered (e.g. 1, 2, 1, 2), they will not be renumbered when combined. Renumbering only applies when values are left blank (i.e., automated numbering).

## Collapsing Identical Footnotes

If two citations reference an identical source:

* Ensure the footnote **content** is identical.
* Check **"Collapse footnotes with identical content"** in the filter configuration (**Configuration → Text formats**).

## Editing Available Options in the Footnotes Modal Window

The footnotes modal is configured at **Configuration > Text Formats > Footnotes**. Note that Drupal Core does not support modal windows within modal windows, so **Media is not supported** within a footnote. Bubble dialogs (such as Link) are supported and Link is enabled by default.

# Theming

Copy the templates into your theme as needed and clear the theme registry cache (`drush cc theme-registry`).

* `footnote-link.html.twig` — The individual citation link. In this fork, uses the class `see-footnote` instead of the upstream `footnote__citation`.
* `footnote-links.html.twig` — Multiple citations rendered inline next to each other. In this fork, the wrapping `<span>` has been removed.
* `footnote-list.html.twig` — The footnote list rendered after the text (or in the footnotes group block). In this fork, backlinks have been removed from this output.

# Upgrading from 3.x

First run `drush updb`.

Then use the Drush commands provided — **test thoroughly before applying to any production environment**:

```bash
drush footnotes:upgrade-3-to-4 node
drush footnotes:upgrade-3-to-4 paragraph
drush footnotes:upgrade-3-to-4 block
drush footnotes:upgrade-3-to-4 taxonomy_term
drush footnotes:upgrade-3-to-4 your_custom_entity_type
```

If upgrading from 3.x to this fork, also run the new `footnotes_update_10004` update hook via `drush updb` to ensure the `footnote` text format and editor configuration are installed.

# Debugging Pasting from Word or Elsewhere

1. Add `?debug=1` to the URL (e.g. `/node/add/page?debug=1`).
2. Paste content into the CKEditor.
3. View the browser console output.