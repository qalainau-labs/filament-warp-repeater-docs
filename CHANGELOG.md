# Changelog

All notable changes to `qalainau/filament-warp-repeater` will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.11.1] - 2026-11-28

### Fixed

- A table with `warpTableWidth()` and no `warpStickyHeader()` no longer makes the box around it scroll sideways too (for example a Warp Split View pane, or the whole page). The screen-reader label of the actions column header was placed past the table's right edge, outside the table's own scroll area.

## [1.11.0] - 2026-11-28

### Added

- `TextInput::warpGrouping()` shows `numeric()` and `integer()` fields with thousands separators in the page's language (`12,000` in English, `12.000` in German), the same on canvas lines and on the line being edited. The focused field shows the plain number to type over. Livewire state, saved values, clipboard and spreadsheet copies and `warpExport()` stay plain numbers. The field uses `type="text"`; `numeric()` validation still applies.

### Fixed

- With `warpBulkActions()` and `warpImport()` / `warpExport()`, the bulk hint and the import and export buttons share one row above the table instead of taking two rows.

## [1.10.0] - 2026-11-25

### Added

- `warpFormula(..., grouping: false)` keeps a calculated field without thousands separators.

### Changed

- Calculated `TextInput` fields made with `warpFormula()` now show thousands separators in the page's language (`12,000` in English, `12.000` in German), both on the canvas and in the focused row. Saved values, Livewire state, clipboard and spreadsheet copies and `warpExport()` files stay plain numbers. Such fields now use `type="text"` instead of `numeric()`'s `type="number"`, because a number input cannot hold a separator; `numeric()` validation still applies.

## [1.9.0] - 2026-11-25

### Added

- `warpFormula()` supports `rounddown(x, n)` and `roundup(x, n)`, which round toward and away from zero like Excel's `ROUNDDOWN` and `ROUNDUP`, so negative lines such as discounts are rounded by their size. The browser, the server recalculation on save and `warpExport()` give the same result, including values such as `4.35` and `0.1 + 0.2` that floating-point numbers cannot store exactly. `round()`, `rounddown()` and `roundup()` also accept a negative literal digit count such as `-2` to round to hundreds.

## [1.8.0] - 2026-11-23

### Added

- `warpSections()` groups consecutive line items with editable headings, line insertion, optional collapsing and per-section subtotals.

- Import line items from CSV, TSV or Excel with column matching and append/replace confirmation; export all lines to CSV or Excel with safe literal values.
- `warpSpreadsheet()` undo and redo with up to 50 operations, native text editing undo, protected server restoration, and history reset after saves or external updates.
- A downward fill handle for spreadsheet ranges, with number, date and numbered text series, repetition for other values, and a destination preview.

- Opt-in `warpBulkActions()` for line selection, bulk deletion, duplication, and column value updates, with server-side permissions and item limits.

- `warpFormula()` calculates read-only fields from other fields in the same line while typing, without a server request, with dependency ordering, optional rounding, column summaries and trusted server recalculation on save.

### Fixed

- Narrow time inputs clip canvas text at the same point as the native clock button, avoiding stray text pixels when hovering a row.

- Empty repeaters use the native Filament add button again, including with spreadsheet editing, bulk actions, import/export or sections enabled.

