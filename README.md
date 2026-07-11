# Data-Driven PD Subtype Calculator

> **Scope:** the calculator covers **years 1–5 since PD diagnosis only**. Beyond year 5 the within-disease-year PPMI sample becomes too small for stable percentile thresholds, so later years are intentionally excluded.

Interactive web tool to assign Parkinson's disease subtypes (DM-PD / IM-PD / MMP-PD) using **disease-duration–specific percentile thresholds** derived from the PPMI April-2026 cohort (N = 1,030 de novo idiopathic PD).

The tool implements the Fereshtehnejad 2017 (*Brain*) DM/MMP/IM rule with **year-by-year percentile cutoffs** rather than fixed baseline thresholds, so a patient's classification reflects how they compare to other patients **at the same disease duration**.

**Live demo:** https://negidamd.github.io/data-driven-pd-subtype-calculator/
**Source:** https://github.com/Negidamd/data-driven-pd-subtype-calculator
**Reference:** Negida A, Mukhopadhyay N, Berman BD, Fereshtehnejad SM, Barrett MJ. Disease-Duration–Specific Percentiles for Prospective Subtyping of Parkinson's Disease: A PPMI-Based Study. *Mov Disord Clin Pract*. 2026. doi:[10.1002/mdc3.70740](https://doi.org/10.1002/mdc3.70740) · PMID: 42427169

---

## Inputs

| Field | Range | Notes |
|---|---|---|
| Years since PD diagnosis | 1 to 5 | integer (round down) |
| MDS-UPDRS Part II total | 0 to 52 | patient-reported motor ADL |
| MDS-UPDRS Part III off-medication total | 0 to 132 | OFF-state, or any visit while untreated |
| PIGD/TD ratio | 0 to 4.40 | Stebbins 2013; tool offers ratio-direct OR raw-items computation with winsorization |
| RBDSQ-13 total | 0 to 13 | 13-item REM Sleep Behavior Disorder Screening Questionnaire |
| SCOPA-AUT total | 0 to 69 | autonomic symptom burden |
| MoCA total | 0 to 30 | global cognition (lower = worse) |

## Output

- **Subtype assignment** (DM-PD, IM-PD, or MMP-PD) per the Fereshtehnejad 2017 rule
- **Motor composite z-score** (mean of the 3 motor z-scores standardized within the patient's disease year)
- **Per-channel percentile rank** for each input score, against same-disease-year peers
- **Worst-domain flags** (motor, RBD, autonomic, cognitive)
- Year-specific reference statistics (mean, SD, percentile breakpoints)

## How the computation works

For each of the three motor components (MDS-UPDRS II, MDS-UPDRS III off, PIGD/TD ratio):

```
z(score, year) = (raw_score − mean[year]) / SD[year]
```

with `mean` and `SD` taken from the PPMI April-2026 reference (file `T_reference_percentiles.csv`, included in this repo).

The motor composite is:

```
motor_z_composite = (z_NP2 + z_NP3off + z_pigd_td) / 3
```

A patient is classified as **worst-motor** if `motor_z_composite > year-specific 75th-percentile threshold`. Non-motor "worst" channels are determined by direct percentile comparison: RBDSQ > P75, SCOPA-AUT > P75, MoCA < P25 (lower = worse).

The Fereshtehnejad 2017 assignment rule then becomes:

| Subtype | Rule |
|---|---|
| **DM-PD** | worst-motor AND ≥ 1 non-motor worst, OR all 3 non-motor worst |
| **MMP-PD** | not worst-motor AND 0 non-motor worst |
| **IM-PD** | anything else |

## Deployment to GitHub Pages

```bash
# Create a new public repository on GitHub:
#   github.com/Negidamd/data-driven-pd-subtype-calculator
git init
git remote add origin https://github.com/Negidamd/data-driven-pd-subtype-calculator.git
git add index.html README.md T_reference_percentiles.csv T_reference_percentiles.json T4_cutoffs_and_assignment_rules.csv
git commit -m "Initial release — data-driven PD subtype calculator v1.0"
git branch -M main
git push -u origin main

# Then on GitHub:
#   Settings → Pages → Source: Deploy from a branch → main / (root) → Save
# The tool will be live at:
#   https://negidamd.github.io/data-driven-pd-subtype-calculator/
```

The HTML is **fully self-contained** (no external dependencies, no build step, no fetch calls). The reference data is embedded inline in the `<script>` block. The companion CSV/JSON files are provided for users who want to consume the lookup table programmatically (e.g., from R, Stata, or Python).

## Files

| File | Description |
|---|---|
| `index.html` | The interactive calculator (self-contained, ~45 KB) |
| `T_reference_percentiles.csv` | Per-year statistics for each subtyping scale (mean, SD, percentiles, cutoffs) |
| `T_reference_percentiles.json` | Same data in nested JSON |
| `T4_cutoffs_and_assignment_rules.csv` | Year 1–5 cutoffs from the published Table 4 |
| `README.md` | This file |

## Citation

If you use this tool in research, please cite:

> Negida A, Mukhopadhyay N, Berman BD, Fereshtehnejad SM, Barrett MJ. Disease-Duration–Specific Percentiles for Prospective Subtyping of Parkinson's Disease: A PPMI-Based Study. *Movement Disorders Clinical Practice*. 2026. doi:[10.1002/mdc3.70740](https://doi.org/10.1002/mdc3.70740). PMID: 42427169.

and the source methodological framework:

> Fereshtehnejad S-M, Zeighami Y, Dagher A, Postuma RB. Clinical criteria for subtyping Parkinson's disease: biomarkers and longitudinal progression. *Brain*. 2017;140(7):1959-1976. DOI: [10.1093/brain/awx118](https://doi.org/10.1093/brain/awx118)

## Disclaimer

This tool is provided for **research and clinical-decision support** purposes only. It is not a regulated medical device and should not be used as the sole basis for clinical decisions. Subtype assignments produced by this tool reflect group-level percentile thresholds from a single observational cohort (PPMI) and may not generalize to all patient populations. Use clinical judgment.

## License

MIT License. PPMI reference data are derived from the publicly accessible Parkinson's Progression Markers Initiative (PPMI) study and are subject to PPMI's data-use terms.

## Contact

Ahmed Negida, MD, PhD
Department of Neurology, Parkinson & Movement Disorder Center
Virginia Commonwealth University, Richmond, VA
Email: ahmed.negida@vcuhealth.org · ORCID: 0000-0001-5363-6369
