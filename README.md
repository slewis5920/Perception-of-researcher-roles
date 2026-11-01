# Perception-of-researcher-roles

Author: SCL
Main analysis file: DISS CLEANING AND ANALYSIS (7).Rmd
Input data file: Diss_full.csv
Overview
This R Markdown document contains the data preparation, descriptive analyses, primary models, robustness checks, reporting tables, measurement analyses, and exploratory repeated-measures ANOVAs for the dissertation study.

The analyses focus on evaluations of researchers in two roles:
- Investigator
- Inspector
The study examines responses across three levels of questionable research practice (QRP) severity:
- Minor
- Moderate
- Major
The main outcomes are:
1. Integrity: composite rating based on the integrity-related items.
2. Likeability: composite rating based on the likeability-related items.
3. Trueness: a single-item rating of the perceived trueness of the finding, measured for Investigator vignettes.
The file also analyzes researcher-attribute choices and includes exploratory factor and measurement-invariance analyses.
Files and folder setup
Place the following files in the same project directory:
Dissertation_Project/
├── DISS CLEANING AND ANALYSIS (7)(4).Rmd
└── Diss_full.csv
The R Markdown file loads the data with:
data_path = here::here("Diss_full.csv")
diss.full = read.csv(data_path)
Using an RStudio Project (.Rproj) in this directory is recommended because the code uses the here package to locate the CSV file.

If the CSV is stored somewhere else, update data_path to point to its location.
Software and packages
Run the analysis in RStudio using a recent version of R. The document uses the following packages:
- pwr
- tinytex
- sjstats
- dplyr
- tibble
- purrr
- tidyverse
- janitor
- here
- psych
- car
- effectsize
- broom
- knitr
- kableExtra
- lattice
- nlme
- lubridate
- lme4
- lmerTest
- emmeans
- lavaan
- semTools
- afex
Install any packages that are not already installed:
packages = c(
  "pwr", "tinytex", "sjstats", "dplyr", "tibble", "purrr",
  "tidyverse", "janitor", "here", "psych", "car", "effectsize",
  "broom", "knitr", "kableExtra", "lattice", "nlme",
  "lubridate", "lme4", "lmerTest", "emmeans", "lavaan",
  "semTools", "afex"
)

missing_packages = packages[
  !packages %in% rownames(installed.packages())
]

if (length(missing_packages) > 0) {
  install.packages(missing_packages)
}
The ANOVA section uses afex::aov_ez(). The EFA/CFA and measurement-invariance section uses lavaan and semTools.
Running the analysis
1. Open the RStudio Project containing the RMD and Diss_full.csv.
2. Open the RMD file.
3. Install any missing packages.
4. Confirm that Diss_full.csv is in the project directory and that the filename matches exactly.
5. Knit the document, or run chunks in order from top to bottom.
Run chunks in order. Later sections rely on objects created earlier in the document. Running a later model or table chunk by itself may produce an object-not-found error if the setup and data-preparation chunks have not been run in the current R session.

The document header is configured to produce HTML, PDF, and Word output. PDF output may require a working LaTeX installation; tinytex is loaded in the document.
Analysis workflow
1. Load and clean the data
The document:
- Loads Diss_full.csv.
- Converts variable names to lowercase.
- Standardizes character encoding.
- Renames consent variables to a common consent name.
- Parses the recorded date.
- Applies the date cutoff of January 27, 2026.
- Retains participants who consented.
- Excludes participants with study durations under 420 seconds (7 minutes).
- Excludes participants under age 18.
- Creates checks for response variability and attention-check performance.
The main data objects created during this stage include:
- diss.full: original imported data.
- filtered_by_date: records after the date filter.
- filtered_by_consent: records after the consent filter.
- filtered_by_duration: records after the duration filter.
- filtered_by_age: records after the age filter.
- filtered_by_variability: records after the response-variability filter.
- filtered_by_attention: records after the attention-check filter.
Important cleaning note: In the current RMD, the variability and attention-check filtered datasets are created, but the later analysis datasets are built from filtered_by_age. The reported final_count is also calculated from filtered_by_age. Therefore, the variability and attention-check filters are not currently carried forward into the main analysis dataset. The comments describe these checks, but the code's actual analysis flow should be checked against the preregistration before treating those criteria as applied exclusions.
2. Demographic descriptives
The demographics section summarizes:
- Age: unique values, mean, standard deviation, and range.
- Consent responses.
- Gender identity.
- Ethnicity.
- Sex orientation.
- Race.
- Political identification.
- Religion.
- Open-ended responses for demographic categories.
- Counts and percentages in combined tables.
The main data object is filtered_by_age.
3. Researcher-attribute choice analyses
The attribute section reshapes the attribute-choice items from wide to long format and identifies:
- QRP severity.
- Attribute.
- Researcher selected (Investigator or Inspector).
- Whether the attribute choice corresponds to the researcher.
The models use a binary outcome (chosen) and include researcher role, severity, and attribute. Attribute is sum coded, and researcher role is effect coded.

Main objects include:
- attr_dat
- attr_long
- attr_long2
- attr_model_dat
The section includes model-building analyses, an overall attribute test, pairwise comparisons, and reporting tables.
4. Build the vignette-level analysis dataset
The Integrity and Likeability analyses use a long-format dataset with one row per participant, vignette, and researcher role.

