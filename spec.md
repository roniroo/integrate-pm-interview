# RecoverAI Initiative — Product Spec

**Author:** Veronica Head  
**Date:** 04-07-2026  
**Status:** Draft  
**TL;DR:** When a disruption hits the production floor, AI instantly re-sequences the active job queue and presents a recommended recovery schedule for the planner to review, adjust, and commit — reducing recovery time from hours to minutes.

---

## 1. Problem Statement
Production schedules in manufacturing are fragile — a single unplanned event anywhere in the line cascades immediately into downstream delays.

**The trigger:** A schedule disruption happens--whether that is a downed machine, a delayed delivery of needed parts, or a critical path component failing QC testing and being returned to the manufacturing queue.

**The consequence:** The planner must manually evaluate every downstream dependency, re-sequence jobs across remaining machines, and re-commit delivery dates — a process that currently takes hours.

**The cost:** Every minute of delayed recovery risks missing contractual delivery windows, which carry financial penalties and damage to customer relationships. Planners report this as their single highest-stress workflow event.

---

## 2. User Story

**Primary story:**

As a manufacturing production planner, I want my AI assistant to immediately suggest a revised schedule when a disruption occurs so that I can review, adjust, and recommit in minutes rather than hours -- avoiding costly delays and manual re-sequencing.

---

## 3. Proposed Solution

### 3a. What the AI Does
For v1, re-sequencing uses deterministic constraint-satisfaction logic (Priority → Deadline → FIFO). AI reasoning is applied to the explanation layer — surfacing natural language rationale for each change and flagging non-obvious risk patterns. Full ML-based scheduling is deferred to v2 pending sufficient training data.

**Inputs:**
- Active job queue (job ID, priority, deadline, start date, current stage)
- Resource availability state (which machines/stations are up or down)
- Disruption event type and estimated recovery time

**Logic:**
- Respects process ordering constraints: manufacturing before testing, testing before pack & ship, no exceptions
- Re-sequences jobs using: Priority → Deadline → FIFO (in that order of weight)
- Accounts for resource availability — does not schedule jobs to unavailable machines

**Output:**
- A recommended re-sequenced schedule presented as a diff against the current schedule
- Each change is visible: what moved, where it moved, and why (e.g. "Job 47 moved earlier — higher deadline priority")
- Flagged risk items (e.g. "Pack & Ship bottleneck risk on [date]")

If no valid on-time schedule exists, surfaces the best available option with explicit flags identifying which jobs are at risk and by how much — never silently drops jobs or hides deadline conflicts


### 3b. Where It Intervenes

**Trigger:** A  disruption is detected within the ERP (e.g. delayed delivery of manufacturing materials) or MES (e.g. CNC machine is down), 
OR the production planner logs a disruption

**Intervention point:** Fired when a disruption is logged, taking as inputs: the disruption type, affected jobs, and current schedule state.

**Surface:** A recovery panel slides in alongside the current schedule showing the AI's recommended sequence as a diff. Planner can accept as-is, drag-and-drop to modify, or dismiss and work manually.

### 3c. What the Planner Controls
The AI operates on a read-only snapshot of the current schedule — it cannot write to or modify the live schedule under any circumstance. It does not connect directly to the MES or ERP; it receives only the specific disruption details and affected job data passed to it by the system. Control remains with the human planner, always.
- The planner **always** has final authority over the committed schedule
- AI suggestions are advisory — they do not auto-apply
- The planner can modify any part of the suggested schedule before committing
- The planner can dismiss the suggestion entirely and work manually
- No schedule change takes effect on the floor until the planner explicitly commits it

---

## 4. Out of Scope (MVP)
We have intentionally chosen not to focus on schedule creation or planning and hone in on disruptions and teh resequencing flow. This is an intentional choice, as we know we have to build trust with planners before we cn introduce additional feature sets and inform the live schedule. Getting feedback loops with the rescheduling and diffs between original human made schedules and AI informed re-routes is helpful for training LLMs.

