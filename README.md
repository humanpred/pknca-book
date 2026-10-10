# PKNCA Documentation Book

[![Render and deploy book](https://github.com/humanpred/pknca-book/actions/workflows/book.yml/badge.svg)](https://github.com/humanpred/pknca-book/actions/workflows/book.yml)

A comprehensive **Quarto book** documenting the [PKNCA](https://github.com/humanpred/pknca) R package (≥ 0.13.0) with runnable examples throughout. Every parameter, option, and user-facing function is covered and verified against live PKNCA output; the underlying `pk.calc.*` calculation functions are indexed in the Developer Reference.

**Live book:** https://humanpred.github.io/pknca-book/

## Build locally

```r
# Install PKNCA 0.13.0 or later
remotes::install_github("humanpred/pknca")
```

```bash
git clone https://github.com/humanpred/pknca-book
cd pknca-book
quarto render
open _book/index.html
```

## Contents

### User Guide (25 chapters)

| Chapter | Topics |
|---|---|
| [Workflow](user/workflow.qmd) | PKNCAconc → PKNCAdose → PKNCAdata → pk.nca; default intervals; all options with defaults; update() |
| [Interval selection & parameter catalog](user/intervals.qmd) | Interval data frame; pknca_interval_table(); presets; interval_add_*(); impute columns; validation; the parameter catalog, rendered live |
| [Automatic intervals & dosing regimens](user/auto-intervals.qmd) | Interval builder vs legacy; find.dose.regimen(); find.tau(); nominal times; regimen changes; classed warnings |
| [AUC types & integration methods](user/auc-methods.qmd) | auclast/aucall/aucinf; dose-aware partial AUCs; lin up/log down vs linear vs lin-log; AUMC; aumcint; aumciv |
| [Half-life & terminal phase](user/halflife.qmd) | Point selection controls; Tobit regression; exclusion rules; get_halflife_fit()/get_halflife_curve(); lambda.z.corrxy |
| [Extravascular (oral/SC)](user/extravascular.qmd) | BLQ handling; imputation; tmin; lag time; f.obs pointer; cl/vz/vss/mrt families |
| [Intravascular (IV)](user/intravascular.qmd) | IV bolus; C0 back-extrapolation; auciv*; IV infusion; cl.iv/vz.iv; MRT correction |
| [Multiple-dose / steady-state](user/multiple-dose.qmd) | deg.fluc; swing; PTR; Cav; tau detection; mrt.md/vss.md; accumulation |
| [Superposition](user/superposition.qmd) | Predicting multiple-dose profiles from single-dose data |
| [Time to steady state](user/tss.qmd) | pk.tss.monoexponential; pk.tss.stepwise.linear; pk.tss |
| [Urine excretion](user/urine-excretion.qmd) | ae; fe; renal clearance in one run; volpk; ermax/ertmax/ertlst; erint/erlst |
| [Secondary parameters](user/secondary-parameters.qmd) | interval_id/_ref; interval_add_secondary(); bioavailability (f.*); ratio.*; renal clearance; composed units |
| [Sparse PK sampling](user/sparse.qmd) | Serial/batch designs; auclast etc. with _se/_df; start_conc0; BLQ rule; summary with SE; sparse_bootstrap() |
| [Sparse AUC to infinity & its uncertainty](user/sparse-uncertainty.qmd) | Yuan vs delta method (sparse_lambda_z_se); IV bolus C0; recommendations; prior work |
| [Bioequivalence](user/bioequivalence.qmd) | be_assess(); ABE/ABEL/RSABE/NTID/HVNTID/descriptive; crossover, replicate, parallel; sparse bootstrap route |
| [Data quality checks](user/data-quality.qmd) | pknca_missing_samples(); exclude_nca_tmax_coverage(); LLOQ and input validation |
| [Post-processing](user/postprocessing.qmd) | exclude(); exclusion rules; normalize(); .dn parameters; summary() options; PKNCA.set.summary |
| [Units](user/units.qmd) | pknca_units_table(); automatic units; preferred units; timeu and tau; conversions |
| [Date-time data & time references](user/datetime.qmd) | POSIXct/Date times; time references; date-time intervals; PPSTINT/PPENINT/PPTPTREF/PPRFTDTC |
| [Imputation](user/imputation.qmd) | start_conc0; start_predose; start_predose_conc0; start_cmin; end_conc_drop; chaining order; custom methods |
| [Dose-aware interpolation](user/dose-aware-interpolation.qmd) | interp.extrap.conc.dose; dose-aware aucint rules |
| [Custom parameters](user/custom-parameters.qmd) | add.interval.col(); FUN_sparse; pknca_ref(); registering new NCA parameters |
| [Utility functions](user/utilities.qmd) | geomean; geocv; clean.conc.*; assert_*; get.parameter.deps(); route helpers |
| [Regulatory & CDISC](user/regulatory.qmd) | CDISC output; pknca_cdisc_codes(); PPANMETH; PPGRPID; exclusion tracking; summary() for CSR tables |
| [Validation & testing](user/validation.qmd) | testthat suite; version pinning; manual AUClast spot-check; PPANMETH |

### Developer Reference (2 chapters)

| Chapter | Topics |
|---|---|
| [Architecture](dev/architecture.qmd) | Internal structure of PKNCA |
| [Function dependencies](dev/function-deps.qmd) | Parameter dependency graph |

## What's new in PKNCA 0.13.0

See the book's [introduction](index.qmd) for the full list.  Highlights: automatic intervals rebuilt from the dosing regimen (breaking); secondary parameters (bioavailability `f.obs` etc., ratios, renal clearance) in one run; bioequivalence (`be_assess()`); sparse NCA with standard parameter names, the delta-method AUC to infinity, and `sparse_bootstrap()`; dose-aware partial AUCs; data quality checks; date-time data and CDISC time references.

## Related

- PKNCA source: https://github.com/humanpred/pknca
- pkgdown reference: https://humanpred.github.io/pknca/reference/index.html
- CRAN: https://cran.r-project.org/package=PKNCA
- Discussion: [humanpred/pknca#541](https://github.com/humanpred/pknca/issues/541)
