<img class="filament-hidden" src="https://raw.githubusercontent.com/qalainau-labs/filament-warp-repeater-docs/main/art/banner.jpg" alt="Warp Repeater">

# Warp Repeater

**Fast Filament table repeaters for long forms: the rows of a `Repeater::table()` are drawn on a `<canvas>`, with ledger-style multi-level rows that the native repeater does not have, while every field still behaves exactly like Filament's own.**

<a href="https://filament-warp-repeater.webllsystem.com/"><img src="https://raw.githubusercontent.com/qalainau-labs/filament-warp-repeater-docs/main/art/live-demo.png" alt="Try the live demo: filament-warp-repeater.webllsystem.com" width="592"></a>

Edit line items, open the searchable selects and date pickers, reorder lines and compare with the native repeater side by side at **[filament-warp-repeater.webllsystem.com](https://filament-warp-repeater.webllsystem.com/)**. No sign-up needed; the data is reset every hour.

A table repeater with a few hundred items gets slow, and with a thousand it may not render at all. Every item renders every field as Blade, and fields such as a `Select` repeat their whole option list in every row. The browser then has to parse megabytes of HTML and start thousands of Alpine components.

Warp Repeater keeps your existing `Repeater` definition. Add `->warp()` and the rows are drawn on a canvas. The header, the "Add" button, validation, actions and saving are still Filament's own. The row under the pointer and the row you are working in become real Filament fields, so typing, selecting, date pickers, dropdowns and the item actions are exactly the native ones.

```php
Repeater::make('items')
    ->table([...])
    ->schema([...])
    ->warp()
```

## Why

Measured on the demo's invoice form, whose line items have five fields, one of them a `Select` with 200 options (Chrome, Filament 5):

| | Native repeater | Warp Repeater |
| --- | --- | --- |
| 300 lines: HTML sent | 27 MB | 0.3 MB |
| 300 lines: DOM nodes | 88,175 | 809 |
| 300 lines: time to `load` | 3.0 s | 0.3 s |
| 300 lines: save | 4.3 s | 0.8 s |
| 1,000 lines | PHP runs out of memory (256 MB) | 0.6 MB, loads in 1.0 s |

On an order form with a `relationship()` repeater, a searchable select, a JavaScript date picker, a multiple select and `live()` totals, 300 lines go from 16.3 MB and 97,457 DOM nodes to 0.5 MB and 926 nodes, the page loads in 0.6 s instead of 1.8 s, and saving takes 1.3 s instead of 2.5 s.

## Features

- **Drop-in**: one method on the repeater you already have. Same schema, same `TableColumn` headers, same validation, actions and saving, including `relationship()` repeaters and `orderColumn()`.
- **Looks exactly like the native repeater.** The canvas draws what Filament renders: each field is rendered by Filament, measured in a hidden table with the same markup and CSS, and drawn from that. Compared pixel by pixel with the native repeater, in light and dark mode, on Chrome, Firefox and Safari.
- **Behaves like the native repeater**, because the rows you interact with are the native fields:
  - The row under the pointer and the focused row are real Filament fields. Clicking, typing, the caret, native selects, Filament's searchable select, the date picker panel, the clone, delete and move buttons all work as usual.
  - Tab and Shift+Tab move through the fields row by row, and in and out of the repeater, with each browser's own rules (Safari skips buttons by default, just like on the native repeater).
  - Values you type before saving are drawn from Livewire's state, so the canvas never shows stale data. `live()` fields and `afterStateUpdated()` hooks run as usual.
  - Validation errors appear under the fields, and the rows grow to fit them.
  - Drag-and-drop reordering, with Filament's own `reorder` action.
  - Narrow containers use Filament's stacked layout (each item as a card), like the native repeater.
- **Multi-level rows**: show each item on several lines, ledger style, with a multi-level header. The native repeater has no equivalent.
- **Theme aware**: colors, fonts, spacing and icons come from Filament's CSS, so custom themes and dark mode work without configuration.
- **Automatic fallback**: layouts other than `table()` and empty repeaters render the regular Filament repeater with no change on your side.

## Screenshots

The row under the pointer is made of real Filament fields, here with a multiple select open. The other rows are drawn on the canvas:

![Real fields on top of the canvas](https://raw.githubusercontent.com/qalainau-labs/filament-warp-repeater-docs/main/art/real-fields.png)

Multi-level rows, two lines per item:

![Multi-level rows](https://raw.githubusercontent.com/qalainau-labs/filament-warp-repeater-docs/main/art/multi-level-rows.png)

600 lines down an invoice with 1,000 lines:

![1,000 lines](https://raw.githubusercontent.com/qalainau-labs/filament-warp-repeater-docs/main/art/thousand-lines.png)

## Requirements

- PHP 8.3+
- Laravel 12 or 13
- Filament 5.x (Livewire 4)

Works in panels and in standalone Livewire components that use Filament's form builder.

## Installation

> **Warp Repeater is a commercial plugin.** A license is required. After purchase you receive credentials for the private Composer repository.

Add the repository and authenticate:

```bash
composer config repositories.qalainau composer https://qalainau.privato.pub/composer
composer config --auth http-basic.qalainau.privato.pub "your-email" "your-license-key"
```

The username is the email address on your license and the password is your license key. Use the same credentials on every machine and server that installs the package, such as your laptop, CI and production.

Install the package and publish Filament's assets:

```bash
composer require qalainau/filament-warp-repeater
php artisan filament:assets
```

The service provider is auto-discovered. There is nothing else to register.

> Run `php artisan filament:assets` again after every update of the package. The asset URLs contain a hash of the file contents, so browsers pick up the new build immediately.

## Usage

Call `->warp()` on any repeater with a `table()` layout:

```php
use Filament\Forms\Components\DatePicker;
use Filament\Forms\Components\Repeater;
use Filament\Forms\Components\Repeater\TableColumn;
use Filament\Forms\Components\Select;
use Filament\Forms\Components\TextInput;
use Filament\Forms\Components\Toggle;
use Filament\Schemas\Components\Utilities\Get;
use Filament\Schemas\Components\Utilities\Set;

Repeater::make('lines')
    ->relationship()
    ->orderColumn('sort')
    ->table([
        TableColumn::make('Product')->markAsRequired(),
        TableColumn::make('Delivery'),
        TableColumn::make('Qty')->width('6rem'),
        TableColumn::make('Unit price')->width('9rem'),
        TableColumn::make('Taxable'),
    ])
    ->schema([
        Select::make('product_id')
            ->relationship('product', 'name')
            ->searchable()
            ->preload()
            ->required(),
        DatePicker::make('delivery_on')->native(false),
        TextInput::make('quantity')
            ->numeric()
            ->live(onBlur: true)
            ->afterStateUpdated(fn (Get $get, Set $set) => $set('amount', $get('quantity') * $get('unit_price'))),
        TextInput::make('unit_price')->numeric()->prefix('$'),
        Toggle::make('taxable'),
    ])
    ->warp();
```

`->warp()` accepts a boolean or a closure, so you can enable it conditionally:

```php
Repeater::make('lines')->warp(fn (): bool => auth()->user()->prefersFastForms());
```

`$repeater->isWarp()` tells you whether Warp Repeater is enabled for a repeater.

## Multi-level rows

Items with many fields are easier to read when each item spans several lines, like a paper ledger. `warpMultiLevel()` lays the fields out on a grid: every item gets the same number of lines, and each field is placed on the grid with `warpCell()`.

```php
use Qalainau\FilamentWarpRepeater\MultiLevel\HeaderCell;

Repeater::make('lines')
    ->table([...])
    ->schema([
        Select::make('product_id')->warpCell(row: 1, col: 1, colSpan: 2),
        TextInput::make('quantity')->warpCell(row: 1, col: 3),
        TextInput::make('unit_price')->warpCell(row: 1, col: 4),
        DatePicker::make('delivery_on')->warpCell(row: 2, col: 1),
        Checkbox::make('gift_wrap')->warpCell(row: 2, col: 2),
        Textarea::make('note')->warpCell(row: 2, col: 3, colSpan: 2),
    ])
    ->warp()
    ->warpMultiLevel(columns: 4, rows: 2, header: [
        HeaderCell::make('Product')->row(1)->col(1)->colSpan(2)->markAsRequired(),
        HeaderCell::make('Qty')->row(1)->col(3),
        HeaderCell::make('Unit price')->row(1)->col(4),
        HeaderCell::make('Delivery')->row(2)->col(1),
        HeaderCell::make('Gift')->row(2)->col(2),
        HeaderCell::make('Note')->row(2)->col(3)->colSpan(2),
    ]);
```

- `warpCell(row, col, rowSpan, colSpan)` places a field (1-based). If you give only `row`, the field takes the first free place on that line. Fields without `warpCell()` fill the remaining free places line by line, like CSS Grid auto-placement. Extra lines are added when the grid is full.
- `header` is optional. Without it, each `TableColumn` header is shown at its field's position. `HeaderCell` supports `->alignment()`, `->markAsRequired()` and `->wrapHeader()`.
- `bordered: false` removes the lines between the columns of the grid. The lines between the lines of an item stay.
- The reorder handle and the item actions span the full height of the item.
- Tab moves through the fields line by line, then to the item actions.
- The column widths of the grid are computed from the content, the same way as in the regular layout.

Multi-level rows are a Warp Repeater layout. When Warp Repeater is disabled, when it falls back to the native repeater, and in narrow containers where Filament stacks each item as a card, the items are shown in the regular layout.

## Supported fields

Every field works, because the fields you interact with are always Filament's own. The canvas only has to draw the rows you are not interacting with:

| Field | Drawn on the canvas |
| --- | --- |
| `TextInput` | The current value (or the placeholder), prefixes and suffixes, icons, password dots, number fields with the browser's spin buttons. |
| `Select` | Native selects, searchable and JavaScript selects (the selected label, wrapped like the native one) and multiple selects (the selected badges). |
| `DatePicker`, `DateTimePicker`, `TimePicker` | Native inputs, and JavaScript pickers with the value in their display format. |
| `Toggle`, `Checkbox` | On and off states with their colors. |
| `Textarea` | The text, wrapped like the browser does, with the resize handle. |
| Anything else | The HTML that Filament renders for the field, for example placeholders and text entries, hints, helper texts and error messages. |

Values changed in the browser and not yet sent to the server are drawn from Livewire's state. The labels of searchable selects and the display format of JavaScript date pickers are computed in the browser until the next request.

## Automatic fallback

Warp Repeater only replaces the rows. In these states the regular Filament repeater is rendered instead:

- Repeaters without a `table()` layout (the default card layout and `simple()`).
- An empty repeater. Only the "Add" button is shown, like the native repeater.

## Limitations

The canvas cannot do everything the DOM can. These are the known differences from the native repeater:

- **Screen readers** cannot read the rows that are drawn on the canvas. The first, the last, the hovered and the focused rows are real fields, but the others are not part of the accessibility tree. If your users rely on assistive technology, keep the native repeater for them. `->warp()` accepts a closure, so you can base it on a user preference.
- **Browser find** (Cmd/Ctrl+F) does not find the values of rows that are drawn on the canvas.
- **Selecting text with the mouse across several rows** is not possible. Inside one row it works, because the row under the pointer is real.
- **Fields whose display is built in the browser** other than the ones listed above, for example rich editors, file uploads and color pickers, are drawn from the HTML that Filament renders on the server, which may look plainer than the initialized field. They become fully interactive when the pointer is over the row.

## How it works

1. **Templates instead of rows.** On every Livewire render, Filament renders every cell as usual. Cells that are identical apart from the item key become one template, so 1,000 lines usually need fewer than ten templates. Values that differ per row, such as the selected label of a searchable select, are sent separately.
2. **The browser measures Filament's own markup.** A hidden table with the same markup and CSS as the native repeater gives the column widths, the row heights and a list of boxes, texts and icons to draw. Fields that Alpine renders are initialized once, detached from Livewire, to measure their real look.
3. **Canvas drawing.** The rows on screen are drawn from those lists, with the current values from Livewire's state. Scrolling only repaints the visible part.
4. **Real fields where you work.** The row under the pointer and the focused row are built from the templates as real Filament fields, bound to the item's state, on top of the canvas. Everything you do there is Filament's own code.

## Troubleshooting

**The repeater still looks like the native one.** Check whether the repeater has a `table()` layout and at least one item. Also make sure you ran `php artisan filament:assets` after installing or updating.

**Old behavior after an update.** Run `php artisan filament:assets` again. Asset URLs include a content hash, so a hard refresh is not needed once the new files are published.

## Changelog

See [CHANGELOG.md](CHANGELOG.md).

## Support

Open an issue at https://github.com/qalainau-labs/filament-warp-repeater-docs/issues. Do not post your license key in the issue.

## License

Warp Repeater is commercial software. See [LICENSE.md](LICENSE.md) for the license terms.