| What | Why Deferred |
|------|-------------|
| **Planner feedback capture on schedule modifications** | Feedback loops require sufficient usage volume and planner trust before the signal is meaningful. Introducing before v1 is validated risks low-quality data and added friction at the highest-stress moment. Targeted for v2. |
| **Schedule planning assistance** | Planners carry institutional knowledge about customer relationships, contract nuances, and shop floor realities that no model can reliably encode at this stage. AI-assisted schedule creation requires a trust foundation that must be earned through the disruption recovery workflow first. Targeted for v3. |
| **Automated schedule commitment (no planner review)** | Defense manufacturing context requires human-in-the-loop for all schedule changes. Auto-commit creates unacceptable risk of AI errors propagating to the production floor without oversight — with potential contractual and safety consequences.|
| **Proprietary schedule format or lock-in** | The system accepts schedules in flexible formats to meet planners where they are. Enforcing a proprietary format would create adoption friction and contradict the product's human-first philosophy. |

---

## 5. Success Metrics

### Leading Indicators (measurable within 30 days)

- **Delta between AI suggestion and committed schedule | _70-80% changes accepted, less than 20% change_**: Showing how much the human scheduler editted the AI's suggestion, indicating if AI response was useful and accurate. Also ensuring that planners are actively reviewing rather than rubber-stamping AI suggestions.
- **Time from disruption to recovery | _30 min or less_**: Showing how long it takes to recover the schedule when a disruption occurs. We assume there will be a learning curve and trust barrier with the new tool. With a current baseline of ~2 hours, we want to see a significant but not complete reduction. 

### Lagging Indicators (measurable within 90 days)

- **On Time delivery rate on affected work orders | _Increased +20%_**: A significant yet ambitious increase in on-time delivery when there are impacts.
-- _Note: on-time delivery is influenced by factors outside this feature — supplier reliability, machine maintenance, contract changes. Attribution should be measured on disruption-affected orders specifically, not overall delivery rate._
- **Time from disruption to recovery | _15 min or less_**: Pushing the envelope on recovery time to a more ambitious level and showing increased comfort in the tool and its usage.
- **Feature adoption rate | _>80% of disruptions used AI_**: Measures whether planners are reaching for the AI assistant when disruptions occur or reverting to manual workflows.


---

## 6. Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| **Planner distrust** — AI suggests a schedule that a planner knows is wrong, eroding confidence | Medium | High | Show reasoning behind each suggestion ("Job 47 moved because deadline is 3 days earlier than Job 52"). Make it easy to override without friction. |
| **Data quality** — job priorities or deadlines not maintained accurately in the system | High | High | Validate data completeness before surfacing AI suggestions. Surface a warning if key fields are missing rather than suggesting on incomplete data. |
| **No valid schedule exists** — AI cannot find a sequence that meets all deadlines | Low | High | Graceful fallback: surface the best available schedule with explicit flags on which jobs are at risk of missing deadlines. Never silently drop jobs from the suggestion. |
| **Change management** — shop floor supervisors bypass the tool and call planners directly | Medium | Medium | Involve at least one supervisor in user testing before launch. Make the committed schedule visible to supervisors in read-only view. |
| **AI error propagates to floor** — planner accepts suggestion without reviewing carefully | Medium | High | Require explicit commit action. Consider a confirmation step that shows the delta from current schedule before committing. |

---

## 7. Open Questions

1. **How are job priorities currently maintained?** If priority is set manually by planners and rarely updated, the AI's triage logic will reflect stale data. Do we need a priority hygiene feature before this is useful?

2. **What do we do if pack and ship goes down?** This is the station with the most risk because it has no redundancy. Are there any other options to get up and running? If not, is our only option to move everything else along until pack and ship is recovered?

3. **What is the expected integration effort to receive disruption data and schedule snapshots from the customer's ERP and MES infrastructure?** This is a critical technical dependency that must be scoped before committing to a launch timeline.