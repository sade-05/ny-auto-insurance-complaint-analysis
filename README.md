# NY Auto Insurance Complaint Analysis
### NY Department of Financial Services Data | 2009–2024 | Python

---

## Background

New York State requires automobile insurers to report complaint data annually
to the Department of Financial Services (DFS). The DFS makes these rankings public and rates each 
insurance company based on how many valid complaints they get for every $1 million in premiums they collect.
A higher ratio means more policyholdersfiled complaints that DFS agreed were valid.

For anyone who has worked inside a NY no-fault PIP operation, these complaint
rankings tell a familiar story. The carriers that appear repeatedly at the top
of the worst-ratio lists are the same names that generate the most contentious
claim files — delayed payments, disputed denials, and claimants escalating to
the DFS because internal resolution failed. This project quantifies those
patterns across 16 years of data.

This analysis was built using Python (pandas, matplotlib, seaborn) and draws
on 9 years of NY no-fault PIP claims adjudication experience to interpret the
findings in context. The domain knowledge behind the feature engineering and
findings commentary is what separates this from a generic data exercise.

---

## Data Source

**Dataset:** NY DFS Automobile Insurance Complaint Rankings
**Publisher:** New York State Department of Financial Services
**Coverage:** 2009–2024 | 2,461 records | 243 unique insurers
**Access:** Public API — no download required

The notebook pulls data directly from the NY Open Data API:
```
https://data.ny.gov/api/views/h2wd-9xfe/rows.csv?accessType=DOWNLOAD
```

See [`data/README.md`](data/README.md) for full source documentation.

---

## Methodology

### Complaint Ratio
The DFS complaint ratio is calculated as upheld complaints per $1M in premiums
written. It normalizes complaint volume by carrier size, making it possible to
compare a regional carrier writing $50M in premiums against a national carrier
writing $3B.

**Important limitation:** The ratio is unreliable for carriers writing less than
$10M in premiums — a single complaint produces an artificially extreme ratio.
All ratio-based analysis in this project applies a $10M premium floor to
exclude these distortions. This filter reduces the universe from 243 to 147
carriers but produces meaningfully more accurate comparisons.

### Chronic Offender Classification
A carrier is classified as a chronic offender if it appears in the worst
complaint ratio quartile (top 25% of ratios among active carriers) in three
or more years. This threshold was chosen to distinguish structural complaint
problems from year-to-year volatility.

### Uphold Rate
Uphold rate measures the percentage of total complaints that DFS determined
were valid — meaning the carrier was in the wrong. A high uphold rate indicates
not just that claimants complained, but that they were right to do so. This
metric is analyzed separately from complaint volume because a carrier can have
low volume but a high proportion of valid complaints, or vice versa.

---

## Key Findings

### Finding 1 — 2024 industry-wide complaint surge
Total complaints reached **5,151 in 2024** — the highest since 6,808 in 2009
and a **47% increase over 2023**. The increase was broad-based across nearly
every major carrier, indicating a systemic market condition rather than
isolated carrier failures. This aligns with post-pandemic dynamics in NY
no-fault: accident frequency recovered, medical costs inflated, carriers
tightened payment standards, and claimants escalated disputes at higher rates.
From 9 years of PIP claims experience, this pattern is recognizable — the same
market stress that produces longer adjudication timelines and more arbitration
filings also produces more DFS complaints.

### Finding 2 — Country-Wide Insurance: 12 years in the worst quartile
**Country-Wide Insurance Company** appeared in the worst complaint ratio
quartile in **12 of 16 years** — the longest chronic offender streak in the
dataset. Country-Wide is a well-known NY no-fault carrier concentrated in
New York City, particularly in the outer boroughs that generate the highest
PIP claim frequency and fraud exposure in the state. Their name appears
repeatedly in no-fault arbitration and litigation, and their chronic complaint
history in this data is consistent with that claims profile.

Notably, Country-Wide showed sustained improvement from 2017 (ratio: 0.640)
through 2023 (ratio: 0.040) — a meaningful operational turnaround. Their
2024 reversal, with complaints tripling from 39 to 112, warrants monitoring
as a potential signal of renewed claims handling stress.

### Finding 3 — Integon National: largest single-year spike in 2024
**Integon National Insurance Company** recorded the most dramatic complaint
increase in the dataset — jumping from **43 complaints in 2023 to 227 in 2024,
a 428% increase**. Unlike some outliers in the top-ratio list, Integon writes
substantial premium volume ($238M in 2024), meaning this spike cannot be
dismissed as a small-carrier ratio artifact.

Integon specializes in nonstandard auto insurance — high-risk drivers,
frequently lower-income policyholders who are statistically less likely to
file formal complaints. A surge of this magnitude in a nonstandard book is
a significant signal. In NY PIP terms, nonstandard carriers are
disproportionately represented in no-fault disputes, fraud rings, and
delayed payment patterns.

