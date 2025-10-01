# DATA_ACCESS.md — 2016 Census PUMF (Individuals)

This project uses the **2016 Canadian Census Public Use Microdata File (PUMF): Individuals** to replicate a race–income mini study in **Stata**.

## What to download
- **Microdata (CSV)**: `pumf-98M0001-E-2016-individuals_F1.csv`
- **Documentation (PDF)**: `pumf-98M0001-E-2016-individuals.pdf` (variable list, codes, and notes)

> These files are available from Statistics Canada’s website for the 2016 Census PUMF (Individuals). You may be asked to accept licence terms before download.

## Where to place files in this repo
- Put the CSV in: `data/pumf-98M0001-E-2016-individuals_F1.csv`
- Put the PDF in: `docs/pumf-98M0001-E-2016-individuals.pdf` (optional but recommended)

Create folders if they don’t exist: `data/` and `docs/`.

## Minimal Stata import (local paths)
```stata
cd "path/to/repo"
log using "logs/CanD3-Zhang.log", replace

import delimited using "data/pumf-98M0001-E-2016-individuals_F1.csv", varnames(1) clear
rename *, lower
keep weight dpgrsum totinc totinc_at sex agegrp ssgrad pr
```

## Key variables used
- `weight` — person weight (use as pweight: `[pw=weight]`)
- `dpgrsum` — visible minority / White classification (2016 coding)
- `totinc`, `totinc_at` — total and after‑tax income (watch for special‑missing codes)
- `sex`, `agegrp`, `ssgrad`, `pr` — controls

## Special‑missing codes (income)
Before any logs/summaries:
```stata
replace totinc    = . if inlist(totinc,    88888888, 99999999)
replace totinc_at = . if inlist(totinc_at, 88888888, 99999999)
```

## Race recode used in this project (dpgrsum → newrace)
```stata
gen newrace = .
replace newrace = 1 if dpgrsum == 1
replace newrace = 2 if dpgrsum == 4
replace newrace = 3 if inlist(dpgrsum, 2,3,5,8,10,11)
replace newrace = 4 if inlist(dpgrsum, 6,7,9,12,13,14,15)
replace newrace = . if dpgrsum == 88

label define newrace 1 "White" 2 "Black" 3 "Asian" 4 "Other"
label values newrace newrace
```

## Licence & citation
- Follow Statistics Canada’s licence terms for the Census PUMF.
- Cite as: *Statistics Canada. 2016 Census of Population Public Use Microdata File (PUMF): Individuals File.*
```

