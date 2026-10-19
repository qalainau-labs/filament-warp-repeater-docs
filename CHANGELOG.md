# Changelog

All notable changes to `qalainau/filament-warp-repeater` will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.0.4] - 2026-10-20

### Changed

- The license agreement no longer lists a Lifetime tier. The tiers are Single Project and Unlimited Projects, each
  with one year of updates.
- The license agreement adds a refund policy (section 5): a full refund within seven days of purchase for a defect
  that cannot be fixed or worked around.

## [1.0.3] - 2026-10-20

### Fixed

- Fast scrolling no longer shows an empty band at the top or bottom edge of the lines for a moment. The canvas is now
  drawn 400px beyond the visible area above and below, so the lines are already there when the browser scrolls ahead
  of the next redraw.
- The counts above the table (`->warpChangeMarks()`) are rebuilt only when they change. A click on "1 new line" or
  "2 changed lines" was lost when a Livewire response arrived while the mouse button was down.

## [1.0.2] - 2026-10-14

### Fixed

- The shadow at the edge of frozen columns (`->warpFrozenColumns()`) is visible in dark mode.
- On a slow first visit, when Filament's font finished loading after the lines were first drawn, the canvas kept the
  fallback font's layout and text could run under the select chevrons. The lines are measured again once the font
  has loaded.
- Images in cells redraw when they fail to load.

## [1.0.1] - 2026-10-13

### Fixed

- While the lines are sorted or filtered, the reorder handles and the move buttons of the lines drawn on the canvas now look
  disabled too, like the ones of the line under the pointer.

## [1.0.0] - 2026-10-13

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
- Sorting and filters (`->warpSorting()`, `->warpFilters()`): a button in each column header sorts the lines and filters
  them by value. Only the display changes: the saved order and the hidden lines stay as they are.
- Fixed height (`->warpHeight()`): the lines scroll inside the repeater, while the header and the summary row stay in place.
- Wide tables (`->warpTableWidth()`) scroll horizontally, with frozen columns (`->warpFrozenColumns()`), for multi-level
  rows too.
- Narrow containers use Filament's stacked layout, like the native repeater.
- Automatic fallback to the regular Filament repeater for layouts other than `table()` and for empty repeaters.
