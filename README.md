# CAnD3 – 2016 Census PUMF: Race & Income Mini Study (Stata)

This repository contains **Stata** code and instructions to replicate a race–income mini study using the **2016 Canadian Census PUMF (Individuals)**. The workflow follows a capture‑free style (rename → label → recode), explicit **pweights** (`weight`), and factor variables (`i.` for categorical, `c.` for continuous).

## Contents

* `code/CanD3-Zhang.do` — Main Stata script for this mini study.
* `data/pumf-98M0001-E-2016-individuals_F1.csv` — Raw PUMF (Individuals) file (place here locally if not using a network path).
* `docs/pumf-98M0001-E-2016-individuals.pdf` — PUMF documentation (optional for reference).
* `logs/CanD3-Zhang.log` — Stata log (if using local paths).
* `figures/` — (optional) where plots can be exported.
* `models/` — (optional) where model output can be saved as `.txt`.

## How to Run

### Recommended: local repo paths

Edit the import/log lines to use this repo’s folders, then run the do‑file.

```stata
cd "path/to/repo"
log using "logs/CanD3-Zhang.log", replace
import delimited using "data/pumf-98M0001-E-2016-individuals_F1.csv", varnames(1) clear

rename *, lower
keep weight dpgrsum totinc totinc_at sex agegrp ssgrad pr
```

Then:

```stata
do code/CanD3-Zhang.do
```

## Variables Used (kept from the PUMF)

`weight` (person weight), `dpgrsum` (visible minority/White), `totinc`, `totinc_at`, `sex`, `agegrp`, `ssgrad`, `pr`.

## Race Recode (dpgrsum → newrace)

The script creates **`newrace`** with the following mapping (2016 PUMF codes):

* **1 → White**
* **4 → Black**
* **{2, 3, 5, 8, 10, 11} → Asian** (South Asian, Chinese, Filipino, Southeast Asian, Korean, Japanese)
* **{6, 7, 9, 12, 13, 14, 15} → Other** (Latin American, Arab, West Asian, VM n.i.e., Multiple VMs n.i.e., White+VM, Aboriginal peoples)
* **88 → missing** (Not available)

Labels:

```stata
label define newrace 1 "White" 2 "Black" 3 "Asian" 4 "Other"
label values newrace newrace
label var newrace "Race (DPGRSUM recode: White/Black/Asian/Other)"
```

## Cleaning & Transforms

Convert PUMF special codes to missing **before** logs:

```stata
replace totinc    = . if inlist(totinc,    88888888, 99999999)
replace totinc_at = . if inlist(totinc_at, 88888888, 99999999)

gen double lntotinc = cond(totinc > 0, ln(totinc + 1), .)
label var lntotinc "ln(Total income + 1)"
```

## Weighted Descriptives

* Race composition:

```stata
proportion newrace [pw=weight]
```

* Design‑based means by race (recommended):

```stata
mean totinc [pw=weight], over(newrace)

svyset _n [pw=weight]
svy: mean totinc, over(newrace)
```

> Note: `tabstat` does **not** accept `pweights`. Use `mean`/`svy:` for design‑based summaries.

## Weighted OLS & Postestimation

Main model (factor notation + robust SEs):

```stata
regress lntotinc i.newrace i.sex i.agegrp i.ssgrad i.pr [pw=weight], vce(robust)
```

Adjusted means and plot:

```stata
margins newrace
marginsplot
```

(Optional) Export the plot:

```stata
graph export "figures/margins_newrace.png", replace width(2000)
```

## Requirements

* **Stata 17+**.
* No R packages are required.

## Reproducibility & Style

* Capture‑free code; clear **rename → label → recode** blocks.
* Factor variables (`i.`/`c.`), interactions via `##` if needed.
* All analyses use **[pw=weight]** and appropriate VCE (`vce(robust)` in the model).
* Case discipline after `rename *, lower`.

## Citation

Statistics Canada. *2016 Census of Population Public Use Microdata File (PUMF): Individuals File.* Refer to `docs/pumf-98M0001-E-2016-individuals.pdf` for variable definitions and coding.
