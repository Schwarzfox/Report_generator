# Changelog

The version is set in `APP_VERSION` at the top of the script in the main HTML file (named
`XRF Report Generator vX.Y.html`, kept in sync with `APP_VERSION` on each release) and shown as a
badge in the sidebar — never in the printed PDF.

Bump it **once per released set of changes**, not once per edit. A round of tweaks within a single
working session is one version.

- **MAJOR** — the printed report changes shape: sections move, columns restructure. A report printed
  under a new major does not look like one printed under the old major, so an archived PDF can be
  traced back to the version that made it.
- **MINOR** — new capability that leaves existing reports looking the same.
- **PATCH** — a fix or a tweak.

---

## 2.3

Header layout freed up for the company/customer block, the info line reorganised, and a set of
per-measurement editing tools so a batch of imported readings no longer has to be exactly right
on upload.

### Header layout

- **Company and customer logos/names are no longer squeezed into a middle column.** The instrument
  name and S/N used to sit between them in a 3-column grid, capping each party to a narrow slice of
  the page; the header is now a plain 2-column grid (company | customer), each free to use the full
  half-width.
- **Logos can be noticeably larger** — capped at 110px tall (was 80px) and up to the full width of
  their own column (was a fixed 260px), so a wide wordmark and a small mark both scale to fill the
  space now available.
- **Instrument name, S/N, calibration and the measurement count now read as one plain info line**
  below the report title, instead of a separate bold letterhead-style row. Operator sits on its own
  line directly beneath.
- **The instrument's built-in "(Default)" operator profiles no longer print that suffix.** A device
  operator literally named `Manager (Default)` on the instrument's own menu prints as `Manager` on
  the report; anything you type into the Operator field yourself is never touched.

### Editing loaded measurements

- **Every loaded measurement now has its own row in the sidebar**, showing its position number and
  an editable sample-name field, so a name can be corrected without re-uploading the file.
- **Remove a single measurement** with a ✕ on its row, instead of clearing everything and starting
  over.
- **Batch-rename all loaded measurements at once**, with an option to auto-append a number
  (`Coupon A 1`, `Coupon A 2`, …) so they stay distinct.

### Fixed

- **Landscape preview did nothing on a narrower window.** `.report-group` had a `max-width:100%`
  clamp that shrank both orientations down to the same on-screen width whenever the window was
  narrower than the true page size, making Landscape look identical to Portrait. Removed; the
  preview now always shows the page at its true size, scrolling horizontally if the window is too
  narrow.
- **Printing in Chrome/Brave sometimes still came out portrait after selecting Landscape.** The
  `@page{size:A4 landscape}` keyword form isn't reliably honoured by the print dialog's own Layout
  control in some Chromium builds; switched to explicit swapped page dimensions
  (`297mm 210mm` / `210mm 297mm`), which is more consistently respected.

### Known limitation

- **Safari does not use the page's CSS to select print orientation at all** — this is a Safari/
  WebKit limitation, not something the page can control. Landscape must be selected manually in
  Safari's own print dialog; the report layout itself adapts correctly once that's done. Chrome and
  Brave apply Landscape automatically.

---

## 2.2

German language support, plus a review pass over the whole generator. In English nothing about a
correct report changes — the fixes are about reports that were wrong, and about the tool appearing
to do nothing.

### German (EN / DE)

- **A language switch at the top of the sidebar**, remembered with the rest of your settings. It
  translates the whole tool: every sidebar label, placeholder, tooltip, dropdown option, prompt and
  confirm, and every string on the printed report — headings, table headers, chart labels and axes,
  the abbreviation keys, and Pass/Fail as **i.O. / n.i.O.**
- **Numbers use a decimal comma in German** (`0,014` not `0.014`), across measured values,
  statistics and chart axes. Sample names, calibration IDs and dates keep their own punctuation —
  a sample called `Sn9.04-Zn1.756` is not a number and is left alone.
- **Notation stays identical in both languages.** `SD`, `RSD`, `n`, `UCL`, `LCL`, `cps`, `keV`,
  `wt%`, `μm` are unchanged; only the explanation in the abbreviation key is translated. One
  concept, one notation, whichever language the report is in.
- **The instrument line reads `HITACHI FT230 Schichtdickenmessgerät`.** `HITACHI` and the model
  number are identity and never translate; the descriptor after them does.
- **Terminology is the German the trade actually uses**, not literal translation: `Messergebnisse`,
  `Prüfer`, `Spannweite`, `Häufigkeit` on the histogram axis, `Grenzwerte` for limits,
  `Eingriffsgrenzen` for UCL/LCL, `Wiederholmessungen`, `Flächenmasse` for g/m². The statistics
  table's first column is `Schicht / Element` — every row is one or the other, which beats an
  abstract measurand word on a report a customer reads.
- **Your own text is never touched.** Switching language rewrites the report title only if it is
  still our untouched default; anything you typed stays exactly as you typed it.
- Implemented as one `STR` table keyed `en`/`de` in the single HTML file, with static markup
  carrying `data-i18n` hooks — no second file to keep in sync.

### Wrong on paper

- **SD and RSD are no longer reported for a single measurement.** `stats()` returned `sd: 0` at
  n = 1, so single-shot reports printed "SD 0.00 · RSD 0.00%" — a claim of perfect repeatability
  from one reading. SD is undefined when the sample formula divides by n−1; both now print `—`.
  Mean, Min, Max and Range are unaffected (Range really is 0 for one reading).
