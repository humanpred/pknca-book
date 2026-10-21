# Contributing to the PKNCA Documentation Book

This guide explains how to update the book when a new version of PKNCA is released.
Anyone — maintainers, users, or contributors — can follow this workflow.

**Book repo:** https://github.com/humanpred/pknca-book  
**Rendered book:** https://humanpred.github.io/pknca-book/  
**Package repo:** https://github.com/humanpred/pknca  
**pkgdown reference:** https://humanpred.github.io/pknca/reference/index.html  
**Upstream issue:** https://github.com/humanpred/pknca/issues/541

---

## Prerequisites

- R (≥ 4.2)
- [Quarto](https://quarto.org/docs/get-started/) (≥ 1.4)
- The PKNCA package at the version you want to document:

```r
# From GitHub (pre-CRAN release)
remotes::install_github("humanpred/pknca")

# Or from CRAN
install.packages("PKNCA")

# Confirm
packageVersion("PKNCA")
```

- Clone the book:

```bash
git clone https://github.com/humanpred/pknca-book
cd pknca-book
```

---

## Update workflow

### Step 1 — Find what changed

Read the PKNCA changelog for the new version:

```r
news(package = "PKNCA")
```

Or read it on GitHub: https://github.com/humanpred/pknca/blob/main/NEWS.md

For each item, classify it:

| Type | Example |
|---|---|
| New NCA parameter | new `add.interval.col()` entry |
| New option | new key in `PKNCA.options()` |
| New function | e.g. `be_assess()` in 0.13.0 |
| Changed behavior | existing function works differently |
| Bug fix | may invalidate an existing example |

Cross-check the parameter count and options list:

```r
library(PKNCA)
length(get.interval.cols())   # total registered parameters — compare to previous version
names(get.interval.cols())    # full list
PKNCA.options()               # all options with current defaults
```

---

### Step 2 — Write and test examples first

Before editing any `.qmd` file, write a minimal working example in R and confirm it runs without error.

Most pages share this dataset setup — reuse it:

```r
library(PKNCA)
library(dplyr)

d_one  <- as.data.frame(Theoph) |>
  filter(Subject == "1") |>
  rename(time = Time, subject = Subject)

d_dose <- data.frame(subject = "1",
                     dose    = Theoph$Dose[1] * Theoph$Wt[1],
                     time    = 0)

o_conc <- PKNCAconc(d_one,  conc ~ time | subject)
o_dose <- PKNCAdose(d_dose, dose ~ time | subject, route = "extravascular")

# Request the new parameter
iv    <- data.frame(start = 0, end = Inf, new_param = TRUE)
o_nca <- pk.nca(PKNCAdata(o_conc, o_dose, intervals = iv))

as.data.frame(o_nca) |>
  filter(PPTESTCD == "new_param") |>
  select(PPTESTCD, PPORRES)
```

**Known gotchas:**

- **Tobit half-life** — set `lloq` on `PKNCAconc()`; in PKNCA ≥ 0.13.0 it is passed through to `pk.calc.half.life()` automatically, so the full `pk.nca()` pipeline works with `options = list(hl_method = "tobit")`. (Older versions required calling `pk.calc.half.life()` directly.)
- **Sparse parameters** — use the standard names (`auclast`, `aucinf.obs`, …); with `PKNCAconc(sparse = TRUE)` their `_se` and `_df` companions are calculated with them. The `sparse_*` names are deprecated.
- **Tobit with imputation** — until the LLOQ is carried onto imputed rows (pknca PR 678), combining `hl_method = "tobit"` with `impute = "start_conc0"` errors; examples show Tobit without imputation.
- **`normalize()`** — on a data frame it replaces the original parameters (does not append); on a `PKNCAresults` object it appends the normalized rows.
- **New option defaults** — always verify with `PKNCA.options("option_name")` before documenting the default.

---

### Step 3 — Identify which pages to update

| Changed item | Page(s) to update |
|---|---|
| New NCA parameter | `user/intervals.qmd` (catalog + count) + thematic page |
| New option | `user/workflow.qmd` (options table) |
| New post-processing function | `user/postprocessing.qmd` |
| New sparse parameter | `user/sparse.qmd` |
| New excretion parameter | `user/urine-excretion.qmd` |
| New units feature | `user/units.qmd` |
| New imputation method | `user/imputation.qmd` |
| New custom-parameter API | `dev/architecture.qmd` |
| Bug fix changing output | Every page whose example output changes |
| New version | `index.qmd` (What's new section) + `README.md` |

---

### Step 4 — Edit the documentation

- Add new sections or update existing ones in the relevant `.qmd` files.
- Tag new features with `(≥ X.Y.Z)` so readers know when they were introduced.
- Keep the shared dataset pattern consistent across pages.
- If the new version introduces new functions, add them to the `:::callout-note` pkgdown reference footer at the bottom of the relevant chapter(s). The link pattern is `[function()](https://humanpred.github.io/pknca/reference/function.html)`.

---

### Step 5 — Render and verify

```bash
quarto render
```

- Watch for **red error output** in the console — with `execute: error: false`, a chunk that errors stops the render.
- Fix the code, not the error suppression.

Before pushing, run the numeric self-check, which verifies that key documented examples still produce correct values:

```bash
Rscript validate.R
```

It prints PASS/FAIL per check and exits non-zero on any failure. CI runs it too (`.github/workflows/book.yml`), so a failing check will fail the build.

Then open the book and spot-check every updated page:

```bash
open _book/index.html
```

Confirm:
- Output matches expected values
- No unexpected `NA` results
- Warnings are expected (e.g. half-life not calculable for some subjects)

---

### Step 6 — Commit and push

```bash
git add -u
git commit -m "update docs for PKNCA vX.Y.Z"
git push
```

The `_book/` directory is in `.gitignore` — only source `.qmd` files are committed. Pushing to `main` triggers the GitHub Actions workflow (`.github/workflows/book.yml`), which renders the book and deploys it to https://humanpred.github.io/pknca-book/. If the site ever moves (e.g. to a custom domain), update `site-url` in `_quarto.yml` accordingly.

---

### Step 7 — Notify the maintainer

Post a comment on the upstream issue or open a new one:

**Issue:** https://github.com/humanpred/pknca/issues/541

Template:
> Updated the documentation book for PKNCA vX.Y.Z — covers [list new features].
> Book: https://humanpred.github.io/pknca-book/

---

## Quick checklist

```
[ ] Read NEWS.md / changelog for the new version
[ ] Install new version and confirm with packageVersion()
[ ] Compare parameter count: length(get.interval.cols())
[ ] Check new options: PKNCA.options()
[ ] Write and test each new example in R before editing .qmd files
[ ] Update index.qmd "What's new" section
[ ] Update README.md version highlights
[ ] Update user/workflow.qmd options table (if options changed)
[ ] Update user/intervals.qmd parameter count and catalog
[ ] Update thematic pages (auc-methods, halflife, sparse, urine, etc.)
[ ] Verify all packages are listed in .github/workflows/book.yml — audit at once with:
    grep -rh "^library(" user/ dev/ index.qmd | sort -u
[ ] quarto render — zero errors in console
[ ] Rscript validate.R — all checks PASS (CI runs this too and fails otherwise)
[ ] Spot-check _book/ in browser
[ ] Add new functions to chapter pkgdown reference footers if needed
[ ] git commit and push (CI auto-deploys to humanpred.github.io/pknca-book/)
[ ] Comment on humanpred/pknca#541
```

---

## Book structure

```
_quarto.yml          # book config (title, author, chapters)
index.qmd            # introduction + What's new
user/                # 25 User Guide chapters (order in _quarto.yml)
  workflow.qmd                  # PKNCAconc → PKNCAdose → PKNCAdata → pk.nca
  intervals.qmd                 # Interval data frame
  auto-intervals.qmd            # Interval builder vs legacy
  auc-methods.qmd               # auclast/aucall/aucinf
  halflife.qmd                  # Point selection controls
  extravascular.qmd             # BLQ handling
  intravascular.qmd             # IV bolus
  multiple-dose.qmd             # deg.fluc
  superposition.qmd             # Predicting multiple-dose profiles from single-dose data
  tss.qmd                       # pk.tss.monoexponential
  urine-excretion.qmd           # ae
  secondary-parameters.qmd      # interval_id/_ref
  sparse.qmd                    # Serial/batch designs
  sparse-uncertainty.qmd        # Yuan vs delta method (sparse_lambda_z_se)
  bioequivalence.qmd            # be_assess()
  data-quality.qmd              # pknca_missing_samples()
  postprocessing.qmd            # exclude()
  units.qmd                     # pknca_units_table()
  datetime.qmd                  # POSIXct/Date times
  imputation.qmd                # start_conc0
  dose-aware-interpolation.qmd  # interp.extrap.conc.dose
  custom-parameters.qmd         # add.interval.col()
  utilities.qmd                 # clean.conc.*
  regulatory.qmd                # CDISC output
  validation.qmd                # testthat suite
dev/                 # Developer Reference
  architecture.qmd   # class hierarchy; interval column system; formalsmap
  function-deps.qmd  # parameter dependency graph
```
