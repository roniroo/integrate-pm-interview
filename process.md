# Outline
Create a summary.md file covering:
• What you built and the key tradeoffs you made
• How you used AI in your design and build process — what tools, what prompts, what
worked and what didn’t
• What you would do differently with more time
• One hard question you’d want to answer before shipping this to a production planner on
a live program

# Flow

How I moved through this process:
1. **Research** - Understanding the operating space and how our target audience is currently operating. Looking to answer, what is the status quo and what pains come out of that?
2. **Problem Space** - With a lay of the land established, really seeking to understand and narrow down a problem that we are well positioned and opinionated enough to solve.
3. **Solution & MVP Spec** - Craft a right-sized solution to the problem with a clear understanding of what is and is not MVP. Have an opinion and back it up with a strategy.
4. **Metrics** - How do we know if this is successful
5. **Prototype** - With a hard hour cap, had Claude Code create a prototype artifact from spec & prompt. Gave it feedback and also made sure I understood what it was doing. it's not a total back box. Goal is to come out with an artifact that gives us something demo-able for user, stakeholder, & engineering feedback.
6. **Reflection** - Round out this exercise and muse next steps & open questions.

# What I Built
A simple UI that displays a schedule and then runs 4 disruption scenarios to simulate how the AI would react.

# How I Used AI, and Didn't

I did not use an ounce of AI in this document, as I want it to be my _actual_ undisturbed human thoughts and a genuinely pure record of my thinking. I did use AI in my process! Revisiting the flow from above...

## AI Setup
I find it's always beneficial to start a project to hold prompts & context (and build memory within Claude). I've tried a few AI stacks and am currently on a Claude kick. Have tried Replit, Cursor, ChatGPT, Gemini, and Perplexity. Would be happy to chat through this over a beer (NA or A!) sometime. For the sake of brevity, for this exercise:
- Set up a project in Claude, gave it your PDF prompt and instructions (see below)
- Created a GitHub repo & init, added the file structure
- Set up a project in Perplexity AI running Claude Sonnet 4.6 with different instructions and the same prompt --  redundant (unconventional? a little wonky?) but allows me to switch LLMs and see a different POV. I asked this one to essentially be the persona of the hiring manager (hi Andrew!) and be a critical yet helpful mentor to me
- Cloned GitHub Repo and opened in Cursor -- tbh mostly using Cursor as an IDE, rarely using its agent and I don't pay for it
- Running Claude Code in Cursor to do development

Tools:
- Claude & Claude Code
- Perplexity
- Cursor

### Claude Instructions
Act as a peer and strategic advisor in a mentor capacity as a Head of Product helping to guide me in this work.

I want to ace my interview, here is the take home task: You are a PM at a defense manufacturing software company. Your platform is used by
production planners and shop floor supervisors responsible for scheduling the intake of raw
components, assembly, testing, and shipment of completed parts — often under strict contractual delivery timelines, limited machine availability, and complex dependency chains.
Your team has been asked to design an AI-assisted scheduling feature to help planners reduce delays, resolve conflicts, and improve on-time delivery rates.

Be my thought partner in developing the spec and product, and help me be strategic and pick apart my ideas with a critical and helpful lens.

### Perplexity Instructions
You are a skilled startup head of product at a growing defense project management company. You are also a wonderful mentor to me and you want to see me succeed. You are not afraid to give criticism to help others improve and refine their ideas. In addition to having a strong product background, you have also done design and have a keen eye for it.

## Where AI Was and Was _Not_ Used

I used AI to start my research. I am already familiar with MRPs through my job but wanted to gain a better understanding of MES and ARP systems as well as ERPs and how they intermingle. I also wanted to know more about the aerospace and defense landscapes.

After the initial ask, I chose where to dig in and what I wanted to Google to learn more. I dug into Spaceforce's move away from a legacy DOS system from 1992 because it's a significant signal -- the dinosaurs are moving away from old and bad software, and the government is using AI. That's a big deal. I also know that Spaceforce is a big deal for Integrate.

