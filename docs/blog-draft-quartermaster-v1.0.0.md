# QUARTERMASTER: Running the Workshop Like a Supply Department

*Field Instrument No. 065 · v1.0.0 release notes and design notes*

Every maker knows the moment. You are standing in a hardware store holding your third bag of M3 standoffs because you could not prove you already had two bags at home. Somewhere on a shelf, in a bin, behind the flux, they wait. The problem is not that you lack inventory. The problem is that you lack a supply department.

QUARTERMASTER is Field Instrument No. 065, and it borrows its whole worldview from Navy supply. I spent years around this system in the Civil Engineer Corps, and the thing civilians miss about military logistics is that the paperwork is not bureaucracy for its own sake. It is a discipline that makes the record trustworthy. Every change to stock is a transaction: a receipt, an issue, an adjustment, or a survey (the Navy's word for formally writing off what is lost or damaged). The count on the card is never the truth. The ledger is the truth, and the count is just what the ledger adds up to.

That principle is the spine of the app. QUARTERMASTER never stores a quantity. It stores an append-only transaction ledger and computes quantity on hand as a projection. Corrections do not edit history; they append a VOID record that nullifies the mistake while preserving both entries. If you have followed my other Field Instruments, you will recognize this as files-as-source-of-truth applied to a storeroom.

Each item gets a stock card with a Green Shoe Number, my own NSN-flavored scheme: a class code and a sequence, like FAST-0114. Cards carry Navy-style nomenclature (noun first: STANDOFF, HEX, M3 X 10MM, BRASS), a unit of issue, a structured bin location like GSG/W2/S3/B07, and a reorder point. Flip the card, literally, for notes and datasheet links. The night theme is a red-lit storeroom below decks. The day theme is a manila card with rubber-stamp status marks that sit at a slightly crooked angle, the way real stamps do.

The feature I care most about is muster mode. Periodic inventory should be a ritual, not a chore you never start. Open a muster on a shelf or a class and the app walks you through one card at a time with three big buttons: Sighted, Adjust Count, Not Sighted. It is built for one hand on a phone while the other hand is moving bins. When you close out, anything not sighted gets a disposition decision, and the app files an immutable report with an inventory accuracy percentage and a signature line for the Supply Officer. That officer is you. Congratulations on the billet.

Items that fall to their reorder point accumulate on a requisition sheet grouped by vendor, exportable as text or CSV for pasting into a cart. Receiving walks you through confirming quantity and bin, and posts the receipt to the ledger. Issue parts against a project name and, over time, the ledger quietly accumulates a per-project bill of materials without you ever setting out to track one.

As always: local-first, single HTML file, no build step, no accounts, no telemetry. Your data exports as JSON from the Manifest page any time you want it in hand. The in-app test harness runs fifteen tests against the ledger math, the GSN assigner, and the muster state machine, because an inventory tool you cannot trust is worse than no tool at all.

v1.0.0 is the MVP. Deferred for later: QR bin labels, a floor-plan map of the garage, spot-check musters, and cross-links to the Hacker Almanac. The storeroom light is red, the stamps are inked, and the first muster is waiting.

Make. Hack. Learn. Share. Repeat.
