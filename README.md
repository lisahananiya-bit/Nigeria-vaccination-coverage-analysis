# Nigeria Vaccination Coverage Analysis (R)

![R](https://img.shields.io/badge/R-4.5-276DC3?logo=r&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white)
![Data](https://img.shields.io/badge/Data-DHS%20Program-1f6feb)

**How has child vaccination coverage in Nigeria changed since 1990, which zones and states lag behind, and where does Kano State stand?**

This project analyses subnational immunization data from the Demographic and Health Surveys (DHS) Program across six survey rounds (1990–2024). It combines data cleaning, visualisation, hypothesis testing, regression and cross-validation in R, and ends with a plain-language summary for a Kano State health policymaker.

## Key findings

1. **Northern coverage collapsed between 1990 and 2003, then recovered.** Full vaccination in the Northwest fell from 17.7% to 3.7% and in the Northeast from 15.5% to 6.0%. Both zones then recovered through 2024. National DHS estimates show the same shape (29% in 1990, 13% in 2003, 39% in 2023–24) [3].

2. **The collapse hit routine vaccines, not polio.** Northwest DPT 1 fell from 55.5% to 20.9% and measles from 39.7% to 15.6%, while Northeast Polio 1 rose from 45.2% to 61.6%. The share of children with no vaccine at all also fell, so more children were partially vaccinated. Published accounts describe polio campaigns running alongside a weakening routine system [1, 2]. This data shows the pattern, not the cause.

3. **The Northwest gap is clear against the South, less clear against other Northern zones.** A 2024 one-way ANOVA found significant differences between zones (F(5, 31) = 7.138, p < 0.001). Tukey's test placed the North West below the three Southern zones but not distinguishable from North Central or the North East. A regression on zone and survey year (R² = 0.538) estimated coverage rising about 0.9 percentage points per year. It treats each state's three survey rows as independent, so its p-values are probably optimistic.

4. **Kano jumped, then stalled.** Kano's full vaccination coverage rose from 13.2% (2013) to 34.3% (2018), then only to 35.8% (2024). The 22.6-point rise over 2013–2024 was statistically significant (p < 0.001; 95% CI roughly 16 to 30 points, using unweighted sample sizes of 367 and 271 children). Against a weighted national benchmark, Kano went from 12.1 points below (2013) to 3.0 above (2018) and back to 3.6 below (2024). Among five neighbouring states, Jigawa went from lowest in 2013 to highest in 2024, and by 2024 Kano was level with Kaduna at the bottom.

5. **Kano's remaining gaps are in routine vaccines.** In 2024 Kano was furthest behind the benchmark on measles (38.6% vs 51.4%), roughly level on Polio 3 (45.6% vs 45.6%), and had a higher share of children with no vaccination (35.5% vs 30.9%).

6. **Simple models generalise poorly to unseen states.** A model on zone and survey year scored R² = 0.538 in-sample, 0.439 under random-row cross-validation, and 0.290 when whole states were held out (one fold was negative at −0.447).

## Charts

**Full vaccination by zone, 1990–2024**
![Zone trends](images/zone_trends_1990_2024.png)

**Full vaccination by state, 2024 (coloured by zone)**
![State ranking 2024](images/state_ranking_2024.png)

**Kano vs an approximate national benchmark**
![Kano vs national](images/kano_vs_national.png)

**Kano and neighbouring states**
![Kano vs neighbours](images/kano_vs_neighbours.png)

## Data

- **Source:** DHS Program subnational immunization indicators for Nigeria, accessed through the [Humanitarian Data Exchange](https://data.humdata.org/dataset/dhs-subnational-data-for-nigeria).
- **File used:** `immunization_subnational_nga.csv` (not included in this repository; see *Reproduce* below).
- **Survey rounds:** 1990, 2003, 2008, 2013, 2018 and 2024 (the 2023–24 survey).
- **Granularity:** state-level (36 states + FCT) from 2013; earlier rounds report geopolitical zones only.
- **Primary metric:** "Fully vaccinated (8 basic antigens)" for children age 12–23 months: one dose of BCG, three of polio, three of DPT-containing vaccine and one of measles [3].

## Methods

| Question | Method |
|---|---|
| Which states and zones lead or lag? | `dplyr` cleaning, `ggplot2` charts |
| How did coverage change from 1990 to 2024? | Zone-level multi-line chart (zone rows are the only ones that go back to 1990) |
| Do zones differ in 2024? | One-way ANOVA with Tukey HSD post-hoc test |
| Was Kano's 2013–2024 rise real? | Two-proportion test using unweighted sample sizes |
| How does Kano compare with the country? | Sample-weighted benchmark from state estimates, checked against official DHS national figures |
| What predicts coverage? | Linear regression on zone and survey year |
| Does the model generalise? | Manual 5-fold cross-validation, random rows vs whole states held out |

## Limitations

- State-level estimates exist for only three survey rounds, so state trends rest on three points and say nothing about the years between surveys.
- DHS data come from a clustered, weighted survey design. The tests here treat samples as simple random samples, which likely overstates precision, and the regression treats repeated state observations as independent.
- The national benchmark is built from state estimates, not an official national figure (it matches the published DHS national values for 2013, 2018 and 2024: 25%, 31% and 39%).
- The predictive model uses only zone and year, performs weakly on unseen states, and every result describes associations rather than causes.
- Explanations from the literature (polio campaigns, the 2003 boycott) are context, not findings tested by this data.

## What I would do next

- Redo the inference with a design-based survey analysis that uses the DHS weights and clusters.
- Fit a mixed-effects model with a state-level random effect instead of treating repeated state rows as independent.
- Find programme and financing data to ask what drove Jigawa's gains and why Kano's progress slowed after 2018.

## Reproduce

1. Install R (4.5.0 was used) and Jupyter. To run R in Jupyter, install the kernel from R: `install.packages("IRkernel"); IRkernel::installspec(user = TRUE)`.
2. Install the tidyverse: `install.packages("tidyverse")`. Everything else uses base R (`stats`).
3. Download the immunization CSV from the HDX page above and save it as `immunization_subnational_nga.csv` in the same folder as the notebook. Check the dataset's licence on that page before redistributing it.
4. Run `jupyter notebook`, open `Nigeria_Vaccination_Analysis.ipynb` and choose **Kernel → Restart & Run All**.

## Repository contents

```
.
├── Nigeria_Vaccination_Analysis.ipynb   # full analysis with write-up
├── images/                              # charts used in this README
├── README.md
└── .gitignore
```

## References

1. Jegede AS. What led to the Nigerian boycott of the polio vaccination campaign? *PLoS Medicine*. 2007;4(3):e73. doi:10.1371/journal.pmed.0040073.
2. Akinyemi O, Adebayo A, Bassey C, Nwaiwu C, Kalbarczyk A, Nomhwange T, Alonge OO, Owoaje ET. A qualitative exploration of the contributions of Polio Eradication Initiative to the Nigerian health system: policy implications for polio transition planning. *Tropical Medicine and Health*. 2022. doi:10.1186/s41182-022-00429-0.
3. Federal Ministry of Health and Social Welfare of Nigeria (FMoHSW), National Population Commission (NPC) [Nigeria], and ICF. 2024. *Nigeria Demographic and Health Survey 2023–24: Key Indicators Report*. Abuja, Nigeria, and Rockville, Maryland, USA: NPC and ICF.

## Author

**Lisa N. Hananiya** · Health education background, building a career in data science.
