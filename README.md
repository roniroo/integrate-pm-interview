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

1. **Normal view** — Job queue table with 8 sample jobs across manufacturing, testing, and pack & ship stages. Resource status bar shows all machines online. Pack & Ship is flagged "No Redundancy" as a permanent structural risk. Table includes estimated finish dates and sub-assembly dependencies between jobs.

2. **Log disruption** — Click "Log Disruption" in the header. A modal presents three disruption scenarios, each with its own recovery logic:

   | Scenario | What happens |
   |----------|-------------|
   | CNC Machine 1 — Unplanned Downtime | CNC 1 goes offline in resource bar. 4 jobs reassigned to CNC 2 or held. Downstream dependency (WO-1046) flagged due to delayed sub-assembly. |
   | Delayed Material Delivery — WO-1045 | Resources stay green. WO-1045 put on hold pending material. WO-1049 pulled forward to CNC 1 to fill freed capacity. |
   | QC Test Failure — WO-1047 | Resources stay green. WO-1047 returned to manufacturing for rework on CNC 2. Pack & Ship bottleneck partially relieved (2 jobs converging instead of 3). |

3. **AI recovery panel** — A side panel slides in showing:
   - AI reasoning summary (affected jobs, deadline risks, dependency impacts)
   - Diff view with old vs. new assignments and plain-English reasoning per job
   - Inline suggestions in the main table under affected fields (machine, est. finish, status)
   - Pack & Ship bottleneck warning (contextual per scenario)
   - Three planner actions: Accept, Modify, Dismiss

4. **Committed** — Clicking "Accept Schedule" updates the job table with new assignments and estimated dates, closes the panel, and shows a dismissible confirmation banner. The planner must explicitly commit; nothing auto-applies.

## Job queue

8 hardcoded jobs with two sub-assembly relationships that create critical path dependencies:

- WO-1043 (Guidance Bracket) is a sub-assembly of WO-1046 (Gimbal Ring Assembly)
- WO-1044 (Servo Mount Plate) is a sub-assembly of WO-1048 (Pressure Valve Block)

When a disruption delays a sub-assembly, the parent job is flagged as impacted downstream.

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