- **The same element in two layers no longer prints two identical column headers.** A stack with,
  say, Fe in both the coating and the substrate produced two `Fe (wt%)` columns over different
  numbers. The layer is named only when there is a collision — `Fe in Ni (wt%)` — so the usual
  single-occurrence header stays short. Layer position is added if two layers share a name.
- **The spectrum axis no longer labels raw counts as cps.** With no live time to divide by, the
  trace is counts; the axis now says so instead of claiming counts per second.

### Appeared broken

- **Switching between loaded calibrations reloads the report-text fields.** Title, Operator, Notes
  and the calibration-name override kept showing the *previous* group's text while the report below
  showed the new group's — and the next keystroke silently overwrote the new group with the old
  text. The inputs now reload on a group change, and only on a group change, so typing is never
  interrupted mid-field.
- **Choosing the same files twice now works.** A file input only fires `change` when the selection
  changes, so Clear → Choose files → pick the same files did nothing at all. Same fix for the two
  logo pickers: removing a logo and re-uploading the same file was equally dead.
- **Skipped files are counted.** Dropping a folder of `lmm-` twins reported "0 measurement(s)
  loaded" and nothing else, which reads as a broken tool. The load line now ends with
  "· N other file(s) skipped" — a count only. Which files and why is the tool's business, not
  something the user should have to read.

### Smaller

- Column checkboxes built in JavaScript carry an id their label points at, so clicking the text
  toggles them like every other toggle in the sidebar.
- "Clear loaded data" also drops the per-calibration column choices, which otherwise accumulated in
  localStorage forever alongside the base64 logos. The confirm text says so.
- Toolbar hint corrected: only the selected group is rendered, so there was never a second group to
  print. Removed the page-break rule that waited for one.
- `compositionReport()`'s empty-group check moved above the loop, where it can actually fire.

---

## 2.1

- **Company / customer block made more prominent.** Name 14px → 18px, address 11.5px → 13px, logo
  cap 68px → 80px — they were reading as an afterthought next to the instrument header.

---

## 2.0

Rewrite of the v1 generator. Everything below shipped together.

### Report

- **Instrument header, always printed.** `HITACHI FT230 Coating Analyzer` with the serial beneath,
  read from `metadata.hwSerialNumbers`, centred above the report title. Both instruments are listed
  if a report spans two machines. The name is auto-fitted to the largest size that stays on one line
  of the printed page.
- **Uncertainty moved into its value's cell** (`9.22 ±0.14`), smaller and muted, instead of taking a
  column of its own — a 50-measurement alloy report went from 14 columns to 10.
- **Portrait / landscape**, driving both the preview width and the real `@page` size.
- **All editable text prints in one place**: title, operator, notes, then the results.
- **Abbreviation keys** at the end of each section, built from what that section actually rendered,
  so a report with no SPC chart never explains UCL/LCL. Only non-obvious terms are glossed.
- **One notation for standard deviation** — SD everywhere, including the histogram ticks
  (`+1 SD`) and the control-limit caption (`Limits: mean ± 3 SD`).
- Statistics table shows **Range** rather than a column repeating a constant `n`; `n` returns
  automatically when a quantity has missing values.
- Calibration / application sits in the metadata line under the title, as plain text.

### Data correctness

- **Spectrum peak accuracy.** Traces were built by taking every Nth channel to fit 2048 channels
  into ~376 pixels. XRF peaks are 3–5 channels wide, so the sampling grid missed apexes: the
  reference file's main peak drew 13% short and identical repeats drew different shapes. Buckets
  now aggregate by maximum — rendered peak matches the true peak exactly.
- **Element symbols and units keep their casing.** A `text-transform:uppercase` on table headers was
  rendering `Sn (wt%)` as `SN (WT%)` and `μm` as `ΜM` — a capital Mu, indistinguishable from M.
- **`±` is not claimed to be 1σ.** The JSON states no coverage factor and it cannot be derived
  (Poisson on total counts gives 0.23%, the reported error is 5.7%).
- **`sequenceNumber` of `-1`** is the instrument's "not set" placeholder and was printing as `#-1`;
  spectra are labelled by reading number instead.
- Energy axis stops where signal does (40 keV on the reference file, was 51.2).

### Printing

- **The preview is a sheet of A4** — 210mm of paper with the 12mm print margins as padding, so the
  186mm content area matches print exactly. Text sized on screen is sized for paper.
- Nothing in the header can clip: the instrument name wraps if a fit ever fails rather than being
  truncated mid-word.

### Interface

- **Saved company / customer profiles** with update / save-as-new / rename / delete. The single
  company and customer from v1 migrate into the first profile of each list. Logos are downscaled on
  upload, and a full browser storage reports an error instead of dropping settings silently.
- **Report text is per group.** Title, operator, notes and the calibration override used to be
  shared, so renaming one calibration renamed every loaded type. Each group keeps its own; unedited
  groups inherit the last-typed title/operator/notes, but never the calibration name.
- **Sidebar is seven collapsible cards**, each tagged with its current state and remembering whether
  it was open: files, report text, page & table, columns, sections, company, customer.
- **Tables** use horizontal rules with faint verticals, tight rows, and zebra striping throughout.
  Eight tints including fully white.
- Spectrum traces are solid, opaque, and painted so reading #1 sits in front.

## 1.x

Baseline, tagged `v1`. Single company and customer, `±` in its own column, three width presets,
no instrument header, portrait only.
