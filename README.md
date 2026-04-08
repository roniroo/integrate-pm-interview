# RecoverAI — Production Scheduling Prototype

Interactive prototype demonstrating an AI-assisted disruption recovery workflow for defense manufacturing production scheduling.

## What this is

A single-page HTML demo simulating a production planner's workflow when a disruption hits the factory floor. The AI re-sequences the job queue using deterministic constraint-satisfaction logic (Priority > Deadline > FIFO) and presents a recovery plan for the planner to review and commit.

No real AI, no backend, no external dependencies. All logic and data are hardcoded in a single HTML file.

## Repo structure

```
.
├── demo/
│   └── index.html        # The interactive prototype (open in browser)
├── spec.md               # Product spec for the RecoverAI feature
├── process.md            # Process documentation
└── README.md
```

## Running locally

No build step, no install, no server required. Open the file directly in a browser:

```bash
open demo/index.html
```

Or on Linux:

```bash
xdg-open demo/index.html
```

Or just double-click `demo/index.html` in your file manager.

Tested in Chrome and Safari. Any modern browser works.

## Demo walkthrough

The prototype has four states:

1. **Normal view** — Job queue table with 8 sample jobs, resource status bar showing all machines online. Pack & Ship is flagged "No Redundancy" as a structural risk.

2. **Log disruption** — Click "Log Disruption" in the header. A modal presents three disruption scenarios:
   - CNC Machine 1 unplanned downtime (primary scenario)
   - Delayed material delivery for WO-1045
   - QC test failure for WO-1047

   Each scenario drives its own affected jobs, resource changes, and recovery logic.

3. **AI recovery panel** — A side panel slides in showing:
   - AI reasoning summary (which jobs are affected, what changed, what's at risk)
   - Diff view with old vs. new machine assignments and plain-English reasoning per job
   - Pack & Ship bottleneck warning (contextual per scenario)
   - Three planner actions: Accept, Modify, Dismiss

4. **Committed** — Clicking "Accept Schedule" updates the job table, closes the panel, and shows a confirmation banner. The planner must explicitly commit; nothing auto-applies.

## Factory constraints (hardcoded)

| Resource | Count | Notes |
|----------|-------|-------|
| CNC Machines | 2 | Manufacturing stage |
| Test Stations | 2 | Testing stage |
| Pack & Ship | 1 | Single point of failure, highest bottleneck risk |

Process order is strictly: Manufacturing > Testing > Pack & Ship. No stage can be skipped.

## Tech

- Single HTML file, vanilla JS and CSS
- No frameworks, no build tools, no npm
- No external API calls
- All scheduling logic is deterministic, hardcoded in the `SCENARIOS` object in the `<script>` block
