# Contributing

We welcome bug reports, suggestions, documentation improvements, and analysis contributions to the DSCI 522 Team 38 project. Please follow our [Code of Conduct](CODE_OF_CONDUCT.md) when participating.

## Submit a contribution

1. Discuss the change in a GitHub issue and agree on its scope before starting substantial work.
2. Create a branch in this repository with a meaningful name, such as `fix-data-download` or `improve-readme`. Do not commit directly to `main`. If you do not have write access, open an issue to discuss how to contribute.
3. Keep the change focused and use clear commit messages. Commit and push progress whenever you work on the project.
4. Check your work. For code changes, run relevant checks or tests. For analysis changes, render the Quarto report and check the results. Proofread documentation and check its links. Follow the environment instructions in the [README](README.md); raise an issue if instructions are missing or unclear.
5. Open a pull request that links to the issue and explains what changed, why, and how you checked it. Disclose any AI assistance.
6. Request a review from another team member and address their feedback. A human teammate must review and approve the change before a human merges it. Team members are expected to review pull requests within 24 hours of creation.

## Use of generative AI

Tools such as ChatGPT, Codex, or Copilot may help explain code, suggest fixes, draft documentation, and double-check code.

Contributors remain responsible for understanding and verifying everything they submit, including code, written claims, and references.

Disclose the tool and how it was used in the pull request description. Record project AI use and example prompts in the README. Any additional uses must be agreed upon by the team and documented in this file.

Humans must open GitHub issues and pull requests, make final review decisions, and merge changes. AI agents must not perform these actions.

ChatGPT Codex was used to structure CONTRIBUTING.md given the content and sections by a team member.

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

Keep `embed-resources: true` enabled in the report’s YAML header so the HTML includes its figures and styling.

Open `docs/index.html` locally and check the text, tables, figures and links. Commit the updated report HTML and publishing files, then open a pull request for teammate review.

GitHub Pages must be configured to deploy from the `main` branch and `/docs` folder. After merging and deployment, check the [published report](https://ubc-mds-2026-27.github.io/DSCI-522-Group-38/) again.

Before creating release `1.0.0`, confirm that the published report matches the version included in the release.

## Licences and sources

Respect the [project licences](LICENSE.md), and credit any third-party material you use or adapt.

This guide was prepared for Team 38 using the [DSCI 522 Milestone 1 instructions](https://ubc-mds.github.io/DSCI_522_dsci-workflows_students/assignments/milestone1.html).