- **Shortened text cut off without the ellipsis on canvas rows**: when a field cuts a long value with `text-overflow: ellipsis` (Warp Tree's `SelectTree` showing a long category name in a narrow column), the real field showed `Deferred revenue fro…` but the rows drawn on the canvas showed `Deferred revenue fro` cut mid-letter, so a row changed look when the mouse moved over it. The canvas now ends such a line with `…` at the same place.
- **Fields from other plugins drawn wrong on the canvas rows**: a field that extends Filament's `Select` or `DatePicker` but draws its own HTML (Warp Tree's `SelectTree`, Warp DatePicker's `WarpDatePicker`, and fields of the same kind from other packages) showed *Select an option* or the raw value (`2026-12-02`) on every row drawn on the canvas, and the browser console showed `Livewire Entangle Error: … cannot be found` each time the page opened. These fields are now drawn from a copy set up with each row's own value, so every row looks like the real field, including after you change a value in the browser.
- **Quick Look eye missing on middle rows**: with Warp Quick Look's `->quickLook()` on a `Select` inside a Warp table repeater, the eye button showed only on the first and last rows (the rows built from real fields) and the rows between showed an empty box. Every row now draws the eye whenever its select has a value.

## [1.7.1] - 2026-11-20

### Fixed

- **Console error `warpRepeater is not defined`**: on pages where Livewire re-renders the component inside another Alpine component, the browser console could show `warpRepeater is not defined` before the component's script had arrived. Empty components are now registered up front, and the real ones replace them as soon as they load. No visible change.

## [1.7.0] - 2026-11-19

### Added

- `RecordPicker::fillEmptyItems()`: records you pick go into the lines nobody has touched yet (every field still holds the value a new line starts with, `default()`s included) before any new line is added, from the top. With `defaultItems(1)`, the blank first line no longer stays behind and stops the save. Off by default, so existing forms behave as before.

### Fixed

- **Copying cells**: a cell whose text contains a lone carriage return (`\r`, e.g. text that came from an old Mac file) is now quoted when copied, so pasting it into Excel or Google Sheets no longer splits it into extra rows.

## [1.6.2] - 2026-11-16

### Fixed

- **Static analysis**: Larastan no longer reports “Call to an undefined method” for `->warp()`, `->warpStickyHeader()`, `->warpCell()`, `->warpSummary()` …. The plugin ships `extension.neon`, a PHPStan extension that reads Filament's macros (loaded automatically with `phpstan/extension-installer`; otherwise add it to `includes`). See “Static analysis” in the README.

## [1.6.1] - 2026-11-14

### Fixed

- Accessibility: the fields in the lines of a table repeater now have the column heading as their name for screen readers (inputs, selects, toggles and the other fields whose label is hidden in the table). Fields that already have their own name are left as they are.

## [1.6.0] - 2026-11-13

### Added

- `RecordPicker`: a list of your records (products, services…) beside the repeater or in a dialog (`modal()`). Drag a
  row onto the lines, or tick rows and press “Add ticked rows”, and each record becomes a new line with the values of
  `fillUsing()`. With `mergeDuplicates('product_id', increment: 'quantity')`, a product that is already in the lines
  raises its quantity instead. The keys are read again on the server with your `query()`, so only its records can be
  added, and nothing is saved until the form is saved. Works with the native repeater too. Nothing changes for forms
  that do not add the component. See [Add lines from a list](README.md#add-lines-from-a-list).

## [1.5.1] - 2026-11-12

### Changed

- Now requires Filament 5.8.3 or later (was 5.0). Older 5.x versions have known security advisories (Composer 2.9 and later refuses to install them), and the plugin cannot be tested on them. The tests now also run on the lowest supported versions before each release.

## [1.5.0] - 2026-11-10

### Changed

- `warpSearch()`: the position ("2 of 14") is shown inside the search field, the previous and next buttons sit right after it, show their names when you point at them, and are disabled until something matches.
- A `readOnly()` text input or textarea gets the gray background of a disabled field (without the shadow), on the canvas and in the live rows, so a calculated amount no longer looks like a field you can type in.

### Fixed

- JavaScript date pickers in languages written with an underscore (`zh_CN`, `zh_TW`, `pt_BR`) showed English month names ("Jan 16, 2027") in the rows drawn in the browser, next to "12月 14, 2026" in the rows from the server.

## [1.4.0] - 2026-11-07

### Added

- `WarpRepeater::markSaved($livewire)` tells the repeater that the form was saved, so `warpChangeMarks()` clears its
  marks after a save that does not go through `save()`, `create()` or a `resetOn` method. See
  [Unsaved changes](README.md#unsaved-changes).

### Fixed

- `warpChangeMarks()`: a Filament action whose closure saves the form through the page's `save()` (for example a "Save
  draft" button) left the marks in place, because the Livewire call is `mountAction` and not a `resetOn` method. Any
  response that reports a saved record now clears the marks; responses with validation errors still keep them.

### Documentation

- README "Column summaries": summaries are for display only and are not saved. How to store a total from the saved
  lines in `afterSave()` / `afterCreate()` (the lines of a `relationship()` repeater are not in the `$data` of
  `mutateFormDataBeforeSave()`).
- Laravel Boost guideline: the stored-total advice no longer points to `mutateFormDataBeforeSave()`, which cannot see
  the lines of a `relationship()` repeater. The guideline and the skill now mention pasting SKUs or other codes into a
  `relationship()` select with `->searchable(['name', 'sku'])`, and saving from an action's closure.

## [1.3.0] - 2026-11-06

### Added

- A JavaScript API for browser tests: `cellPoint({ row, column })`, `rows()` and `whenIdle()` on the repeater's Alpine
  component find a field drawn on the canvas, read the lines as shown and wait until requests and drawing are done. See
  [Browser tests (JavaScript)](README.md#browser-tests-javascript).

## [1.2.0] - 2026-11-06

### Added

- Instructions for AI coding assistants (Claude Code, Cursor, Codex) through [Laravel Boost](https://laravel.com/docs/boost):
  a guideline that Boost adds to `CLAUDE.md` and `AGENTS.md`, and the `warp-repeater-development` skill for building
  warp repeaters and testing them. Select `qalainau/filament-warp-repeater` in `php artisan boost:install`, or add it to
  `"packages"` in `boost.json`. See [AI coding assistants (Laravel Boost)](README.md#ai-coding-assistants-laravel-boost).

### Documentation

- README: "Testing with tenancy" shows how to test the lines in a panel with tenancy with Filament's form assertions
  (`assertFormSet()`, and `assertHasFormErrors()` for a product of another team). Warp Repeater itself has nothing
  tenant-specific to test.

## [1.1.1] - 2026-11-02

### Fixed

- Simplified Chinese: the "Sorted by" message is now a full sentence.

## [1.1.0] - 2026-10-31

### Added

- German, French, Spanish, Italian, Brazilian Portuguese, Korean and Chinese (simplified and traditional)
  translations, next to English and Japanese.

## [1.0.13] - 2026-10-30

### Added

- Pasting into a searchable `relationship()` select also accepts the exact value of one of its search columns: with
  `->searchable(['name', 'sku'])`, a column of SKUs copied from a spreadsheet picks the products. The value must belong
  to exactly one record among the select's own search results.

### Fixed

- Pasting many lines was slow: 400 lines into a field with an `afterStateUpdated()` hook took 16 seconds, because every
  pasted cell also ran the repeater's hooks, which read the state of every line again. The repeater's hooks now run once
  per paste (the fields' own hooks still run for every cell): 2.5 seconds for the same paste. Missing lines are added
  all at once.
- A relationship select stopped looking values up after 100 different values in one paste; it now looks up as many
  as the paste has lines (1,000 at most).
- Pasting text that is not an option into a multiple select cleared its options; the old options now stay. An empty
  cell still clears them.

## [1.0.12] - 2026-10-28

### Fixed

- Adding the first item to an empty repeater (which is drawn as Filament's own repeater) failed with
  `warpRepeater is not defined` in the browser console, and the new item could not be shown. Livewire turned the
  element of the empty repeater into Warp Repeater's element in place, so Alpine evaluated `x-data` before the
  component was loaded. Warp Repeater's element now has a `wire:key` of its own, so Livewire inserts it as a new
  element and Filament's `x-load` waits for the component, as for any other lazily loaded field. If you registered
  `warpRepeater` yourself as a workaround, you can remove it.

## [1.0.11] - 2026-10-28

### Fixed

- An empty date input in Japanese (and Chinese) on a row drawn on the canvas showed `年/月/日` starting 4px too far
  left. As in the browser's date input, the year field is wider than `年` and the text is centred in it (`年 /月/日`).

## [1.0.10] - 2026-10-28

### Fixed

- A date input (`TextInput::make()->type('date')`) in a row drawn on the canvas was an empty box when it had no
  value, and showed its value as plain text (`2026-09-15`) without the calendar icon, while the row under the pointer
  showed the browser's date input. Rows on the canvas now look like the browser's date input: the date in the browser's
  format (`09/15/2026`, `2026/09/15` in Japanese), the field markers when it is empty (`mm/dd/yyyy`, `年/月/日`), the
  calendar icon on the right, and the text cut before the icon in narrow columns. `time` (with the clock icon),
  `datetime-local`, `month` and `week` inputs are drawn the same way.
- In a searchable select (`Select::searchable()` and other selects drawn by Filament's JavaScript), a selected name
  that started with an ideographic space (`　`) lost that line on the canvas and was drawn one line too high. Text
  drawn on the canvas now treats ideographic and no-break spaces as the browser does: they are kept, and only runs of
  normal spaces, tabs and line breaks are collapsed to one space.

## [1.0.9] - 2026-10-28

### Fixed

- A `Textarea` drawn in the grid wrapped its text earlier than the browser. Japanese, Chinese and Korean text now
  breaks between any two characters, as in the browser's own textarea, so a part after a space (including the
  ideographic space `　`) fills the room left on the line before. Runs of Latin letters and digits stay whole, and
  brackets and punctuation follow the browser's line-break rules. Text is also measured with the same kerning as the
  page (Japanese fonts were measured about 1.4% wider).
- A word wider than the textarea that started in the middle of a line was drawn past the right edge. It is now split
  across lines, as the browser does (`overflow-wrap: break-word`).

## [1.0.8] - 2026-10-25

### Fixed

- With Warp Table in the same app, a Warp Repeater showed only its first row. Both plugins' CSS used the same class
  names (`.fi-warp-stage`, `.fi-warp-canvas` and others) and is loaded on every page. Warp Repeater's classes and CSS
  variables now start with `fi-warp-rep` (`.fi-warp-rep-stage`, `--fi-warp-rep-height`), so they no longer reach other
  plugins. If your own CSS targets a Warp Repeater class, add `rep-` after `fi-warp-` (`.fi-warp-repeater` becomes
  `.fi-warp-rep`).

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
