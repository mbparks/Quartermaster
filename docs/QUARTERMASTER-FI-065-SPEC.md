# QUARTERMASTER
## Field Instrument No. 065
### Workshop Stores and Inventory

**Document revision:** 1.0 (as built)
**Status:** Matches shipped release v1.0.0 (2026-07-05)
**Author:** M.B. Parks, Green Shoe Garage
**Motto:** "Sighted, counted, stowed."

---

## 1. Purpose

QUARTERMASTER is a workshop stores and inventory instrument built on the mental model of a ship's supply department rather than a spreadsheet. It manages stock records, bin locations, reorder points, and periodic inventory audits (musters) for the Green Shoe Garage. It answers three questions at the shelf: Do I have it? Where is it? Do I need to buy more?

The north star, as with all Field Instruments, is that owning your data and understanding your tools is worth the learning curve. The Navy supply vocabulary is not decoration. It provides a disciplined transaction model (receipt, issue, adjustment, survey) that keeps the record trustworthy.

## 2. Core Concepts and Vocabulary

| Term | Meaning in QUARTERMASTER |
|---|---|
| Stock Card | The record for one line item. The primary UI object. |
| GSN | Green Shoe Number. Local stock number: CLASS-NNNN (e.g., FAST-0114). |
| Class | Owner-defined taxonomy code (RES, CAP, FAST, ADHV, LUMBR, FILM, RADIO, etc.). |
| Unit of Issue | EA, PR, FT, PKG, RL, BX, and similar. |
| Bin Location | Structured location code: SITE / WALL-OR-AREA / SHELF / BIN. |
| Transaction | Any event changing quantity or status: RECEIPT, ISSUE, ADJUST, SURVEY. |
| Muster | A periodic inventory audit of a location or class. |
| Survey | Formal write-off of lost, damaged, or expended-untracked material. |
| Requisition | The reorder worklist generated from items at or below reorder point. |

## 3. Data Model

### 3.1 Source of truth

The transaction ledger is the source of truth. Quantity on hand for any stock card is a projection computed from its transaction history. Stock cards store descriptive data; they never store an authoritative count.

### 3.2 Ledger rules