### Finding 4 — DFS uphold patterns identify carriers most often in the wrong
Filtering to carriers with 50+ total complaints and $10M+ in premiums,
the highest uphold rates were:

| Carrier | Uphold Rate | Context |
|---|---|---|
| AIG Property Casualty | 35.7% | 13 years in dataset |
| SAFECO Insurance of Indiana | 32.5% | 13 years in dataset |
| Permanent General Assurance | 28.0% | Nonstandard auto specialist |
| Plymouth Rock NY | 25.7% | Regional NY carrier |

A 35% uphold rate means more than one in three claimants who filed a complaint
with DFS was found by the regulator to be in the right. In no-fault terms,
this reflects carriers where payment denials and delays were frequently
unjustified — not just contested.

### Finding 5 — Large carrier complaint volume vs. ratio tells two stories
The largest carriers by premium show low complaint ratios but high absolute
complaint counts. State Farm generated **847 complaints in 2024** despite a
ratio of just 0.033. GEICO across two entities generated **720 complaints**.
Both metrics matter: the ratio tells you how a carrier performs relative to
their size; the raw count tells you how many real policyholders experienced
a problem worth escalating to a regulator. For a NY no-fault examiner, both
numbers are relevant — the ratio for benchmarking, the count for workload
and litigation exposure.

### Finding 6 — COVID era complaint trend
Complaints dropped to a 16-year low in 2021 (2,591) before recovering.
The pandemic reduced accident frequency sharply in 2020, which lowered
PIP claim volume and complaint activity. But uphold rates actually
*increased* during this period — rising from 9.9% in 2018 to 16.6% in
2021 — suggesting that the complaints that were filed during COVID were
more likely to be legitimate. Claimants with marginal complaints may
have self-selected out during the pandemic period, leaving a higher
proportion of valid grievances in the DFS pipeline.

---

## Visualizations

### Market Overview: Complaint Trends 2009–2024
![Market Overview](outputs/ny_complaint_analysis.png)

### Deep Dive: Chronic Offenders, 2024 Surge, and Uphold Patterns
![Deep Dive](outputs/ny_complaint_deepdive.png)

---

## How to Run

### Requirements
```
Python 3.10+
pandas
numpy
matplotlib
seaborn
scipy
requests
```

Install dependencies:
```bash
pip install -r requirements.txt
```

### Run the notebook
```bash
jupyter lab
```

Open `notebooks/ny_complaint_analysis.ipynb` and run all cells.
Data pulls directly from the NY Open Data API — no file download needed.
Runtime is approximately 15–20 seconds.

---

## Project Structure
```
ny-auto-insurance-complaint-analysis/
│
├── README.md                        # This file
├── requirements.txt                 # Python dependencies
│
├── notebooks/
│   └── ny_complaint_analysis.ipynb  # Full analysis notebook
│
├── data/
│   └── README.md                    # Data source documentation
│
└── outputs/
    ├── ny_complaint_analysis.png    # Market overview charts
    ├── ny_complaint_deepdive.png    # Deep dive charts
    ├── ny_complaints_yearly.csv     # Yearly market summary
    ├── ny_chronic_offenders.csv     # Chronic offender rankings
    ├── ny_complaint_jumps_2024.csv  # 2023→2024 complaint changes
    └── ny_high_uphold_rate.csv      # Carriers with highest uphold rates
```

---


## About This Project

This analysis was built as part of a self-directed transition from NY no-fault
PIP claims adjudication into insurance analytics and data science. The
underlying domain knowledge — 9 years adjudicating PIP claims under NY
Insurance Law Article 51 and NY DFS Regulation 68 — informed every aspect
of this project: which carriers to look at closely, why certain complaint
patterns matter more than the ratio alone suggests, and what the COVID-era
trend means in the context of actual claims operations.

The technical implementation uses Python with pandas, matplotlib, and seaborn.
SQL and R projects are in development.

---

## References

- [NY DFS Auto Insurance Complaint Rankings](https://www.dfs.ny.gov/consumers/auto_insurance/auto_insurance_complaint_ranking)
- [NY Open Data — Complaint Rankings Dataset](https://data.ny.gov/Government-Finance/Automobile-Insurance-Company-Complaint-Rankings-Be/h2wd-9xfe)
- [NY Insurance Law Article 51 — No-Fault](https://www.nysenate.gov/legislation/laws/ISC/A51)
- [NY DFS Regulation 68 — No-Fault Benefits](https://govt.westlaw.com/nycrr/Browse/Home/NewYork/NewYorkCodesRulesandRegulations?guid=I070b7030b5a111dda0a4e17826ebc834)
- [NPPES NPI Registry](https://npiregistry.cms.hhs.gov/)
