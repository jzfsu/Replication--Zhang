# CAnD3 – 2016 Census PUMF: Stratified Earnings  
*A Mini Study of Racial Income Disparities (Stata)*

This repository provides a reproducibility exercise using the **2016 Canadian Census Public Use Microdata File (PUMF: Individuals)**.  
The goal is to replicate a **race–income mini study** in Stata, applying good practices in data cleaning, survey weighting, and regression modeling.

---

## Repository Structure

- `code/CanD3-Zhang.do` — Main Stata script (updated, capture-free style).  
- `data/` — Place the Census PUMF CSV here (not distributed).  
- `docs/` — Optional documentation (PUMF codebook PDF).  
- `logs/` — Stata log files.  
- `figures/` — (optional) plots exported by the analysis.  
- `models/` — (optional) regression results and text outputs.  

---

## How to Reproduce the Analysis

### 1. Obtain the Data
The raw **2016 Census PUMF (Individuals)** is not distributed here due to Statistics Canada licensing restrictions.  
Follow [`DATA_ACCESS.md`](DATA_ACCESS.md) for instructions to obtain the dataset.  

- Save the CSV into `data/`.  
- (Optional) Save the codebook PDF into `docs/`.  

---

### 2. Prepare the Environment
- Install **Stata 17+**.  
- Clone this repository to your local machine.  
- Ensure the folder structure above is intact.  
- Confirm that logs, figures, and model outputs will be written to their designated folders.  

---

### 3. Run the Script
Open Stata and set your working directory to the repository root.  
Then run the script in `code/CanD3-Zhang.do`.  

The updated `.do` file:  
- Uses a **capture-free style** (rename → label → recode).  
- Sets all variable names to lowercase.  
- Retains only the subset of variables needed downstream.  
- Destrings variables if necessary.  
- Applies consistent labels for race, sex, age, education, and province.  
- Cleans income variables by converting special PUMF codes to missing.  
- Generates a log income variable for analysis.  

---

### 4. Analysis Steps
The analysis proceeds in the following stages:

1. **Race recoding**  
   - PUMF `dpgrsum` is recoded into a simplified `newrace` variable: White, Black, Asian, Other.  

2. **Other recodes**  
   - Sex, age groups, secondary school graduation, and province are relabeled.  
   - “Not available” codes are consistently treated as missing.  

3. **Income cleaning and transformation**  
   - Special PUMF income codes (e.g., 88,888,888 and 99,999,999) are marked as missing.  
   - Log income (`lntotinc`) is generated for regression analysis.  

4. **Weighted descriptives**  
   - Race composition is estimated using person-level weights.  
   - Design-based means of income are calculated by race.  

5. **Regression modeling**  
   - Weighted OLS regression of log income on race, sex, age group, education, and province.  
   - Robust standard errors are applied.  

6. **Postestimation**  
   - Adjusted means of income are computed by race.  
   - Results are visualized using margins plots, including breakdowns by sex.  

---

## Reproducibility Standards
- **Weights**: All analyses use person-level weights (`[pw=weight]`).  
- **Design-based methods**: Means and regression models account for survey design.  
- **Clean style**: Explicit recodes and labeling; no `capture drop`.  
- **Transparency**: Logs record the workflow from start to finish.  
- **Outputs**: Figures and models are saved in separate folders for clarity.  

---

## Requirements
- Stata 17 or newer.  
- Access to the 2016 Census PUMF (Individuals).  

---

## Citation
Statistics Canada. 2019. *Census of Population, 2016 [Canada] Public Use Microdata File (PUMF): Individuals File.*  
Statistics Canada [producer and distributor], accessed September 10, 2021.  
ID: pumf-98M0001-E-2016-individuals.  

---

## License
Use of the 2016 Census PUMF must comply with **Statistics Canada licensing terms**.  
- Do **not** share or commit raw microdata.  
- Only derived, de-identified outputs may be shared in compliance with license and institutional policies.  