- The ledger is append-only. Transactions are never edited or hard-deleted.
- Corrections are made by appending a reversing or superseding transaction that references the original by ID (soft-delete via VOID transaction type, which nullifies a prior transaction's effect while preserving both records).
- Every transaction carries: ID, timestamp, type, GSN, quantity delta, optional project tag, optional note, and optional reference to a voided transaction.
- ADJUST and SURVEY transactions require a note. VOID transactions require a note.
- Guards enforced as built: ISSUE and SURVEY cannot drive projected stock negative; a VOID is rejected if nullifying its target would drive projected stock negative; a VOID cannot target another VOID; a transaction can be voided only once.
- Archiving a stock card requires zero quantity on hand (issue or survey the remainder first). Archived cards are hidden by default and never deleted.

### 3.3 Entities

**StockCard**
- gsn (CLASS-NNNN, immutable once created)
- nomenclature (Navy-style: noun first, e.g., "STANDOFF, HEX, M3 X 10MM, BRASS")
- unitOfIssue
- binLocation
- reorderPoint (nullable; null means never flag)
- reorderQuantity (suggested buy amount)
- source / vendor (freeform)
- unitCost (nullable)
- notes, datasheet links
- status: ACTIVE or ARCHIVED (archive, never delete, per standing rule)

**Transaction** (see 3.2)

**Muster**
- ID, date opened, date closed, scope (location prefix or class)
- line results: SIGHTED, ADJUSTED (with delta and note), NOT_SIGHTED
- computed accuracy percentage
- archived as an immutable report on close

**Class registry**
- code, description, next sequence number (auto-increment per class)

### 3.4 GSN assignment

GSNs are assigned automatically: user picks a class, app issues the next sequence number. Sequence numbers are never reused, including after archive.

## 4. Bin Location System

Format: `SITE/AREA/SHELF/BIN`, e.g., `GSG/W2/S3/B07`.

- Location browser: pick any prefix and see every card mustered to it, like opening a drawer.
- Locations are freeform-but-structured: validated to the four-segment pattern, no pre-registration required in v1.0.
- Printable bin labels: GSN, nomenclature, location code. QR codes deferred (see Section 9).

## 5. Muster Mode

The signature feature. A muster is a guided audit workflow, one card at a time, designed for one-handed phone use while standing at a shelf.

**Workflow:**
1. Open a muster, choose scope (location prefix or class).
2. App presents each in-scope card with large touch targets:
   - SIGHTED (count confirmed as projected)
   - ADJUST (enter observed count; app appends ADJUST transaction; note required)
   - NOT SIGHTED (flags card for survey decision at close-out)
3. Close-out screen: resolve NOT SIGHTED items (survey now, or skip with note), review adjustments.
4. App generates a muster report: scope, date, accuracy percentage, discrepancy list, transactions generated. Report is stamped, archived, and immutable. Styled as a signed form with a Supply Officer signature line.

Musters can be paused and resumed. Only one muster open at a time in v1.0.

## 6. Requisition Workbook

- Any ACTIVE card whose projected quantity is at or below its reorder point appears on the requisition sheet automatically.
- Sheet is grouped by vendor/source, shows suggested reorder quantity, and estimated cost where unit cost is known.
- Export as plain text or CSV.
- Receiving workflow: mark a line received, confirm quantity and bin, app appends a RECEIPT transaction. Partial receipts supported (line stays open with remaining quantity).
- Manual add-to-requisition allowed for items not yet below reorder point.

## 7. Issue and Project Tagging

- Pulling parts is an ISSUE transaction, optionally tagged with a project name (freeform with autocomplete from prior tags).
- Byproduct view (deferred past v1.0): per-project bill of materials and cost reconstructed from issue history.

## 8. Aesthetic Direction

- Night-default theme: red-lit storeroom below decks. Dim, utilitarian, stenciled type.
- Day mode: manila stock card, typewriter face, rubber-stamp status marks (SIGHTED, SURVEYED, DUE IN) at slight random rotation.
- High Contrast theme included (standard when multiple themes exist). WCAG AA verified in all themes.
- Card-flip animation: face shows operational data; reverse shows notes, links, history.
- Muster reports and requisition sheets styled as period supply forms, printable.

## 9. Scope

### v1.0 MVP (build to this, then stop for review)

- Stock cards with GSN auto-assignment and class registry
- Append-only transaction ledger with VOID soft-delete corrections
- Projected quantities computed from ledger
- Bin location system and location browser
- Full muster mode with archived reports
- Requisition sheet with receiving workflow and text/CSV export
- Issue transactions with project tags
- Local-first storage with JSON export/import
- Night, day, and high-contrast themes
- Browser-runnable test harness (ledger math, GSN assignment, muster state machine)

### Deferred

- QR/barcode label scanning via camera
- SVG floor-plan location map
- Per-project BOM and costing views
- Spot-check (random sample) muster variant
- Photo attachments on cards
- HACKER ALMANAC cross-linking (open question: link part cards to component family reference entries)
- Printable label sheet generator

## 10. Development Standards (per Field Instrument template rev. 3)

- Local-first; export/import required.
- Single-file HTML aspirational, not mandatory; modular internally; no build step.
- No server-side component anticipated for v1.0. If one emerges, Cloudflare Workers first-class.
- Dynamic/fluid sizing; collapsible side menus.
- No sound planned; if added, in-app mute button required.
- In-app feedback mechanism.
- Auto-increment and display version number; each release saved as its own file.
- GitHub repository from day one.
- No backwards compatibility until requested; rewrite over patch when complexity creeps.
- Test harness must pass before release.
- Archive/soft-delete instead of hard delete throughout.
- Console logging behind a debug flag.
- README updated each revision, including Known Limitations section.
- Blog post draft accompanies each release.

## 11. Decisions and Open Questions

### Resolved in v1.0.0

1. Starter taxonomy: classes begin empty. A one-click "Seed Starter Taxonomy" action (16 classes) is offered on the Classes page only while no classes exist.
2. Nomenclature discipline: freeform, with a noun-first hint in the field label. Input is uppercased automatically; the style is encouraged, not enforced.

### Still open

3. Muster cadence reminders: in-scope for v1.x, or does QUARTERMASTER stay silent and let the owner decide when to muster?
4. Does the requisition sheet ever want vendor URLs per card for one-click cart building?

## 12. License

GPL-3.0
