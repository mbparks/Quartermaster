# QUARTERMASTER v1.1.0: The Bins Learn Their Names

*Field Instrument No. 065 release notes*

A location system is only as real as its labels. QUARTERMASTER v1.0 gave every stock card a structured bin address like GSG/W2/S3/B07, but an address the shelf cannot display is just a promise. Version 1.1.0 keeps that promise with a label sheet generator.

The new Labels page filters your stock cards by class or by location prefix, lets you untick anything you do not want, and lays the rest out on standard Avery stock: 5160 for small bins (thirty labels per sheet, 2 5/8 by 1 inch) or 5163 for drawers and larger containers (ten per sheet, 4 by 2 inches). Each label carries the GSN, the nomenclature, the bin address, and the unit of issue, printed black on white no matter which theme you run.

My favorite small detail is the skip-used-positions offset. Anyone who prints labels knows the ritual of the partially used sheet: five labels gone from the top, twenty-five perfectly good ones remaining, and software that insists on starting at position one. Tell QUARTERMASTER how many positions are already used and it leaves them blank. Waste not.

The preview on screen is print-exact, rendered at true size in CSS inches. Print at 100 percent scale with no printer-added margins and the grid registers on the Avery die cuts. The print stylesheet cleanup also improved muster reports, which now print without the app chrome and with proper page padding.

Two more tests joined the harness, covering label selection and pagination math, bringing the count to seventeen. Still local-first, still one file, still no build step. Next up on the deferred list: QR codes on those labels, so a phone camera can open the stock card straight from the shelf.

Make. Hack. Learn. Share. Repeat.
