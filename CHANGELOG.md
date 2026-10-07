# Changelog

All notable changes to `qalainau/filament-warp-repeater` will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- `->warp()` for Filament 5 table repeaters (`Repeater::table()`): the rows are drawn on a `<canvas>` while the
  header, the "Add" button, validation, actions and saving stay Filament's own. Works with `relationship()` and
  `orderColumn()` repeaters.
- Cells are rendered by Filament and shared as templates, measured in a hidden table with the native markup and
  drawn from Filament's own CSS: text inputs (prefixes, suffixes, icons, password, number spin buttons), native,
  searchable and multiple selects, date and time pickers (native and JavaScript), toggles, checkboxes, textareas,
  and the HTML of any other field.
- The row under the pointer and the focused row are real Filament fields, so editing, dropdowns, date pickers and
  the clone, delete and move actions are the native ones. Tab and Shift+Tab move between rows and in and out of
  the repeater with each browser's own rules.
- Values changed in the browser are drawn from Livewire's state before they are sent to the server. `live()` fields
  and `afterStateUpdated()` hooks work as usual.
- Validation errors under the fields, with rows that grow to fit them.
- Drag-and-drop reordering through Filament's `reorder` action.
- Sticky header (`->warpStickyHeader()`): the header row stays below the panel's topbar (or at the top of a modal's
  scrolling area) while the page scrolls.
- Saving with validation errors scrolls to the first line with an error, turns it into real fields and focuses the
  field, like the native form does for fields on screen.
- Error navigation (`->warpErrorNavigation()`): "n lines have errors" with previous and next buttons above the
  repeater, and markers for the lines with errors along the table. English and Japanese translations.
- Multi-level rows (`->warpMultiLevel()`): each item spans several lines on a grid, with a multi-level header
  (`HeaderCell`). Fields are placed with `->warpCell()`.
- Column summaries (`->warpSummary()` with `Summary::sum()`, `average()`, `min()`, `max()`, `count()` and `checked()`): a
  summary row under the table, computed in the browser from the Livewire state and updated as you type.
- Spreadsheet editing (`->warpSpreadsheet()`): Enter and arrow keys move between lines, tables pasted from a
  spreadsheet fill the cells and add lines, Shift+click selects a range to copy as tab-separated text or fill down with
  Ctrl/Cmd+D.
- Unsaved changes (`->warpChangeMarks()`): marks on changed cells, changed lines and new lines, with the number of
  changed, new and removed lines above the repeater. The marks are reset when the form is saved.
- Search (`->warpSearch()`): a search field above the repeater that highlights the matching cells in every line and
  moves between them. With a sticky header, the tools above the repeater stay on screen too.
- Line numbers (`->warpRowNumbers()`): a first column with the number of each line.
- Fixed height (`->warpHeight()`): the lines scroll inside the repeater, while the header and the summary row stay in place.
- Narrow containers use Filament's stacked layout, like the native repeater.
- Automatic fallback to the regular Filament repeater for layouts other than `table()` and for empty repeaters.
