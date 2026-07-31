# Changelog

The version is set in `APP_VERSION` at the top of the script in `XRF Report Generator.html` and
shown as a badge in the sidebar — never in the printed PDF.

Bump it **once per released set of changes**, not once per edit. A round of tweaks within a single
working session is one version.

- **MAJOR** — the printed report changes shape: sections move, columns restructure. A report printed
  under a new major does not look like one printed under the old major, so an archived PDF can be
  traced back to the version that made it.
- **MINOR** — new capability that leaves existing reports looking the same.
- **PATCH** — a fix or a tweak.

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
