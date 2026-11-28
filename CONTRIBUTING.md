# Contributing to the DSCI 522 Team 38 Project

We welcome reproducibility fixes, bug reports, documentation improvements, and contributions to our analysis of Vancouver building permit processing times. Please follow our [Code of Conduct](CODE_OF_CONDUCT.md) and the team's pull request review process.

## Before making a change

For a substantial change, open a GitHub issue describing the problem or proposed improvement and agree on its scope with the team. If you discover a bug, include a minimal reproducible example and relevant error messages.

Small typographical errors should be corrected in the source (`analysis/permit-processing-time-prediction.qmd`) or original documentation, rather than only in the generated HTML.

## Set up the Container

We develop inside the same Docker environment used to reproduce the analysis. **First follow the [Usage instructions in the README](README.md#usage)** to install Docker, clone the repository, create an SSH key if necessary, and run `docker compose pull`.

Those steps are not repeated here. Run the following commands from the repository root.

### First-time configuration

Install [Positron](https://positron.posit.co/download.html) on your computer. Positron provides a built-in Remote SSH connection, so no separate SSH extension is required.

Before connecting, verify that Git knows your identity on the host computer:

```bash
git config --global user.name
git config --global user.email
```

If either value is empty or incorrect, configure it with your own name and the email associated with your GitHub account:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

Our `docker-compose.yml` mounts your `~/.gitconfig` in read-only mode so commits created from the container have the correct author details.

### Work in Positron over SSH

Each time you start a development session, open a terminal at the repository root and run:

```bash
docker compose up -d
docker compose ps
```

The `analysis-env` service should be running.

In Positron, open the Command Palette (`Ctrl+Shift+P` on Windows or `Cmd+Shift+P` on macOS), run **Remote SSH: Show Remote Menu**, and choose **Connect to Host**.

Enter `root@localhost:2222`. When prompted, trust the container's SSH fingerprint and select **Linux** as the remote platform. On the first connection, Positron may need to install its remote server inside the container.

Once connected, select **Open Folder** and enter `/workspace`. The repository is mounted there, so edits made in the remote window update the working files on your computer.

Select the **Python interpreter installed in the container**, not the system Python or an old local environment. In Positron's interpreter selector, choose **New Console Session** and the environment whose executable is `/opt/venv/bin/python`.

If it does not appear, open the Command Palette, select **Python: Select Interpreter**, choose **Enter interpreter path**, and enter:

```text
/opt/venv/bin/python
```

You can confirm the active interpreter in the Positron console:

```python
import sys
print(sys.executable)
```

It should print `/opt/venv/bin/python`. Avoid selecting `/usr/bin/python3` or `/workspace/.venv`, which are not the locked project environment built in the image.

When finished, close the remote Positron window and stop the persistent container from your local terminal:

```bash
docker compose down
```

### If the SSH connection fails

If `~/.ssh/id_ed25519.pub` is a directory rather than a public-key file, stop the container, remove that incorrectly created **directory** only after verifying its contents, and create the SSH key as described in the README.

If `~/.gitconfig` was similarly created as a directory or Git cannot find your identity, stop the container, correct that path, configure Git on the host, and restart.

If Positron asks for a password, confirm that your public key path matches the volume declaration in `docker-compose.yml`.

If SSH reports that the remote host identification has changed after an image update, run:

```bash
ssh-keygen -R "[localhost]:2222"
```

If port 2222 is already occupied, coordinate with the team before changing the host-side port in `docker-compose.yml`, then connect using the updated port.

If GitHub authentication is requested while pushing from Positron, complete the sign-in shown by the editor.

For further guidance, see [Working in a container over SSH with Positron](https://ubc-dsci.github.io/reproducible-and-trustworthy-workflows-for-data-science/lectures/containers-ssh-positron.html).

## Run and verify the analysis

Before requesting review for a change to the analysis, render the full report from the repository root inside the Docker image:

```bash
docker compose run --rm analysis-env uv run --locked quarto render analysis/permit-processing-time-prediction.qmd --to html
```

Inspect the generated `analysis/permit-processing-time-prediction.html` and verify that charts, tables, and reported numbers agree with the source and intended dataset snapshot.

The one-off container is automatically removed after this command finishes.

## Add or update a dependency

Create a feature branch before changing dependencies. Inside the container's project directory, add a package with an explicit version; for example:

```bash
uv add "package-name==x.y.z"
```

Replace the example package and version with the real values.

Commit the updated `pyproject.toml` and `uv.lock` together and push your branch.

The GitHub Actions image-publishing workflow is designed to build and publish a new image and update the SHA-tagged image reference in `docker-compose.yml` on that branch.

Verify that the workflow finished successfully before running:

```bash
git pull
docker compose pull
```

After a teammate reviews and merges the PR, wait for the workflow on `main` to complete. Everyone should then pull the latest `main` and its Docker image.

Do not manually change the Compose image tag to `latest`.

## Update the published report

GitHub Pages displays `docs/index.html`, a copy of our rendered report. Refresh this copy whenever the analysis is re-rendered.

After completing the container setup, run these commands from the repository root on a feature branch, using Git Bash on Windows or a terminal on macOS/Linux, with Docker running:

```bash
docker compose pull
docker compose run --rm analysis-env uv run --locked quarto render analysis/permit-processing-time-prediction.qmd --to html
```

After rendering succeeds, copy the updated report into `docs/`:

```bash
mkdir -p docs
cp analysis/permit-processing-time-prediction.html docs/index.html
touch docs/.nojekyll
```

Keep `embed-resources: true` enabled in the report's YAML header so the HTML includes its figures and styling.

Open `docs/index.html` locally and check the text, tables, figures, and links. Commit the updated report HTML and publishing files, then open a pull request for teammate review.

GitHub Pages must be configured to deploy from the `main` branch and `/docs` folder. After merging and deployment, check the [published report](https://ubc-mds-2026-27.github.io/DSCI-522-Group-38/) again.

Before creating release `1.0.0`, confirm that the published report matches the version included in the release.

## Pull request workflow

Create a descriptive branch from an up-to-date `main`, such as `update-docker-readme` or `fix-categorical-encoding`.

Keep changes focused, commit and push your progress with meaningful messages, and open a pull request to `main`.

Link its GitHub issue (for example, `Closes #12`), explain your changes and verification steps, and disclose any AI assistance used.

At least one teammate other than the author should review and approve each pull request before it is merged.

Follow PEP 8 for new Python code where practical.

## Use of generative AI

Generative AI may support debugging, explanations, review, and writing, but contributors must understand and validate their changes.

ChatGPT Codex was used to help structure `CONTRIBUTING.md` based on content and sections provided by a team member.

Disclose relevant AI use in pull requests. The project-wide disclosure appears in the [README](README.md#generative-ai-use).

Team members retain responsibility for their decisions, code, references, and final submissions.

## Licenses and attribution

Respect the project's [licenses](LICENSE.md) and attribute third-party work appropriately.

These contribution guidelines adapt the DSCI 522 Milestone 2 sample to Team 38's Docker and Quarto project.