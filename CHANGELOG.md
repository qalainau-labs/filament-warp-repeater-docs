# Changelog

All notable changes to `qalainau/filament-warp-repeater` will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.0.7] - 2026-10-25

### Added

- README: a "Multi-tenancy" section. Warp Repeater works with Filament's tenancy (`Panel::tenant()`) as it is:
  select options and pasted values only match the records of the current tenant. Tests with a real tenant panel.

### Fixed

- No more "Error fetching option label" in the browser console when a line gets a value that is not one of the
  options of its select (for example an id copied down with Ctrl/Cmd+D). The editable line that was rebuilt is now
  cleaned up right away, so its old select no longer tries to fetch a label after it left the page.

## [1.0.6] - 2026-10-20

### Fixed

- Pasting a date and time into a date picker with its own timezone (`timezone()` or `FilamentTimezone::set()`) no
  longer moves it by the time difference. The pasted text is read as the time shown in the field. Filling dates down
  with Ctrl/Cmd+D keeps them as they are, instead of moving them again on every fill.
- `->warpChangeMarks()` on Filament resource pages clears the marks only when the record was saved. A save stopped by
  `$this->halt()` in a hook such as `beforeSave()` used to clear them too.
- Values typed while the save request was on its way keep their marks. They were taken as saved.
- When the form is filled again, for example by a header action that reloads the record, the change marks start from
  the new values, and a sorted or filtered view is applied again. Every line used to show as removed and added again,
  and the lines kept the old order under a "sorted" label.
- Deleting the last line and adding a new one keeps the change marks (one removed line, one new line), also after
  more than two seconds without lines.
- After a repeater went through zero lines, the sort and filter buttons in the column headers work again, the search
  field shows the text that is still highlighted, and an open filter panel is closed instead of staying invisible.
- Saving again with the same validation errors scrolls to the first line with an error again. Before, nothing moved,
  and the scroll happened later, after an unrelated update.
- The error marks next to the table name the line by its line number, also while the lines are sorted or filtered,
  and the "2 of 5" position starts again when the lines with errors change.
- A selected range of cells stays on the same lines when lines are added or reordered elsewhere, and is cleared when
  one of its lines is removed. It used to point at whatever line had moved into its place.
- A drag of a line is stopped when the lines change under it, instead of moving another line.
- An open filter panel lists the current values after an update.
- Change marks treat a toggle's `1` and `true`, and the options `[1, 2]` and `["1", "2"]` of a multiple select, as
  the same value.
- Text typed into an `live(onBlur: true)` field is kept when an update rebuilds that field before it loses focus,
  unless the server changed its value.
- The labels of searchable selects wrap at the right place after the page's web font loads late.
- Repeaters whose item keys are plain numbers (`generateUuidUsing(false)`) fall back to the native table. Their cells
  were broken, because the number also appears elsewhere in the HTML of a cell.

## [1.0.5] - 2026-10-20

### Changed

- Support moves to https://warpui.com/support?plugin=repeater: questions before and after buying, bug reports (also
  private ones) and an email when the fix is released. The README, the license agreement and the `support` links in
  `composer.json` point there, and `composer.json` links the public docs instead of the private repository.

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
