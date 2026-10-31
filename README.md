# ENPM611 Fall 2026 Group 6 Project

Team 6 is extending the course Python application to analyze Poetry GitHub issue activity: time to closure, contributor activity, and time to first response.

This is our shared fork of the [ENPM611 application template](https://github.com/enpm611/project-application-template).

## Team

| Member | GitHub | Milestone 1 responsibility |
| --- | --- | --- |
| Marcin | [mpoplaw1](https://github.com/mpoplaw1) | Sequence diagram and repository |
| Ashish Bhoya | [11Ashish11](https://github.com/11Ashish11) | Component diagram after changes |
| Mahima Shenoy | [s-mahima09](https://github.com/s-mahima09) | Component diagram before changes |

Implementation and testing responsibilities will be recorded as the team assigns and completes them.

## Current status

The main branch currently contains the original template. Feature 0 is the provided example. Features 1, 2, and 3 remain placeholders in run.py; the new analysis modules are planned and have not been implemented on main.

Milestone 1 diagrams are available on the design branches linked below. These describe the existing architecture and planned changes.

## Milestone 1 Design

| Artifact | Mermaid source | Image |
| --- | --- | --- |
| Component diagram before changes | [TXT](https://github.com/mpoplaw1/Fa26-Group6-project/blob/milestone1-component-before/docs/milestone1/team_6_component_diagram_before.txt) | [SVG](https://github.com/mpoplaw1/Fa26-Group6-project/blob/milestone1-component-before/docs/milestone1/team_6_component_diagram_before.svg) |
| Component diagram after changes | [TXT](https://github.com/mpoplaw1/Fa26-Group6-project/blob/milestone1-component-after/docs/milestone1/team_6_component_diagram_after.txt) | [SVG](https://github.com/mpoplaw1/Fa26-Group6-project/blob/milestone1-component-after/docs/milestone1/team_6_component_diagram_after.svg) |
| Contributor profile sequence diagram | [TXT](https://github.com/mpoplaw1/Fa26-Group6-project/blob/codex/milestone1-sequence/docs/milestone1/team_6_sequence_diagram.txt) | [SVG](https://github.com/mpoplaw1/Fa26-Group6-project/blob/codex/milestone1-sequence/docs/milestone1/team_6_sequence_diagram.svg) |

The sequence diagram follows `python run.py --feature 2 --user abn`, from configuration and data loading through contributor comparison and output.

[Repository URL submission file](docs/milestone1/team_6_repo.txt). GitHub stores the work and review history; required assignment files must also be submitted through Canvas.

## Planned analyses

These commands describe the intended interface after implementation. They do not yet produce the proposed results on main.

### Feature 1 Time to closure by area and kind

**Question:** Which issue areas and kinds have the longest median elapsed time to closure?

**Module:** `resolution_time_analysis.py` (planned).

**Method:** For eligible closed issues, calculate days from creation to the final recorded closure. Group by current `area/*` and `kind/*` labels. Exclude bot closures and unusable records with explicit exclusion counts. Report the proportion still open separately.

**Input:** None beyond the feature selection.

**Output:** Sorted horizontal bar chart of median days with sample sizes and a console explanation.

```bash
python run.py --feature 1
```

### Feature 2 Contributor profile

**Question:** How does a contributor's activity mix compare with the most active non-bot contributors?

**Module:** `contributor_profile_analysis.py` (planned).

**Method:** Collect the selected user's events, calculate event-type percentages, and compare with the pooled activity of the top 10 active non-bot contributors. Summarize issue areas in which the user participates.

**Input:** GitHub username through `--user`.

**Output:** Stacked percentage bars and a console summary. Unknown users receive a no-activity message. Bot users may be inspected but are excluded from the baseline.

```bash
python run.py --feature 2 --user abn
```

### Feature 3 Time to first response by year

**Question:** How quickly do issues receive their first response, and how has that changed over time?

**Module:** `response_time_analysis.py` (planned).

**Method:** Find the earliest dated non-bot comment by someone other than the issue creator. Calculate elapsed hours and group by issue creation year. Report median time among answered issues and the percentage with no eligible response observed by the dataset cutoff.

**Input:** None beyond the feature selection.

**Output:** Annual response-time and unanswered-share charts with clearly identified units, plus a console summary of leading first responders. The proposal also includes a sensitivity comparison excluding a leading responder's comments; its exact calculation must be agreed before implementation.

```bash
python run.py --feature 3
```

## Setup

Use Python 3 and pip. A tested minimum Python version has not yet been established. Dependencies in `requirements.txt` are python-dateutil, pandas, and matplotlib.

### Clone the repository

```bash
git clone https://github.com/mpoplaw1/Fa26-Group6-project.git
cd Fa26-Group6-project
```

For an existing clone of the team fork under its previous name, run this inside that clone:

```bash
git remote set-url origin https://github.com/mpoplaw1/Fa26-Group6-project.git
```

### Install dependencies

Windows PowerShell:

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
```

macOS or Linux:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

### Configure the dataset

Download `poetry_issues.json` or `poetry_issues_all.json` from Canvas. Keep the dataset outside the repository. Use the full dataset for project-wide results and identify the dataset used when presenting findings.

Set the environment variable in the same terminal used to run the application. Replace the example path with your actual file location.

Windows PowerShell:

```powershell
$env:ENPM611_PROJECT_DATA_PATH = 'C:\path\to\poetry_issues_all.json'
```

macOS or Linux:

```bash
export ENPM611_PROJECT_DATA_PATH='/path/to/poetry_issues_all.json'
```

Alternatively, edit that setting in your local `config.json`. Environment variables take precedence. Do not commit personal paths.

### Run the existing example

From the repository root, with the environment active and dataset configured:

```bash
python run.py --feature 0
```

The template prints an event count and displays a bar chart of top issue creators. A graphical desktop session is needed to view its chart window.

## Application structure

| File | Responsibility | Status |
| --- | --- | --- |
| `run.py` | Parse arguments and dispatch analyses | Existing; features 1-3 need implementation |
| `config.py` | Settings and argument overrides | Existing |
| `data_loader.py` | Load and cache Issue objects | Existing |
| `model.py` | Issue and Event data model | Existing |
| `example_analysis.py` | Demonstrate analysis and chart output | Existing |
| `resolution_time_analysis.py` | Feature 1 | Planned |
| `contributor_profile_analysis.py` | Feature 2 | Planned |
| `response_time_analysis.py` | Feature 3 | Planned |
| `issue_utils.py` | Shared bot, closure-date and response helpers | Planned |

## Interpretation and limitations

- Closure does not necessarily mean successful resolution. Elapsed calendar time does not measure developer effort.
- Current labels may differ from labels at the time of an event. Multi-label issues can contribute to multiple groups.
- Bot identification using a username suffix such as `[bot]` is a heuristic.
- The top 10 active contributors are an activity-based comparison group, not a verified maintainer list. Event types alone do not establish whether comments are answers or questions.
- Unanswered means no eligible response observed within the dataset. Recent issues have shorter observation periods, and 2024 is a partial year in the supplied data.
- Removing one contributor's comments is a sensitivity analysis, not proof of what would happen if that person left.
- Configuration parsing can convert usernames such as `null` and `true` into other types. Feature 2 must preserve the original username string when implemented.

## Team workflow

Create a branch for each independent change, commit meaningful work, and open a pull request targeting this team's main branch for review. Merge approved implementation work before the implementation milestone. Record completed contributions and verified run instructions as work progresses.

Keep datasets, virtual environments, credentials, personal configuration, and temporary outputs out of Git. Keep Mermaid sources and required SVG design deliverables together.

The inherited `.github/workflows/auto-close-pr.yml` is configured to close pull requests opened by anyone other than the repository owner if the workflow is enabled. Review this template workflow before relying on teammate pull requests.