After getting a grasp of what exists, I dove into writing the spec. I have a conversation with Claude (https://claude.ai/share/51db4f68-3212-4089-b45f-791f7b7d08d7). Mused about what I was thinking and what I envisioned building. I asked for a spec outline, based on what I had said and what you (Integrate) want.

I put the outline spec.md in my Cursor and then manually edited every section 1:1. First filling in the gaps then going back and forth with Claude and Perplexity, sometimes alternating between the two and asking it to pick sections apart. I did this until spec was done.

Then, I used Claude to write a prompt based on the spec. Then I asked perplexity to pick it apart. Then asked Claude what it thought.

I removed a line from both that said not to use any CSS frameworks, that seemed silly to me, don't reinvent the wheel.

Then, I had Claude Code build it from the prompt w/ access to the spec. It built, I ran in local, I did bug fixing with it.
- Toast that was in the way and not dismissible
- Not displaying right disruption source in test scenarios
- Of course, I had to ask it to remove emojis
- I asked it if it was purposely showing the pack and ship module on every scenario and it affirmed yes it was intentional

Then, I did the most human thing I could have done, I slept on it.

The next morning I looked at the UI with fresh eyes and suggested a few edits to improve the user experience.
- Show suggested changes in schedule table as new line, not just on the sidebar so changes are more clear
- Added an estimated finish date as that's critical info for the planner
- Added a sub-assembly of work order field and dependency logic as that's more realistic and a big question to consider in planning

# What Would I Do Differently With More Time

I'd spend more time on the upfront user and landscape research. It feels like a big risk not to fully understand this aspect from a strategic standpoint and having this understanding is a competitive advantage over others. Whenever I am developing I product, I seek to develop a deep user empathy and immersive understanding of the landscape so I can be strategic.

# The Biggest Question

**Can this proposed new feature integrate seamlessly with our Meridian MES & APS?** If it can't or if that presents a timeline barrier, then this feature is a non-starter IMHO. The feature has no value and no function without integrating into the existing infrastructure. If it can't do that, it has no legs, and then secondary questions like will planners use it or trust it are meaningless.

# **Appendix**

# App Prompt
You are building a lightweight HTML/JS prototype for a product demo. 
This is not production code — it should look credible and make a concept 
tangible for a non-technical audience.

## What you are building
A single-page web app simulating an AI-assisted production scheduling 
tool for a defense manufacturing floor called "RecoverAI". The scenario: 
a production planner manages a job queue across constrained factory 
resources. When a disruption occurs, an AI assistant suggests a recovery 
schedule for the planner to review and commit.

The AI does two distinct things:
1. Deterministic constraint-satisfaction logic to re-sequence jobs
   (Priority → Deadline → FIFO)
2. Natural language explanation of each change — surfacing reasoning 
   the planner can read, evaluate, and override

The planner always stays in control. The AI is advisory only.

## Factory constraints (hardcode these)
- 2 CNC Machines (Manufacturing stage)
- 2 Test Stations (Testing stage)
- 1 Pack & Ship Station — single point of failure, highest bottleneck risk
- Process order is strictly: Manufacturing → Testing → Pack & Ship
- A job cannot enter testing until manufacturing is complete
- A job cannot be packed until testing is passed

## The job queue (hardcode 8 sample jobs)
Give each job:
- Job ID (e.g. WO-1042)
- Part name (realistic defense part names e.g. "Actuator Housing", 
  "Guidance Bracket", "Servo Mount Plate")
- Priority (Critical / High / Standard)
- Deadline (dates in the next 2-3 weeks)
- Current stage (Manufacturing / Testing / Pack & Ship / Queue)
- Assigned machine
- Status (On Track / At Risk)

Design the queue so that:
- A CNC machine going down creates a meaningful cascade across 3-4 jobs
- At least 2-3 jobs are converging on Pack & Ship around the same date,
  creating a visible bottleneck risk even before the disruption

## The demo flow

### State 1 — Normal view
Show the job queue as a clean table with status indicators.
Include a resource status bar at the top showing all machines 
as green/online (CNC 1, CNC 2, Test Station 1, Test Station 2, 
Pack & Ship).
Add a "Log Disruption" button in the header.
Pack & Ship should have a subtle "No Redundancy" label — 
foreshadowing its risk without alarming.

### State 2 — Disruption triggered
When the planner clicks "Log Disruption", show a modal to choose 
disruption type. Pre-populate with:
- "CNC Machine 1 — Unplanned Downtime" (primary scenario)
- "Delayed Material Delivery — WO-1045"
- "QC Test Failure — WO-1047"
CNC Machine 1 going down is the primary demo scenario.
After selection, show CNC Machine 1 turning red in the resource 
status bar and affected jobs highlighting in the queue.

### State 3 — AI recovery panel
After disruption is logged, slide in a recovery panel alongside 
the schedule. This panel should show:

- A brief AI reasoning summary at the top:
  "CNC Machine 1 is offline. 3 jobs affected. Suggested recovery 
  re-sequences to CNC Machine 2 where capacity allows. 
  2 jobs flagged at deadline risk. Pack & Ship bottleneck 
  risk detected — 3 jobs converging on [date]."

- A diff view of the suggested schedule showing changed rows 
  highlighted, with old vs new assignment clearly visible

- A natural language reasoning label on each changed job:
  e.g. "Reassigned to CNC 2 — earliest available capacity"
  e.g. "Moved earlier in queue — Critical priority, deadline in 3 days"
  e.g. "⚠ At risk — CNC 2 capacity insufficient to meet deadline"

- A prominent bottleneck warning card:
  "⚠ Pack & Ship Bottleneck Risk — [date]
   3 jobs scheduled to complete testing within 24hrs of each other.
   Pack & Ship has no redundancy. Review sequencing to stagger 
   arrivals or flag for supervisor review."

- Three planner action buttons: 
  "Accept Schedule", "Modify", "Dismiss"

### State 4 — Committed
When planner clicks "Accept Schedule":
- Schedule table updates to reflect the new sequence
- Recovery panel closes
- Success banner: "Schedule committed — floor notified"
- Resource status bar updates CNC 1 to show as offline 
  but schedule now reflects workaround

## AI logic (deterministic, no real AI needed)
Re-sequence using:
1. Priority (Critical → High → Standard)
2. Deadline (earliest first)
3. FIFO (original order as tiebreaker)

Reassign affected jobs from CNC Machine 1 to CNC Machine 2 
where capacity allows. Flag any job that cannot be reassigned 
without missing its deadline. Generate plain-English reasoning 
for each change as a hardcoded string — it should read naturally, 
not like a formula output.

## Design requirements
- Clean, professional enterprise UI — not a consumer app
- Neutral color palette: use red for disruption/critical risk, 
  amber for warnings and bottleneck flags, green for on track
- The diff view and bottleneck warning are the two most important 
  UI elements — make them scannable and impossible to miss
- Pack & Ship bottleneck warning should feel more urgent than 
  other flags given it has no redundancy
- Mobile responsiveness not required
- Single HTML file, pure vanilla JS and CSS, no frameworks, 
  no external dependencies, no backend

## What to avoid
- Do not over-engineer — demo only
- Do not add features not listed above
- The AI does not call any external API
- Do not auto-commit anything — every schedule change 
  requires explicit planner action