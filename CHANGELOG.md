# Changelog

Versions are `MAJOR.MINOR.PATCH`, set in `APP_VERSION` at the top of the script in
`XRF Report Generator.html` and shown as a badge in the sidebar (never in the printed PDF).

- **MAJOR** — the printed report changes shape: sections move, columns restructure. A report
  printed under a new major does not look like one printed under the old major, so an archived
  PDF can be traced back to the version that made it.
- **MINOR** — a new capability that leaves existing reports looking the same: a new option, a
  new field, a new format choice.
- **PATCH** — a fix or a tweak. Nothing new.

Bump the version in the same commit as the change and add a line here.

---

## 2.6.1

Spectrum palette set to `#3380ff` / `#ef476f` / `#ffd166`. The blue is the same one the statistics
chart plots its readings in, so a reading looks the same in both charts. `#ffd166` is pale against
white, so that trace carries extra stroke weight. Slots 4–6 extended in the same family:
`#118ab2`, `#8338ec`, `#073b4c`.

## 2.6.0

**Instrument line sized per orientation** — 22px in landscape, 18px in portrait, where the narrower
middle column was pushing the name onto a third line. The serial follows at 15px / 13.5px.

**Company and customer logos share a top edge.** The header row was centring each block vertically,
so identical logos sat at different heights whenever one block had more lines than the other (a Job
number, a longer address). Blocks are top-aligned now; only the instrument stays vertically centred.

**Calibration / application is plain text**, not a chip.

**Moving-range chart gained its centre line.** It had a UCL and nothing else, while the run chart
above it shows mean, UCL and LCL. It now draws MR-bar, the average change between consecutive
readings. There is deliberately still no LCL: a range cannot fall below zero, so the axis floor is
the lower limit.

**Spectrum paint order reversed.** Reading #1 is the reference, so it is painted last and sits in
front; later readings recede behind it in order. Previously the last trace was drawn on top, which
made the whole chart read as amber. The front trace is solid and slightly heavier; everything behind
it is dashed or dotted so it reads through the gaps. Legend order is unchanged.

## 2.5.0

**One notation for standard deviation: SD.** The same concept was appearing four ways — `SD` in the
statistics table, `Std Dev` in the chart's stats block, `s` on the histogram's sigma ticks and `σ`
in the key. Everything now reads SD: ticks are `+1 SD` / `-2 SD`, the control-limit caption is
`Limits: mean ± 3 SD`, and the chart's stats block uses the same labels as the table (n, Mean, SD,
RSD, Max, Min, Range).

**Statistics table: `n` replaced by `Range`.** n is already in the section heading and was identical
in every row. Range (Max − Min) says something different about each quantity. The n column comes
back automatically if a quantity has missing values and its n genuinely differs.

**Calibration / application moved from a stray footer line into the metadata sub-line** under the
title, next to Operator and the measurement count — it is the same kind of information, and it was
easy to miss stranded at the bottom of the last page.

**Instrument line enlarged** to 19px now that the serial has its own line.

**Spectrum palette reworked**: opaque navy, crimson and amber — no green, no transparency.
Separation comes from hue plus dash pattern; traces are drawn solid → dashed → dotted so the ones
underneath show through the gaps.

**Spectrum legend labels by reading number** (`#1`, `#25`, `#50`), matching the results table.
`sequenceNumber` is `-1` in most files — an instrument placeholder for "not set" — and was being
printed literally as `#-1`.

## 2.4.0

**Element symbols and units keep their casing — a real bug.** The table header rule carried
`text-transform:uppercase`, which rendered `Sn (wt%)` as `SN (WT%)` and `μm` as `ΜM` — a capital
Mu, indistinguishable from an M. Headers are no longer case-transformed; they are larger and bolder
instead.

**Spectrum reverted to a direct overlay.** The 2.3.0 waterfall offset was worse: a shifted spectrum
is no longer a spectrum, and repeats exist to be compared peak-to-peak. Traces sit on top of each
other again and are separated by opacity — 0.55, 0.75, 0.95, lightest drawn first — with dash
patterns backing that up. The peak-accurate bucketing from 2.2.0 is kept.

**Instrument line moved into the header row**, centred between the company and customer blocks,
which were leaving that space empty. Two lines: model, then serial.

**Tables tightened** — row padding cut from 8px to 4px vertical.

**Keys trimmed further.** `±` is self-evidently the error, and `Mean`, `Min` and `Max` explain
themselves. Statistics now glosses only SD, RSD and n; the results table usually shows no key at all.

## 2.3.0

**Corrected the uncertainty wording.** The key claimed `±` was 1σ. The JSON only names those fields
`thicknessStatisticalError` / `compoundMassFractionsStatisticalErrors` and states no coverage
factor, and it cannot be derived: total spectrum counts imply 0.23% on pure Poisson while the
reported error is 5.7%, because it propagates through the FP model from net peak counts. It now
reads "statistical measurement uncertainty, as reported by the instrument". If Hitachi documents
the factor, put it back.

