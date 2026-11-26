# DSCI 522 — Predicting Vancouver Building Permit Processing Times

**Team 38:** Patrick Li Pascal, Yuvraj Singh Walia, Louise Izere, Jiyuan Wang  
**Course:** UBC Master of Data Science — DSCI 522: Data Science Workflows

## About

How accurately can we estimate the number of calendar days between the creation of a Vancouver building permit number and first issuance using recorded permit characteristics? We investigate this question using the [City of Vancouver's Issued Building Permits dataset](https://opendata.vancouver.ca/explore/dataset/issued-building-permits/). Each record represents an issued permit. The analysis uses project value, type of work, permit category, property use, and geographic local area to predict `PermitElapsedDays`.

We compare ordinary linear regression with a dummy regressor that predicts the training-set mean. Our evaluation uses an 80/20 train/test split and five-fold cross-validation on the training data. Feature imputation, scaling, and one-hot encoding are fitted within the training pipelines to reduce the risk of data leakage.

On the dataset snapshot included in this repository, the linear regression model achieved a **test mean absolute error (MAE) of 70.76 days**, **root mean squared error (RMSE) of 120.75 days**, and **R² of 0.324**. Relative to the dummy baseline, this reduced MAE by approximately 31.0% and RMSE by 17.8%. Nevertheless, large errors remain for unusually long permit processing times. The dataset contains only issued permits, so the results do not describe unresolved applications; this exploratory model is not a production-ready estimate of an applicant's wait time.

## Report

The published report is available on [our GitHub Pages site](https://ubc-mds-2026-27.github.io/DSCI-522-Group-38/). The source is [`analysis/permit-processing-time-prediction.qmd`](analysis/permit-processing-time-prediction.qmd), and the rendered report is also retained at [`analysis/permit-processing-time-prediction.html`](analysis/permit-processing-time-prediction.html). A self-contained copy is published as [`docs/index.html`](docs/index.html).

## Usage

We run this project inside a Docker container so that everyone uses the same installed tools and Python package versions. The Docker image is built from the repository's [`Dockerfile`](Dockerfile) and published on [Docker Hub](https://hub.docker.com/r/jiyuanwang/dsci-522-group-38). You do not need to install Python, `uv`, Quarto, or project packages separately on your own computer.

### Setup

Install and start [Docker Desktop](https://www.docker.com/products/docker-desktop/) (or an equivalent Docker installation) and install Git. Then clone the repository and move to its root directory:

```bash
git clone https://github.com/UBC-MDS-2026-27/DSCI-522-Group-38.git
cd DSCI-522-Group-38
```

All subsequent commands should be run from the repository root in a terminal with Docker available. The commands are intended for macOS, Linux, or Git Bash on Windows.

### 1. Prepare an SSH key

The Docker Compose configuration mounts an SSH public key so that contributors can connect to the container with Positron. If you do not already have a key at `~/.ssh/id_ed25519.pub`, create one by running:

```bash
ssh-keygen -t ed25519
```

Press Enter to accept the default filename and follow the prompts. Check that the public key exists **before** starting the container. If the key is missing, Docker can create a directory at the expected file path, which prevents SSH from working later. If you use a different public-key filename, adjust the key path in `docker-compose.yml` accordingly.

### 2. Download the container image

With Docker running, download the version-pinned image specified in [`docker-compose.yml`](docker-compose.yml):

```bash
docker compose pull
```

This downloads the image for your computer's architecture. The Compose file specifies a `sha-` tag rather than `latest`, so the environment is associated with a particular image build.

### 3. Reproduce the analysis

From the repository root, run the following command to execute the Quarto report inside the container:

```bash
docker compose run --rm analysis-env uv run quarto render analysis/permit-processing-time-prediction.qmd
```

The command runs the report's Python code, including data preparation, exploratory analysis, model evaluation, and creation of figures and tables. It writes the rendered report to `analysis/permit-processing-time-prediction.html` and refreshes `data/processed/model_input.csv`. The project files are mounted into the container at `/workspace`, so the output appears in your local repository too.

When the included `data/raw/raw_data.csv` is present, the report uses that snapshot to reproduce the published results. If the file is absent, the code attempts to download the latest data from the City of Vancouver's open-data portal; an internet connection is then required and results may differ because the source changes over time.

The `--rm` option removes the temporary analysis container when rendering finishes, so there is no running container to stop. If you started the persistent container separately with `docker compose up -d`, stop it with `docker compose down` when finished.

## Contributing

If you want to change the analysis, add dependencies, work in Positron over SSH, or update the GitHub Pages report, follow [`CONTRIBUTING.md`](CONTRIBUTING.md). That guide explains the development workflow; this README covers running the analysis.

## Dependencies and project layout

The image installs Python packages from [`pyproject.toml`](pyproject.toml) with exact versions recorded in [`uv.lock`](uv.lock), alongside the project Python version in [`.python-version`](.python-version). [`Dockerfile`](Dockerfile) defines the image and [`docker-compose.yml`](docker-compose.yml) identifies the published image and mounts the working directory.

The raw dataset snapshot is stored at `data/raw/raw_data.csv`, while the selected modeling dataset is written to `data/processed/model_input.csv`. The source and rendered reports live in `analysis/`. The published website uses the copy at `docs/index.html`.

## Limitations

`PermitElapsedDays` measures elapsed calendar days between permit-number creation and first issuance, not necessarily every applicant's full waiting time. The source omits unresolved permits, some predictors may not be available at the moment of application, and random hold-out performance on historical records does not establish future-period performance. See the report's Discussion for more details.

## Generative AI use

The team used OpenAI's ChatGPT to support analysis design, Python and Quarto troubleshooting, checking statistical interpretations, and revising explanatory text. Examples of representative prompts include: “How can I avoid data leakage in a scikit-learn preprocessing pipeline?”, “Why does the Quarto report fail with the freshly downloaded dataset?”, and “Help improve this interpretation of regression errors without changing its meaning.” Team members reviewed the AI suggestions and remain responsible for the submitted code, analysis, sources, and writing. Additional disclosure appears in the report where relevant.

## Licenses

See [`LICENSE.md`](LICENSE.md) for the repository's applicable software and report licenses. The City of Vancouver data is subject to the source provider's licensing terms, available through the [dataset portal](https://opendata.vancouver.ca/explore/dataset/issued-building-permits/).