# DSCI 522 — Predicting Vancouver Building Permit Processing Times

**Team 38:** Patrick Li Pascal, Yuvraj Singh Walia, Louise Izere, Jiyuan Wang  
**Course:** UBC Master of Data Science — DSCI 522 (Data Science Workflows)

## Project overview

How accurately can we estimate the number of calendar days between creation of a Vancouver building permit number and first issuance using recorded permit characteristics?

We analyze the [City of Vancouver's Issued Building Permits dataset](https://opendata.vancouver.ca/explore/dataset/issued-building-permits/), using five predictors: project value, type of work, permit category, property use, and geographic local area. We compare ordinary linear regression against a dummy regressor that predicts the training-set mean. We use an 80/20 train/test split (`random_state=42`) and five-fold cross-validation on the training set. Imputation, scaling, and categorical encoding are fitted within model pipelines to avoid leakage.

On the analyzed snapshot, the linear regression model achieved **test MAE of 71.44 days**, **RMSE of 123.35 days**, and **R² of 0.327**. Compared with the dummy baseline, this represents approximately **30.9% lower MAE** and **18.0% lower RMSE**. Error remains substantial for unusually long processing times. The dataset includes *issued* permits, not unresolved applications, and some predictors may not be reliably available at application time. The model is therefore an exploratory analysis, **not a production-ready permit wait-time estimator**.

**Reproducibility note:** Reported results use the saved CSV included in this repository. Use it to reproduce our results; downloading newer City of Vancouver data may produce different results.

## Report and repository structure

- **Source report:** [`analysis/permit-processing-time-prediction.qmd`](analysis/permit-processing-time-prediction.qmd)
- **Rendered report:** [View the report](https://ubc-mds-2026-27.github.io/DSCI-522-Group-38/)
- **Raw data snapshot:** `data/raw/raw_data.csv`
- **Selected modeling data:** `data/processed/model_input.csv` (five predictors and `PermitElapsedDays`)
- **Environment definition:** `pyproject.toml`, `uv.lock`, `.python-version`
- **Project policies:** `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, `LICENSE.md`

## Reproduce the analysis

### Requirements

Install [uv](https://docs.astral.sh/uv/) and [Quarto](https://quarto.org/docs/get-started/). These instructions were developed on Windows and Mac using **Python 3.14.7** and **Quarto 1.10.18**.

### 1. Clone the repository

```bash
git clone https://github.com/UBC-MDS-2026-27/DSCI-522-Group-38.git
cd DSCI-522-Group-38
```

### 2. Create the locked Python environment

```bash
uv sync --locked
```

The `.python-version` file selects Python 3.14. Run `uv run python --version` to check the selected interpreter. If necessary, run `uv python install 3.14` first.

### 3. Render the Quarto report

**Git Bash on Windows:**

```bash
export QUARTO_PYTHON="$(pwd)/.venv/Scripts/python.exe"
quarto render analysis/permit-processing-time-prediction.qmd --to html
```

**macOS / Linux:**

```bash
export QUARTO_PYTHON="$(pwd)/.venv/bin/python"
quarto render analysis/permit-processing-time-prediction.qmd --to html
```

The rendered report will be written to `analysis/permit-processing-time-prediction.html`. It uses `embed-resources: true`, so the HTML is intended to be self-contained. The report writes or refreshes `data/processed/model_input.csv`.

### Data download and reproducibility note

If `data/raw/raw_data.csv` is missing, the Quarto report attempts to download it from the [City of Vancouver Open Data Portal](https://opendata.vancouver.ca/explore/dataset/issued-building-permits/) before processing. An internet connection is required in that case. The source dataset changes over time; a newly downloaded version may produce different sample sizes, plots, and scores. The reported results use the snapshot analyzed on November 20, 2026. **Automatic download from a clean clone has not yet been separately verified.**

## Data and analytical limitations

The response, `PermitElapsedDays`, is the elapsed calendar days between permit-number creation and first issuance. This is not necessarily the full wait experienced by all applicants. The records exclude unresolved cases, and the availability of all input features at application time has not been independently established. Randomly held-out historical records also do not establish predictive accuracy for future periods. See the report's Discussion for additional limitations.

## Licenses

See [`LICENSE.md`](LICENSE.md) for the applicable licenses: **MIT** for repository source code and **CC BY-NC-ND 4.0** for the original analysis report and visualizations. The City of Vancouver data remains subject to the source provider's terms; see the [dataset portal](https://opendata.vancouver.ca/explore/dataset/issued-building-permits/).

## Contributing and conduct

Please read [`CONTRIBUTING.md`](CONTRIBUTING.md) and [`CODE_OF_CONDUCT.md`](CODE_OF_CONDUCT.md) before proposing changes. Team members should use reviewed pull requests for contributions.

## Generative AI Use

Generative AI tools, primarily **OpenAI's ChatGPT**, were used to assist with the development, analysis, and documentation of this project. ChatGPT served as a supporting tool for brainstorming, troubleshooting technical issues, reviewing analytical approaches, interpreting model results, and improving written explanations.

Specifically, ChatGPT was used for the following purposes:

- **Analysis design:** Discussing the regression workflow, selection of predictor variables, comparison with a dummy baseline, and strategies for preventing data leakage.
- **Code development and debugging:** Assisting with Python code, scikit-learn preprocessing pipelines, cross-validation, model evaluation, and troubleshooting programming errors.
- **Exploratory data analysis:** Providing suggestions for data visualization, identifying potential data quality issues, and improving explanations of observed patterns.
- **Model evaluation and interpretation:** Clarifying evaluation metrics such as MAE, RMSE, and R², reviewing model performance, and discussing limitations related to prediction errors and data availability.
- **Reproducibility and environment setup:** Troubleshooting Quarto rendering, Python environment configuration, dependency management with `uv`, and issues involving dataset downloading and processing.
- **Documentation and writing:** Improving the clarity, organization, and consistency of the Quarto report, README, and other project documentation.

### Examples of Prompts Used

The following are representative examples of prompts used during project development:

1. "Help me design a regression workflow for predicting Vancouver building permit processing times."
2. "How can I prevent data leakage when using scikit-learn pipelines for preprocessing and cross-validation?"
3. "Explain the differences between MAE, RMSE, and R² and how to interpret these metrics."
4. "Help me debug this Python code and explain why the error occurs."
5. "Why is Quarto unable to detect my Python environment when rendering the report?"
6. "How can I make my analysis reproducible using `uv` and a locked Python environment?"
7. "Review this paragraph and improve its clarity and coherence without changing its technical meaning."
8. "Review my README and suggest improvements to the project setup and reproducibility instructions."

### Verification and Responsibility

All AI-generated suggestions were subject to review by project team members before inclusion in the final submission. The team remains responsible for verifying the correctness of the code, analytical results, interpretations, references, and documentation.

ChatGPT was used as an **assistance tool rather than an authoritative research source**. All final methodological decisions, implementation choices, conclusions, and submitted materials remain the responsibility of the project team.