**Spectrum: traces are offset vertically (waterfall).** Repeats of one sample are near-identical,
so overlaying them exactly produced a single unreadable smear. Each trace is now lifted by a
constant slice of the plot height, with a thin white casing underneath so it stays legible where it
crosses another. Colours are fully opaque and further apart in hue — the old transparency was
blending three traces into one muddy colour exactly where they overlapped. Chart is larger
(520×300). The axis belongs to the lowest trace, and the note says so.

**Log scale removed.**

**Instrument line**: `Hitachi FT230 Coating Analyzer (SN 150086)` in the same face as the rest of
the report, sentence case, no letterspacing. Still centred, still always printed.

**Only the fixed header is centred** — the report title and its operator line are left-aligned
again, with the rest of the page.

**Tables**: faint internal vertical rules added, zebra striping on every table regardless of length,
and the statistics and composition tables gained `thead`/`tbody` so striping starts on the same row
everywhere (and their headers now repeat across printed pages).

**Eight tints**, spread far enough apart in hue to tell apart: Plain, Blue, Graphite, Teal, Green,
Amber, Violet, Rose. Slate is gone — it was indistinguishable from Blue and Graphite.

## 2.2.1

Explicit `.card[hidden]{display:none}` so the Columns card cannot leak a header row before files
are loaded, and the table's closing rule is scoped to `tbody` so it can never reach a header row.

## 2.2.0

**Spectrum chart: fixed a real drawing bug.** The trace was built by taking every Nth channel to
fit 2048 channels into ~376 pixels. XRF peaks are 3–5 channels wide, so the sampling grid missed
peak apexes: on the reference file the main peak drew at 4379 counts instead of 5054 (13% short),
273 of 410 plotted points under-reported, and identical repeats drew visibly different shapes
depending on where the grid landed. Buckets are now aggregated by **maximum**, so peak heights are
exact and repeats overlay cleanly. Verified: rendered peak now matches the true peak to 0.00%.

**Spectrum: log scale option and a trimmed energy axis.** Counts span ~340× between the main peak
and a typical channel, so a linear axis flattens every minor element into the baseline — a `log
scale` checkbox under Spectrum chart spans four decades below the peak. The x-axis now stops at
the last energy carrying signal (40 keV on the reference file, was 51.2) instead of plotting a
third of a chart of empty tail.

**Tables redesigned, with four tints.** Vertical borders removed — horizontal rules only, roomier
cells, small-caps headers. Zebra striping applies only to tables over 8 rows, where it earns its
keep. Tint picker: Plain (fully white), Blue, Graphite, Slate. Every tint drives the same four
colour slots, so the structure never changes.

**Instrument header centred**, letterspaced, same size as the report title, which is now centred
with it. Reads as a letterhead rather than a callout.

**Sidebar rebuilt as collapsible cards.** Seven cards, each collapsing to a header row that carries
a tag saying what it holds (`landscape · slate`, `3 loaded`, `not set`), so a closed card still
tells you its state. Open/closed is remembered. Paired controls sit side by side, and logos are a
thumbnail beside their button instead of a full-width block.

**Report text fields reordered to match the printed page** — title, operator, notes, then
calibration name, each labelled with where it lands. Previously the calibration override sat
second in the panel but printed last on the page.

## 2.1.0

**Spectrum traces are told apart by line style, not just colour.** Overlaid spectra of one sample
sit almost exactly on top of each other, so colour alone could not separate them. Each trace now
carries its own colour, dash pattern and opacity — solid blue at 0.70, dashed red at 0.80, dotted
green at 0.95, and three more for wider overlays. The legend swatch draws the actual line, dashes
included, instead of a colour chip.

**Instrument header reads as a header.** `Measured with Hitachi FT230 SN 150086` — plain ink,
underlined, no coloured panel, so it no longer competes with the report title.

**Abbreviation keys drop the obvious units.** `wt%`, `μm`, `mm` and `%` are never glossed anywhere.
`ppm` and the rarer thickness units still are.

**Version is visible** in the sidebar heading, the toolbar and the browser tab title. All three are
hidden when printing.

## 2.0.0

**Instrument header.** Every report now opens with the device that produced it —
`FT230 · S/N 150086`, read from `metadata.hwSerialNumbers` — set above the report title.
Always printed; not an option. If a report spans two instruments, both are listed rather
than the first one standing in for both.

**Saved company / customer profiles.** Both are now named profiles selected from a dropdown,
with update / save-as-new / rename / delete. The single company and customer from 1.x migrate
into the first profile of each list. Logos are downscaled on upload, and a full browser
storage now reports an error instead of dropping settings silently.

**Abbreviation keys.** Each section ends with a small right-aligned key, built from what that
section actually rendered — a report with no SPC chart does not explain UCL/LCL. Always printed.

**Uncertainty moved into the value cell.** `9.22 ±0.14` in one column instead of a value column
plus a `±` column. On a 50-measurement alloy report this drops the results table from 14 columns
to 10. Results are left-aligned.

**Page orientation.** Portrait / landscape, driving both the on-screen page width and the real
`@page` size, so the preview matches the PDF. In landscape the transposed composition layout
fits more measurements per table.

**Table width presets reduced to two** — Full width and Fit content. `Balanced` is gone along
with the column pressure that justified it.

## 1.x

Baseline, tagged `v1`. Single company and customer, `±` in its own column, three width presets,
no instrument header, portrait only.
