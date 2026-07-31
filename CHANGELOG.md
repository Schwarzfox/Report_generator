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
