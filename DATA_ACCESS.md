# DATA_ACCESS.md — 2016 Census PUMF (Individuals)

## Data Accessibility Statement
The **2016 Canadian Census Public Use Microdata File (PUMF)** used in this project is **not distributed** in this repository due to Statistics Canada licensing restrictions.

### How to obtain the data (CAnD3 users)
1. Log in to the **CAnD3 Canvas** site.  
2. Open **RRWM | Data Activity**.  
3. Download the dataset provided by the instructor.  
4. Save the file into a local folder named `data/` in this repository.

> If your download has a different filename, rename it or update the paths in your code.

---

## Required Files & Paths
- **CSV microdata** → `data/pumf-98M0001-E-2016-individuals_F1.csv`  
- *(Optional)* **Codebook PDF** → `docs/pumf-98M0001-E-2016-individuals.pdf`

Create folders if needed: `data/` and `docs/`.

---

## Stata Quick Start
```stata
version 18.0
import delimited using "data/pumf-98M0001-E-2016-individuals_F1.csv", varnames(1) clear
rename *, lower
keep weight dpgrsum totinc totinc_at sex agegrp ssgrad pr

* Handle special-missing income codes before any summaries/logs
replace totinc    = . if inlist(totinc,    88888888, 99999999)
replace totinc_at = . if inlist(totinc_at, 88888888, 99999999)

* Race recode (dpgrsum → newrace)
gen byte newrace = .
replace newrace = 1 if dpgrsum == 1
replace newrace = 2 if dpgrsum == 4
replace newrace = 3 if inlist(dpgrsum,2,3,5,8,10,11)
replace newrace = 4 if inlist(dpgrsum,6,7,9,12,13,14,15)
label define newrace 1 "White" 2 "Black" 3 "Asian" 4 "Other"
label values newrace newrace

* Weights
svyset _n [pw=weight]


Citation

Statistics Canada. 2019. Census of Population, 2016 [Canada] Public Use Microdata File (PUMF): Individuals File. Statistics Canada [producer and distributor], accessed September 10, 2021. ID: pumf-98M0001-E-2016-individuals.

License

Use and redistribution must comply with Statistics Canada licensing terms for the 2016 Census PUMF.

Do not commit or distribute the raw PUMF in this repository.

Share only derived, de-identified outputs that conform to the license and course/institutional policies.