The main dataset is:
- tl_rows: extracted vignette-level item responses.
- tl_dat: composite outcomes and analysis variables.
tl_dat contains the Integrity and Likeability composites, role, severity, participant ID, and vignette ID, along with the raw item responses used to construct the composites.
5. Reliability and validation checks
The document evaluates:
- Integrity internal consistency by role and severity.
- Likeability internal consistency.
- The correlation between the Integrity and Likeability composites.
6. Primary multilevel models
The primary Integrity and Likeability analyses use multilevel models (lmer) with:
- Researcher Role.
- QRP Severity.
- Role × Severity interaction.
- Participant-level random effects.
- A vignette-level random intercept when supported by the random-effects checks.
The code evaluates random-effects structures, checks for singular fits, and uses a consistent random-effects structure when comparing the step-up fixed-effects models.

The model sequences are:
Outcome	Model sequence
Integrity	Null; Role; Severity; Role + Severity; Role × Severity
Likeability	Null; Role; Severity; Role + Severity; Role × Severity


The final interaction models are T4 for Integrity and L4 for Likeability.

The models are fitted using maximum likelihood (REML = FALSE) for model comparisons. The code also reports fixed effects, model fit, planned role comparisons at each severity, Bonferroni-adjusted comparisons, and effect-size estimates.
7. Severity and role coding
The current code uses:
Predictor	Coding
QRP Severity: Minor	0
QRP Severity: Moderate	1
QRP Severity: Major	2
Researcher Role: Investigator	-1
Researcher Role: Inspector	1


Severity is treated as a continuous numeric predictor in the primary multilevel models. Thus, its coefficient represents the expected change in the outcome for a one-unit increase in severity.

The categorical labels are used for descriptive displays, estimated marginal means at the three observed severity levels, and exploratory ANOVAs.

Because severity is coded 0, 1, and 2, the intercept in models that include severity is evaluated at Minor severity (when the other numeric predictors are zero).
8. Trueness model
Trueness is measured only for Investigator vignettes. The data are reshaped into three severity observations per participant.

The main dataset is trueness_dat, and the primary model is FT1.

The outcome is centered at 3:
trueness_c = trueness - 3
The model predicts centered trueness from continuous QRP severity, with participant and severity random intercepts. The section also reports descriptives, planned severity comparisons, effect sizes, and a figure.
9. Robustness checks
The demographic robustness models add gender, political identification, and religion as covariates to the final Integrity and Likeability models.

The adjusted models are:
- T4_robust
- L4_robust
The code compares the Role × Severity interaction estimates from the original and adjusted models.
10. APA reporting tables and random-effects summaries
The reporting sections create model-fit and fixed-effect tables for:
- Integrity.
- Likeability.
- Trueness.
Additional sections report model comparisons, variance components, and intraclass correlations (ICCs).
11. Exploratory factor and measurement-invariance analyses
The EFA/CFA section creates participant-level data by researcher role and evaluates:
- One-factor evaluation models.
- Two-factor models separating Integrity and Likeability.
- Model fit for Investigators and Inspectors.
- Comparisons of one-factor and two-factor models.
- Configural, metric, and scalar measurement invariance across researcher role.
- Fit-index tables and a model-fit figure.
The measurement-invariance section includes a code comment indicating that it should only be run or interpreted if the two-factor model provides a reasonable fit.
12. Exploratory repeated-measures ANOVAs
The final section treats severity as a categorical within-participant factor. These analyses are exploratory and do not replace the primary multilevel models.
- Integrity: Role and Severity are within-subjects factors.
- Likeability: Role and Severity are within-subjects factors.
- Trueness: Severity is the only within-subjects factor because Trueness is measured only for Investigator vignettes.
The ANOVAs use afex::aov_ez() and the categorical severity levels Minor, Moderate, and Major (coded 0, 1, and 2 in the source data).

Participants with missing values in required repeated-measures cells may be omitted by aov_ez(); the function reports these omissions.
Key objects created by the RMD
Object	Description
diss.full	Imported raw dataset
filtered_by_age	Dataset after date, consent, duration, and age filters
attr_model_dat	Long-format attribute-choice analysis data
tl_dat	Long-format Integrity and Likeability analysis data
trueness_dat	Long-format Trueness analysis data
T4	Final Integrity interaction model
L4	Final Likeability interaction model
FT1	Trueness model
T4_robust	Integrity model with demographic covariates
L4_robust	Likeability model with demographic covariates
integrity_rm_anova	Exploratory Integrity repeated-measures ANOVA
like_rm_anova	Exploratory Likeability repeated-measures ANOVA
trueness_rm_anova	Exploratory Trueness repeated-measures ANOVA


Troubleshooting
cannot open file 'Diss_full.csv'
Make sure the CSV is in the RStudio Project directory and that the filename and capitalization match. Check the current project with:
here::here()
could not find function "aov_ez"
Load afex before running the exploratory ANOVA section:
library(afex)
If it is not installed:
install.packages("afex")
library(afex)
variable(s) ... not in data
Check the outcome name and the data frame supplied to the model. For example:
- Integrity is named Integrity in tl_dat.
- Likeability is named likeability in tl_dat.
- Trueness is named trueness in trueness_dat.
The older name trust may not exist in the current version of the analysis dataset.
Missing-value warnings in repeated-measures ANOVAs
aov_ez() requires observations for the repeated-measures cells used in the analysis. It may remove participants with missing cells. This warning does not mean that those participants were automatically removed from every other analysis.
Model or object not found
Run the RMD chunks in order from the beginning. The model and reporting sections depend on data objects and helper functions defined earlier.
Reproducibility notes
- Keep the original Diss_full.csv unchanged.
- Run the RMD from top to bottom in a fresh R session when preparing final results.
- Record the R version and package versions used for the final dissertation analyses.
- Save the knitted output and any figures/tables used in the dissertation.
- Before final reporting, verify that the implemented exclusion criteria match the preregistration, especially the response-variability and attention-check filters noted above.
- Do not interpret exploratory categorical-severity ANOVAs as replacing the primary continuous-severity multilevel models.
