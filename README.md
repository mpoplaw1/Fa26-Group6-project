# ENPM611 Fall 2026 Group 6 Project

Team 6 is extending the course Python application to analyze Poetry GitHub issue activity: time to closure, contributor activity, and time to first response.

This is our shared fork of the [ENPM611 application template](https://github.com/enpm611/project-application-template).

## Team

| Member | GitHub | Milestone 1 responsibility |
| --- | --- | --- |
| Marcin Poplawski | [mpoplaw1](https://github.com/mpoplaw1) | Sequence diagram and repository |
| Ashish Bhoya | [11Ashish11](https://github.com/11Ashish11) | Component diagram after changes |
| Mahima Shenoy | [s-mahima09](https://github.com/s-mahima09) | Component diagram before changes |

Marcin implements and tests Feature 1 (time to closure) and the shared issue helpers. Features 2 and 3 remain for team integration.

## Current status

This version contains the course template, all three Milestone 1 diagrams, shared issue helpers, and Feature 1 (time to closure). Feature 0 remains the provided example. Features 2 and 3 are not implemented in this version; selecting either reports an error instead of silently doing nothing.

Milestone 1 diagrams are available in docs/milestone1 on main and linked below. These describe the existing architecture and planned changes.

## Milestone 1 Design

| Artifact | Mermaid source | Image |
| --- | --- | --- |
| Component diagram before changes | [TXT](docs/milestone1/team_6_component_diagram_before.txt) | [SVG](docs/milestone1/team_6_component_diagram_before.svg) |
| Component diagram after changes | [TXT](docs/milestone1/team_6_component_diagram_after.txt) | [SVG](docs/milestone1/team_6_component_diagram_after.svg) |
| Contributor profile sequence diagram | [TXT](docs/milestone1/team_6_sequence_diagram.txt) | [SVG](docs/milestone1/team_6_sequence_diagram.svg) |

The sequence diagram follows `python run.py --feature 2 --user abn`, from configuration and data loading through contributor comparison and output.

[Repository URL submission file](docs/milestone1/team_6_repo.txt). GitHub stores the work and review history; required assignment files must also be submitted through Canvas.

## Analyses

Feature 1 is implemented below. Features 2 and 3 describe the planned interfaces.

### Feature 1 Time to closure by area and kind

**Question:** Which issue areas and kinds have the longest median elapsed time to closure?

**Module:** `resolution_time_analysis.py`.

**Method:** For eligible closed issues, calculate days from creation to the final recorded closure. Group by current `area/*` and `kind/*` labels. Exclude bot closures and unusable records with explicit exclusion counts. Report the proportion still open separately.

**Input:** None beyond the feature selection.

**Output:** Sorted horizontal bar charts of median days with sample sizes, CSV label statistics, JSON summary and exclusion counts, and a console explanation.

```bash
python run.py --feature 1
```

By default, files are saved in `outputs/resolution_time/` without opening chart windows:

- `closure_by_area.png` and `.svg`
- `closure_by_kind.png` and `.svg`
- `closure_by_label.csv`
- `summary.json`

Use `python run.py --feature 1 --output-dir outputs/my-run` to select another directory. Add `--show` to display charts on a graphical desktop. The `--user` and `--label` options are reserved for other analyses and do not filter Feature 1.

Elapsed days use UTC timestamps and 86,400 seconds per day; timestamps without a time zone are assumed UTC. Only currently closed issues with a usable final closure are eligible. A reopening at or after the last closure, or an undated closure/reopening, makes the closure unusable. Missing creation dates, missing closing authors, bot final closures, and negative durations are also excluded. A zero-day duration is valid. The bot rule checks a case-insensitive `[bot]` suffix.

Open percentages use all issues in the dataset (or all issues carrying a label for the per-label percentage), not just eligible closed issues. Current duplicate labels are counted once per issue; an issue with several labels contributes to several groups. Eligible issues without `area/` or `kind/` labels are counted separately. Groups with no eligible closures have a blank CSV median and JSON `null`; they are omitted from median bars. Empty datasets still produce tables and placeholder charts. Closure-exclusion subcounts explain `unusable_closure`; they are not additional exclusions.

The JSON report records the dataset path for provenance. Generated reports are ignored by Git; check that path before sharing reports separately.

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

**Module:** `response_time_analysis.py` (class `ResponseTimeAnalysis`).

**Method:**

- For each issue, find the first reply from someone other than the person who opened it. Bot accounts (names ending in `[bot]`) don't count. This uses `get_first_response()` from `issue_utils.py`.
- Measure the hours between the issue being opened and that first reply.
- Group issues by the year they were opened.
- For each year, show the typical wait (the median) and the share of issues that got no reply.
- Issues missing an opening date or an author are left out and counted separately. They are not counted as unanswered.

**Extra check:** For each year, the analysis names the person who replied first most often. It then works out what the typical wait would have been without their replies. This shows how much the project depends on one person. The original data is not changed.

**Input:** None. Just run the command.

**Output:**

- On screen: the question, how it was answered, a table for each year, the five people who replied first most often, and a one-line finding.
- Saved in `outputs/response_time/`: a line chart (`first_response_by_year.png` and `.svg`) with two lines, typical wait in hours and % with no reply, plus the numbers as `first_response_by_year.csv` and `summary.json`.

```bash
python run.py --feature 3
```

Add `--show` to open the chart in a window as well. To save the files somewhere else, add `--output-dir <folder>`. You can also run this feature on its own with `python response_time_analysis.py`.

**Finding (full dataset):** The typical wait for a first reply dropped from about 21 hours in 2018 (and 108 hours in 2019) to about 1 hour from 2022 onward. Each year, 7% to 14% of issues got no reply. Since 2023, one volunteer (`dimbleby`) gave about three out of four first replies. Without his replies, the typical wait would be about 10 to 12 hours.

**Limitations:**

- Any comment counts as a reply, even if it doesn't help.
- 2024 is only a partial year, and newer issues have had less time to get a reply.
- Bots are spotted by name only, so some may be missed.
- The "without the top replier" number shows how much the project relies on that person. It does not prove what would have happened without them.

Tests: `python -m unittest tests.test_response_time -v`

## Setup

Use Python 3 and pip. Feature 1 has been tested with Python 3.13 on Windows; a minimum supported version has not been established. Dependencies in `requirements.txt` are python-dateutil, pandas, and matplotlib.

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
| `run.py` | Parse arguments and dispatch analyses | Features 0 and 1 connected |
| `config.py` | Settings and argument overrides | Existing |
| `data_loader.py` | Load and cache Issue objects | Existing |
| `model.py` | Issue and Event data model | Existing |
| `example_analysis.py` | Demonstrate analysis and chart output | Existing |
| `resolution_time_analysis.py` | Feature 1 | Implemented |
| `contributor_profile_analysis.py` | Feature 2 | Planned |
| `response_time_analysis.py` | Feature 3 | Implemented |
| `issue_utils.py` | Shared bot, closure-date and response helpers | Implemented; see [helper contract](SHARED_HELPERS.txt) |

## Testing Feature 1 and shared helpers

After installing the runtime dependencies, run from the repository root:

```bash
python -m unittest discover -s tests -v
```

The 26 tests include 14 closure-analysis tests, 10 shared-helper tests, and two CLI integration tests. Integration tests create temporary synthetic JSON datasets, invoke Feature 1, and check the numerical CSV/JSON results and PNG/SVG artifacts. No course dataset or network access is needed. Coverage includes final closures after reopening, bot closures, UTC offsets, zero and negative durations, missing dates, duplicate labels, open-issue denominators, medians, and empty data.

The inherited loader expects the course JSON schema. Invalid issue states or malformed JSON can stop loading; the CLI reports a failure rather than claiming those records were analyzed. The `unknown_state` unit case exercises the pure calculation with an already constructed object.

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

The inherited auto-close workflow is restricted to the original course repository and does not close pull requests in this team fork.
