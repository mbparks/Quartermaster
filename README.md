# QUARTERMASTER

**Field Instrument No. 065 · Workshop Stores and Inventory · v1.1.0**

Workshop inventory on the supply department model. Not a spreadsheet. QUARTERMASTER manages stock cards, bin locations, reorder points, and periodic inventory audits (musters) for the Green Shoe Garage, using the vocabulary and discipline of Navy supply: receipts, issues, adjustments, surveys, and requisitions.

Motto: *Sighted, counted, stowed.*

## Core idea

The transaction ledger is the source of truth. Quantity on hand is never stored; it is a projection computed from the transaction history. The ledger is append-only. Corrections are made by appending a VOID transaction that nullifies a prior entry while preserving both records. Nothing is ever erased.

## Features (v1.1.0)

- **Stock cards** with auto-assigned GSNs (Green Shoe Numbers, format `CLASS-NNNN`). Sequence numbers are never reused, even after archive. Card face shows operational data; flip the card for notes and reference links.
- **Classes**: owner-defined taxonomy behind every GSN. Begin empty or seed a 16-class starter taxonomy with one click.
- **Bin locations** in structured `SITE/AREA/SHELF/BIN` format (e.g., `GSG/W2/S3/B07`), with a drill-down location browser that opens any shelf like a drawer.
- **Ledger** with four posting types (RECEIPT, ISSUE, ADJUST, SURVEY) plus VOID. Adjustments and surveys require a note. Issues cannot drive stock negative. Issues take an optional project tag with autocomplete.
- **Muster mode**: guided physical inventory of a location prefix, a class, or all stores. One card at a time with large touch targets (Sighted, Adjust Count, Not Sighted). Pause and resume freely; one muster open at a time. Close-out resolves not-sighted items (survey or skip), then files an immutable, printable report with an inventory accuracy percentage and a Supply Officer signature line.
- **Requisition workbook**: items at or below reorder point appear automatically, grouped by vendor with estimated cost. Pin items manually. Export as plain text or CSV. Receiving walks through quantity and bin confirmation and posts a receipt.
- **Label sheets**: print bin labels for any class or location prefix on Avery 5160 (30-up, 2 5/8 x 1 in) or Avery 5163 (10-up, 4 x 2 in) stock. Per-card include/exclude, print-exact preview, and a skip-used-positions offset so partially used sheets are not wasted. Labels always print black on white regardless of theme.
- **Manifest**: full JSON export and import. Local-first; no accounts, no servers, no telemetry.
- **Three themes**: night (red-lit storeroom, default), day (manila stock card), and high contrast. Theme choice persists.
- **Test harness** in-app: 17 tests covering GSN assignment, ledger math, void rules, the muster state machine, requisition thresholds, and label selection and pagination, run against an isolated state without touching live data.

## Running it

Open `quartermaster-v1.1.0.html` in any modern browser. No build step, no dependencies, no network required. Data persists in the browser via local storage; if storage is unavailable the app runs in memory and warns you to export before closing.

Debug logging: append `?debug=1` to the URL.

## Data model summary

| Entity | Notes |
|---|---|
| Class | `code`, `description`, `nextSeq`. Codes permanent. |
| StockCard | Descriptive only; never stores a count. Status ACTIVE or ARCHIVED. |
| Transaction | Append-only. `id`, `ts`, `type`, `gsn`, `delta`, `project`, `note`, `voids`. |
| Muster | Scope, lines with expected/observed/result, accuracy, archived on close. |

Archiving a card requires zero stock on hand (issue or survey the remainder first). Archived cards are hidden by default but never deleted.

## Known Limitations

- Single-browser storage. Data lives in the browser that filed it. Use Manifest export/import to move between machines. No sync.
- One muster open at a time. Spot-check (random sample) musters are not yet implemented.
- No QR or barcode scanning and no floor-plan location map yet. Both are on the deferred list.
- Label printing requires 100% scale and no printer-added margins for the Avery grids to register. Nomenclature is clipped, not shrunk, when it exceeds the label.
- Per-project bill of materials and cost views are not yet built, though the data accrues in the ledger from project-tagged issues.
- Import replaces the entire state. There is no merge.
- The requisition "Buy" column reflects the card's reorder quantity; it does not track partially received order remainders as a separate figure. Partial receipts work naturally because the line stays open until the count recovers above the reorder point.
- Currency is assumed USD and formatting is fixed.
- No HACKER ALMANAC cross-linking yet.

## Version history

- **v1.1.0** (2026-07-05): Label sheet generator. Avery 5160 and 5163 layouts, class and location-prefix filtering with per-card selection, skip-used-positions offset, print-exact preview, global print stylesheet cleanup (app chrome hidden, muster reports padded). Test harness grown to 17 tests.
- **v1.0.0** (2026-07-05): Initial MVP release. Stock cards, classes, locations, ledger with VOID soft-delete, full muster mode with archived reports, requisition workbook with text/CSV export and receiving, manifest export/import, three themes, in-app test harness (15 tests passing).

## License

GPL-3.0
