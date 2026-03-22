# PFC-DSY-PLAN — Workshop Delivery Rundown

**Date:** 2026-03-22 (TONIGHT)
**Status:** DELIVERY DAY
**Facilitators:** Amanda (tech lead / demo) + Milana (narrative / audience engagement)
**Source plan:** `PFC-DSY-PLAN-Workshop-Patterns-Graphs-Agents-v1.0.0.md`

---

## What We Have (Ready)

| Asset | Location | Status |
|-------|----------|--------|
| Full session plan + runsheet | `pfi-w4m-dev/docs/PFC-DSY-PLAN-Workshop-Patterns-Graphs-Agents-v1.0.0.md` | DONE |
| OAA Workbench (live demo) | [GitHub Pages](https://ajrmooreuk.github.io/Azlan-EA-AAA/PBS/TOOLS/ontology-visualiser/browser-viewer.html) | LIVE |
| VP-ONT mini ontology JSON | In plan doc (Step 1 of THE BUILD) | DONE |
| Skill definitions (Skill 1 + 2) | In plan doc (Step 2 of THE BUILD) | DONE |
| Agent orchestrator prompt | In plan doc (Step 3 of THE BUILD) | DONE |
| Backup business ideas (x3) | In plan doc | DONE |
| Toolkit folder structure + all content files | In plan doc (THE TAKE section) | CONTENT DONE — needs packaging |
| Women-in-tech research PDFs | `~/Downloads/` (Europe gender gap + STEM analysis) | REFERENCE |

---

## What Needs Doing NOW

### PRIORITY 1 — Slide Deck (Amanda)

Draft in Claude AI (`claude.ai` Projects):

**How to do it:**
1. Open Claude AI → create a new Project called "WIT Workshop — Patterns, Graphs & Agents"
2. Upload the full plan MD as a Project file (knowledge base)
3. Prompt Claude to generate slides section by section (see prompts below)

**Slide deck structure — 12-15 slides max:**

| # | Slide | Content | Notes |
|---|-------|---------|-------|
| 1 | Title | "Build an Agent in an Hour" / Patterns, Graphs & Agents | Amanda + Milana names, event branding |
| 2 | The Pattern Thread | Euler (1736) → Bowie (1970s) → LLM (now) | Three images, one line each — conversational not academic |
| 3 | Euler's Bridges | Seven Bridges of Konigsberg visual | *"He stopped looking at bridges and looked at connections"* |
| 4 | Bowie's Cut-Ups | Fragment → recombine → meaning | *"Genius from fragmentation and recombination"* |
| 5 | The LLM Connection | Tokens = fragments, patterns at scale | *"Not magic. Pattern recognition."* |
| 6 | Live Demo | "Everything is a Graph" — OAA Workbench | SWITCH TO LIVE DEMO (slide = fallback screenshot) |
| 7 | What Makes an Agent | Chatbot vs Agent distinction | One-liner: *"A chatbot answers questions. An agent does work."* |
| 8 | The Agent Recipe | The anchor diagram (Skills + Graph + Orchestration) | This is THE slide — redraw clean |
| 9 | Three Ingredients | Skills / Knowledge Graph / Orchestration table | With the "without it..." column |
| 10 | Let's Build | Step 1-2-3-4 flow | *"By the end, YOU will have built one"* |
| 11 | The Knowledge Graph | VP-ONT — 3 nodes, 3 edges | Show the JSON (simplified) + visualiser screenshot |
| 12 | The Skills | Skill 1 + Skill 2 cards | Input → Process → Output |
| 13 | Wire It Together | Agent prompt | *"Three files. No code."* |
| 14 | Your Toolkit | QR code + folder structure | What they take away |
| 15 | The Close | *"Euler saw it in bridges. Bowie heard it in fragments. Now you can too."* | Milana delivers |

**Claude AI prompt to generate slides:**

```
I'm running a 60-minute workshop tonight called "Build an Agent in an Hour"
for women in tech — mixed technical backgrounds. I need a slide deck.

Here's the full session plan [already uploaded as project knowledge].

Generate the content for each slide as markdown. For each slide give me:
- Title
- 1-3 bullet points or a key quote (MAX — these are visual aids not essays)
- Speaker notes (what Amanda or Milana says)
- Any visual suggestion (diagram, image, screenshot)

Keep slides minimal — more whitespace, less text. The energy comes from
Amanda and Milana, not the slides.
```

**Then export:** Copy slide content into Google Slides / Keynote / Canva — or ask Claude to format as a Mermaid-based deck if presenting from browser.

---

### PRIORITY 2 — Toolkit Package (Amanda)

Create the actual toolkit folder for attendees:

```bash
mkdir -p ~/workshop-toolkit/{examples,extend-it,resources}
```

All file contents are already written in the plan doc — just extract into real files:

| File | Source |
|------|--------|
| `README.md` | Plan doc → THE TAKE → README.md Content |
| `teaser-script.md` | Extract THE HOOK section |
| `mini-ontology.json` | Plan doc → Step 1 JSON block |
| `skills.md` | Plan doc → Step 2 skill definitions |
| `agent-prompt.md` | Plan doc → Step 3 orchestrator prompt |
| `examples/idea-strong.md` | Run the agent prompt in Claude with backup idea #1, save output |
| `examples/idea-weak.md` | Run the agent prompt with a deliberately vague idea, save output |
| `examples/idea-pivot.md` | Run the agent prompt, show pivot recommendation |
| `extend-it/add-a-skill.md` | Plan doc → THE TAKE section |
| `extend-it/add-ontology-nodes.md` | Plan doc → THE TAKE section |
| `extend-it/chain-patterns.md` | Plan doc → THE TAKE section |
| `resources/further-reading.md` | Plan doc → THE TAKE section |
| `resources/claude-quickstart.md` | Write: sign up at claude.ai, paste the prompt, try it |
| `resources/graph-thinking.md` | Write: mental models for thinking in graphs |

**Hosting:** Push to a public GitHub repo or zip and host on Google Drive → generate QR code.

---

### PRIORITY 3 — Substack Article (Milana or Amanda)

Draft in Claude AI for post-event publishing:

**Claude AI prompt:**

```
I just ran a workshop called "Build an Agent in an Hour" for a women in tech
group. The narrative thread was pattern recognition — from Euler's bridges
to Bowie's cut-ups to LLMs.

Write a Substack article (1200-1500 words) that:
1. Opens with the Euler/Bowie hook (grab attention)
2. Explains what we built (VP Agent — graph + skills + orchestration)
3. Makes the case that AI agents are accessible NOW — no code needed
4. Includes the "three ingredients" framework as a takeaway
5. Ends with a call to action: try it yourself (link to toolkit)

Tone: smart, warm, confident. Not corporate. Not dumbed down.
Write for women who are ambitious and curious but may not have a CS degree.
```

**Pre-draft now, refine after the event** with real audience reactions, quotes, photos.

---

### PRIORITY 4 — Milana Briefing

Send Milana a condensed briefing pack:

| Item | What to send |
|------|-------------|
| **Runsheet** | The full plan MD (or a 1-page summary) |
| **Her lines** | Extract all Milana-tagged lines from the plan |
| **Backup ideas** | The 3 backup business ideas + which VP node each is weak on |
| **Closing script** | THE TAKE section — she leads the close |
| **QR code / link** | For toolkit distribution |
| **Bowie lyrics** | 3 printed copies if doing the cut-up prop |

**Key Milana moments to rehearse:**
- Room energy reads during THE SEE (when to prompt Amanda to skip/breathe)
- Seeding business ideas if audience is shy
- The personal angle: *"Think about YOUR expertise. What could be a graph?"*
- Closing line delivery

---

## Pre-Session Tech Checklist (from plan)

- [ ] OAA Workbench loads on venue WiFi
- [ ] VP-ONT pre-loaded in Tab 1
- [ ] Full registry pre-loaded in Tab 2 (cached)
- [ ] Claude AI open with agent prompt ready to paste
- [ ] Screen sharing / projector tested
- [ ] QR code for toolkit ready (printed or on slide)
- [ ] Bowie lyrics printed x3 (if doing cut-up prop)
- [ ] Mobile hotspot as WiFi fallback
- [ ] 3 backup business ideas memorised

---

## Timeline — What to Do When

| When | Who | What |
|------|-----|------|
| **NOW** | Amanda | Draft slide deck in Claude AI (Priority 1) |
| **+30 min** | Amanda | Extract toolkit files from plan doc (Priority 2) |
| **+1 hr** | Amanda | Draft Substack article shell in Claude AI (Priority 3) |
| **+1.5 hr** | Amanda | Send Milana her briefing pack (Priority 4) |
| **+2 hr** | Amanda | Run through the demo once end-to-end (rehearsal) |
| **+2.5 hr** | Amanda + Milana | Quick call — walk the runsheet, confirm roles |
| **-1 hr** | Amanda | Arrive at venue, test tech, pre-load tabs |
| **-15 min** | Amanda + Milana | Final check — energy, props, backup plan |
| **GO** | Both | Deliver |

---

## Post-Event (Tonight / Tomorrow)

- [ ] Collect "3 words" feedback from attendees
- [ ] Photo of cut-up exercise (social content)
- [ ] Share toolkit link with all attendees (Milana owns)
- [ ] Refine Substack draft with real quotes/reactions
- [ ] Publish Substack article within 48 hours
- [ ] Follow-up message to attendees (48 hr) — toolkit reminder + one extra resource
- [ ] Capture any agent ideas from audience for future workshops

---

## All Assets — Quick Links

| Asset | Location |
|-------|----------|
| Session plan | `pfi-w4m-dev/docs/PFC-DSY-PLAN-Workshop-Patterns-Graphs-Agents-v1.0.0.md` |
| This delivery rundown | `pfi-w4m-dev/docs/PFC-DSY-PLAN-Workshop-Delivery-Rundown-v1.0.0.md` |
| OAA Workbench | https://ajrmooreuk.github.io/Azlan-EA-AAA/PBS/TOOLS/ontology-visualiser/browser-viewer.html |
| Women-in-tech research | `~/Downloads/women-in-tech-and-ai-in-europe-can-the-region-close-its-gender-gap.pdf` |
| STEM analysis | `~/Downloads/women_stem_ai_analysis_20250624203207.pdf` |
| Slide deck | Draft in Claude AI Projects → export to Keynote/Slides/Canva |
| Substack article | Draft in Claude AI → refine post-event → publish |
| Toolkit package | Extract from plan doc → host on GitHub/Drive → QR code |

---

---

## Anthropic Resources — Agent Orchestration & Skills (for demo + reference)

### Official Guides & Articles

| Resource | What it is | Use for |
|----------|-----------|---------|
| [Building Effective Agents](https://www.anthropic.com/research/building-effective-agents) | Anthropic's definitive guide to agent architectures — prompt chaining, orchestrator-workers, evaluator-optimizer patterns | **THE WIRE section** — reference for explaining orchestration patterns to audience |
| [Equipping Agents with Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) | Engineering deep dive — how skills teach Claude repeatable tasks | **THE BUILD section** — backs up the "skills = what an agent CAN DO" narrative |
| [Introducing Agent Skills](https://www.anthropic.com/news/skills) | Official launch announcement — skills as folders of instructions | Quick reference for THE TAKE toolkit |
| [Advanced Tool Use](https://www.anthropic.com/engineering/advanced-tool-use) | Programmatic tool calling — Claude orchestrates tools through code not round-trips | Advanced reference if audience asks about production agents |
| [Building Agents with Claude Agent SDK](https://www.anthropic.com/engineering/building-agents-with-the-claude-agent-sdk) | SDK architecture for multi-agent systems — subagents, parallelisation, hooks | Advanced reference — beyond workshop scope but great for follow-up |

### Code & Demos (GitHub)

| Resource | What it is | Use for |
|----------|-----------|---------|
| [Anthropic Cookbook — Agent Patterns](https://github.com/anthropics/anthropic-cookbook/tree/main/patterns/agents) | Reference implementations: prompt chaining, orchestrator-workers, parallelisation | **DEMO GOLD** — show orchestrator_workers.ipynb as "here's what production looks like" |
| [Orchestrator-Workers Notebook](https://github.com/anthropics/anthropic-cookbook/blob/main/patterns/agents/orchestrator_workers.ipynb) | Working Jupyter notebook — orchestrator delegates to specialised workers | Live demo option — or screenshot for slides |
| [Agent Pattern Prompts](https://github.com/anthropics/anthropic-cookbook/tree/main/patterns/agents/prompts) | The actual prompts used in the cookbook agent patterns | Show alongside your VP Agent prompt — "yours is simpler but same pattern" |
| [Skills Repository](https://github.com/anthropics/skills) | Public repo of agent skills — SKILL.md format, folder structure | **THE TAKE** — point attendees here for real-world skill examples |
| [Claude Agent SDK Demos](https://github.com/anthropics/claude-agent-sdk-demos) | Working multi-agent demos — research system, parallel agents | Advanced follow-up for attendees who want to go deeper |
| [Claude Agent SDK (Python)](https://github.com/anthropics/claude-agent-sdk-python) | Python SDK for building agent systems | Link in toolkit resources |
| [Claude Agent SDK (TypeScript)](https://github.com/anthropics/claude-agent-sdk-typescript) | TypeScript SDK for building agent systems | Link in toolkit resources |
| [Custom Subagents Docs](https://docs.anthropic.com/en/docs/claude-code/sub-agents) | How to create custom subagents in Claude Code | Advanced reference |

### Webinar

| Resource | What it is | Use for |
|----------|-----------|---------|
| [Agent Skills Webinar](https://www.anthropic.com/webinars/agent-skills-transform-claude-from-assistant-to-specialized-agent) | "Transform Claude from Assistant to Specialized Agent" — full technical walkthrough | Share in follow-up email to attendees |
| [Complete Guide to Building Skills (PDF)](https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf) | Comprehensive PDF guide | Include in toolkit /resources folder |

### How These Map to Your Workshop

```
YOUR WORKSHOP                    ANTHROPIC'S TERMINOLOGY
─────────────                    ───────────────────────
Knowledge Graph (VP-ONT)    →    Context / Knowledge Base
Skill 1 (Analyse)          →    Agent Skill (SKILL.md)
Skill 2 (Recommend)        →    Agent Skill (SKILL.md)
Agent Prompt (orchestrator) →    Prompt Chaining / Orchestration
Skill 1 → Skill 2 chain    →    Sequential Workflow Pattern
```

**Key demo moment:** Show the [Anthropic cookbook orchestrator-workers notebook](https://github.com/anthropics/anthropic-cookbook/blob/main/patterns/agents/orchestrator_workers.ipynb) alongside your VP Agent prompt and say: *"Same pattern. Yours is three files and no code. Theirs is production Python. The thinking is identical."*

---

*You've got the plan. You've got the content. Now it's just assembly and rehearsal. Go.*
