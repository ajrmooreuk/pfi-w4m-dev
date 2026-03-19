# PFC-DSY-PLAN — Workshop: Patterns, Graphs & Agents

**Version:** 1.0.0
**Duration:** 60 minutes
**Audience:** Smart, ambitious women — mixed technical backgrounds
**Facilitators:** Amanda (tech lead / demo) + Milana (narrative / audience engagement)
**Theme:** Pattern recognition — from Euler to Bowie to building your first AI agent

---

## Session Arc — "Build an Agent in an Hour"

```
THE HOOK          THE SEE           THE WIRE          THE BUILD         THE TAKE
(5 min)           (10 min)          (10 min)          (25 min)          (10 min)

Euler → Bowie →   Live graph →      Skills = what     Wire skills +     Toolkit +
LLM. Pattern      "oh, THAT's       an agent CAN do.  graph into a      resources +
recognition is    a knowledge        Graph = what it   working agent.    next steps.
the thread.       graph — agents     KNOWS. Wire them  Test it. Break    You built
                  navigate these"    together = agent  it. Fix it.       an agent.
```

**Central narrative:** You are building an agent. Skills are its capabilities. The graph is its knowledge. Wiring them together is what makes it an agent, not just a chatbot.

---

## Detailed Runsheet

### 1. THE HOOK — "Patterns You Can't Unsee" (5 min)

**Lead: Amanda** (Milana sets up demo / manages room)

Amanda walks the room through the teaser theme — conversational, not slides:

- **Euler** (1736): Seven bridges. Nobody could walk them all without retracing. Euler stopped looking at bridges and looked at *connections*. Pattern recognition → graph theory. Every network you've ever used exists because of that moment.
- **Bowie** (1970s–2016): Cut-up technique from William Burroughs. Literally cut lyrics into strips, rearranged randomly, found unexpected meaning. "Heroes", "Ashes to Ashes" — genius from fragmentation and recombination.
- **The LLM**: Does exactly what Bowie did, at scale. Fragments language into tokens, maps patterns across billions of relationships, recombines. Not magic. Pattern recognition.

**Key line:** *"Today you're going to build something that does what Euler theorised and Bowie intuited — and you'll do it in under an hour."*

**Props (optional):** Milana hands out printed Bowie lyrics, someone cuts them up and rearranges — instant audience participation + visceral understanding of tokenisation. Amanda can reference the result: *"That's what an LLM does — billions of times a second."*

---

### 2. THE SEE — "Everything is a Graph" (10 min)

**Lead: Amanda** (live OAA Workbench demo)

**Setup:** Browser open to GitHub Pages OAA Visualiser:
`https://ajrmooreuk.github.io/Azlan-EA-AAA/PBS/TOOLS/ontology-visualiser/browser-viewer.html`

#### Demo Flow (rehearse to ~8 min, 2 min buffer)

| Step | Action | What audience sees | Say |
|------|--------|--------------------|-----|
| 1 | Load a single simple ontology (VP-ONT or VSOM-ONT) | Force-directed graph appears — nodes bounce, settle | *"This is a knowledge graph. Nodes are things. Edges are relationships. That's it."* |
| 2 | Click a node | Sidebar opens — Details tab shows properties | *"Every node knows what it is, what it connects to, and the rules about those connections."* |
| 3 | Switch to Connections tab | Relationship list appears | *"This is what Euler saw — it's not the nodes that matter, it's the connections."* |
| 4 | Click "Load Registry" → load all ontologies | Full merged graph — series-coloured, cross-ontology gold edges | *"52 ontologies. Hundreds of concepts. All connected. This is what the agent navigates."* |
| 5 | Switch to Mermaid view | Clean flowchart rendering | *"Same data, different pattern. Mermaid diagrams — you'll use these in the build."* |
| 6 | Switch to Tier 0 view | Series-level overview | *"Zoom out — six knowledge domains, all interconnected. This is the map the agent uses."* |

#### Key Teaching Moments

- **Ontology = structured knowledge** — not a database, not a spreadsheet. A web of meaning.
- **Why it matters for AI:** An LLM without a graph is clever but unstructured. With a graph, it *reasons* — it follows edges, respects rules, finds paths.
- **The Bowie callback:** *"Bowie cut up words and found patterns. We structured knowledge into graphs and gave the patterns to an AI. Same instinct, industrial scale."*

**Milana's role during demo:** Watch the room. If eyes glaze, prompt Amanda to skip ahead. If energy is high, let it breathe. Ask the audience: *"Who's seen a graph like this before?"* — gauge the room.

---

### 3. THE WIRE — "What Makes an Agent an Agent" (10 min)

**Lead: Amanda** (whiteboard or simple slide) **Support: Milana** (real-world analogies)

#### The Big Idea

A chatbot answers questions. An **agent** does work. The difference? Skills + knowledge + decisions.

#### The Agent Recipe (draw this — it's the session's anchor diagram)

```
                    ┌─────────────┐
          YOU ────→ │    AGENT    │ ────→ RESULT
                    └──────┬──────┘
                           │ decides
                    ┌──────┴──────┐
              ┌─────┴─────┐ ┌─────┴─────┐
              │  SKILL 1  │ │  SKILL 2  │    ← what it CAN DO
              │ (analyse) │ │(recommend)│
              └─────┬─────┘ └─────┬─────┘
                    │             │
              ┌─────┴─────────────┴─────┐
              │    KNOWLEDGE GRAPH       │    ← what it KNOWS
              │    (ontology)            │
              └──────────────────────────┘
```

**Three ingredients. That's it:**

| Ingredient | What it is | Without it... |
|------------|-----------|---------------|
| **Skills** | Specific things the agent can do — each one has an input, a process, and an output | The agent can chat but can't *do* anything structured |
| **Knowledge Graph** | Structured expertise — concepts, relationships, rules | The agent guesses instead of reasons — no expertise, just vibes |
| **Orchestration** | The decision logic — which skill to run, when, what to pass between them | Skills exist but nothing connects them — like having tools but no plan |

#### Milana's Angle — Make It Personal

*"Think about what YOU do at work. You have skills — specific things you're good at. You have knowledge — years of expertise about how things connect. And you have judgement — you decide what to do when, in what order, based on context. An agent is the same three things. Today you're going to wire them together."*

#### The Bowie Callback

*"Bowie's cut-up technique was a skill — fragment and recombine. His musical knowledge was the graph — he knew which fragments could work together because he understood the patterns. And his creative judgement was the orchestration — he chose which recombinations to keep. You're about to give an AI the same three things."*

#### Quick Concept Glossary (for reference, not to teach exhaustively)

| Term | One-liner |
|------|-----------|
| **MCP** | Standardised plug sockets — lets agents connect to any external tool |
| **Skill chain** | Skills wired in sequence — output of one feeds input of the next |
| **Prompt engineering** | How you tell the agent what it is, what it knows, and what to do |
| **Tokens** | How the LLM sees text — fragments, just like Bowie's cut-ups |

---

### 4. THE BUILD — "Build Your First Agent" (25 min)

**Lead: Amanda** (live build) **Support: Milana** (circulate, collect ideas, keep energy up)

**The promise:** *"By the end of this section, you will have built a working AI agent. Not watched someone build one. Built one."*

#### What We're Building — Three Components, One Agent

An agent has three parts. We're going to build each one, wire them together, and run it live.

```
STEP 1 (5 min)     STEP 2 (3 min)      STEP 3 (2 min)       STEP 4 (15 min)
Build the graph  →  Write the skills  →  Wire the agent  →   Run it, test it,
(knowledge)         (capabilities)       (orchestration)      break it, fix it
```

#### STEP 1: Build the Knowledge Graph (5 min)

**Amanda says:** *"First, we give our agent expertise. Not by training it — by giving it a structured graph of knowledge it can reason over."*

Show the ontology, walk through it node by node:

```json
{
  "@context": { "vp": "https://pf-ontology.org/vp#" },
  "@id": "vp:ValueProposition",
  "entities": [
    {
      "@id": "vp:Problem",
      "label": "Problem",
      "description": "The pain point or unmet need",
      "properties": ["severity", "frequency", "currentAlternatives"]
    },
    {
      "@id": "vp:Solution",
      "label": "Solution",
      "description": "What you're proposing",
      "properties": ["uniqueness", "feasibility", "timeToValue"]
    },
    {
      "@id": "vp:Benefit",
      "label": "Benefit",
      "description": "The measurable outcome",
      "properties": ["quantifiable", "sustainable", "scalable"]
    }
  ],
  "relationships": [
    { "from": "vp:Problem", "to": "vp:Solution", "type": "solvedBy" },
    { "from": "vp:Solution", "to": "vp:Benefit", "type": "delivers" },
    { "from": "vp:Benefit", "to": "vp:Problem", "type": "validates" }
  ]
}
```

**Teaching moment:** *"Three nodes. Three edges. That's a knowledge graph. Problem is solvedBy Solution, Solution delivers Benefit, Benefit validates Problem. A cycle — because a benefit that doesn't solve the original problem isn't a benefit."*

**Quick OAA callback:** Drop this JSON into the visualiser — watch the graph render live. *"That's your agent's brain. Let's give it hands."*

#### STEP 2: Write the Skills (3 min)

**Amanda says:** *"A skill is one thing the agent can do. Input, process, output. Like a recipe card."*

```markdown
## Skill 1: Analyse Value Proposition

**Input:** A business idea (free text)
**Process:**
- Map the idea against the VP ontology (Problem → Solution → Benefit)
- Score each entity's properties (1-5)
- Identify the weakest link in the chain

**Output:** Structured analysis with scores and the critical gap

---

## Skill 2: Recommend Next Steps

**Input:** The analysis from Skill 1
**Process:**
- For the weakest link, generate 3 specific actions
- Prioritise by impact vs effort (Pareto — the 20% that delivers 80%)
- Frame as testable hypotheses, not assumptions

**Output:** Prioritised action plan with validation criteria
```

**Teaching moment:** *"Notice Skill 2's input IS Skill 1's output. That's a skill chain. The output of one feeds the next. This is how agents do complex work — not one massive prompt, but composable skills wired together."*

#### STEP 3: Wire the Agent (2 min)

**Amanda says:** *"Now we wire the graph and skills together into an agent. This is the orchestration — the bit that makes it an agent, not just a chatbot."*

```markdown
You are a Value Proposition Agent. You help people stress-test
business ideas fast.

## Your Knowledge Graph
You reason using the Value Proposition ontology:
- Every idea has a PROBLEM it solves, a SOLUTION it proposes,
  and a BENEFIT it delivers
- These form a cycle: Problem → solvedBy → Solution → delivers
  → Benefit → validates → Problem
- If any link is weak, the whole proposition is at risk

## Your Skill Chain
1. FIRST: Run the Analyse skill — map the idea to the ontology,
   score it, find the gap
2. THEN: Run the Recommend skill — generate actions for the
   weakest link

## Rules
- Be honest. A weak score is more useful than false confidence
- Use the ontology structure — don't freelance outside it
- Keep language clear and jargon-free
- Format output with clear sections matching the skill outputs
```

**Teaching moment:** *"That's your agent. Knowledge graph, two skills, orchestration logic, and rules. Three files. No code. Let's run it."*

#### STEP 4: Run It, Test It, Break It (15 min)

| Time | What | Amanda says | Milana does |
|------|------|-------------|-------------|
| 0-2 min | Paste agent prompt into Claude | *"We're giving Claude its identity, knowledge, and skills"* | Watch the room — this is the "is it really that simple?" moment |
| 2-5 min | Feed it a STRONG idea from the audience | *"Who's got a business idea? Doesn't matter how rough"* | Seed an idea if the room is shy. Have 2-3 backup ideas ready |
| 5-8 min | Watch the agent run both skills | *"See it mapping to the ontology? Problem, Solution, Benefit — it's following the graph. Now Skill 2 chains — the analysis becomes the input"* | Point out to the room: *"Look at the structure — that's the ontology working, not just vibes"* |
| 8-11 min | Feed it a WEAK idea (intentionally vague, no clear problem) | *"Now let's break it. Give me a terrible idea — or at least a vague one"* | Encourage the room to go deliberately bad — this is fun |
| 11-13 min | Watch the scores tank, gap analysis fire | *"The graph caught it. No clear problem? The whole chain weakens. This is why structure matters — it catches what vibes miss"* | *"That's the difference between an agent and a chatbot. The chatbot would have said 'great idea!' The agent found the gap"* |
| 13-15 min | Add a third skill live (optional stretch) | *"What if we add a Skill 3 — Competitive Edge Check? One more line in the chain..."* | Bridge to takeaway: *"The toolkit has templates for adding more skills — you can keep building"* |

#### The "So What" Moment (Amanda + Milana together)

**Amanda:** *"You just built an agent. Three files — a knowledge graph, two skills, and a prompt that wires them together. No code. No infrastructure. The graph is what makes it reason, not just respond."*

**Milana:** *"Think about YOUR expertise. What do you know that could be a graph? What do you do that could be a skill? You now know how to wire those together into something that works while you sleep."*

#### Backup Ideas (if audience is shy)

1. "A subscription box for sustainable office supplies"
2. "An app that matches freelancers to short-term projects by skill"
3. "A consultancy that helps small restaurants reduce food waste"

Each is deliberately strong on one VP node and weak on another — good for showing the agent's differential scoring.

---

### 5. THE TAKE — "Your Toolkit" (10 min)

**Lead: Milana** (Amanda answers technical Qs)

#### Takeaway Pack (digital — shared link or QR code)

```
workshop-toolkit/
├── README.md                    ← Start here
├── teaser-script.md             ← The Euler/Bowie/LLM story
├── mini-ontology.json           ← VP ontology (used in session)
├── skills.md                    ← Skill definitions
├── agent-prompt.md              ← Orchestrator prompt
├── examples/
│   ├── idea-strong.md           ← Example: strong VP analysis
│   ├── idea-weak.md             ← Example: weak VP (intentional gaps)
│   └── idea-pivot.md            ← Example: how agent guides a pivot
├── extend-it/
│   ├── add-a-skill.md           ← Template: how to add Skill 3
│   ├── add-ontology-nodes.md    ← Template: extend the ontology
│   └── chain-patterns.md        ← Common chain patterns
└── resources/
    ├── further-reading.md       ← Curated links
    ├── claude-quickstart.md     ← Getting started with Claude
    └── graph-thinking.md        ← Graph/ontology mental models
```

#### `README.md` Content

```markdown
# Patterns, Graphs & Agents — Workshop Toolkit

You built an AI agent in under an hour. Here's everything you need to keep going.

## What You Built
A Value Proposition Agent that uses a knowledge graph (ontology) to structure
its reasoning and chains two skills together to analyse and recommend.

## What To Do Next
1. **Try it again** — paste agent-prompt.md into Claude and test your own ideas
2. **Extend the ontology** — add nodes (see extend-it/add-ontology-nodes.md)
3. **Add a third skill** — see extend-it/add-a-skill.md for the template
4. **Go deeper** — resources/further-reading.md has the good stuff

## The Pattern (remember this)
Ontology (knowledge) + Skills (actions) + Orchestration (decisions) = Agent

Euler saw it in bridges. Bowie heard it in fragments. Now you can build with it.
```

#### `resources/further-reading.md`

```markdown
# Further Reading & Resources

## Claude & AI Agents
- Claude documentation: https://docs.anthropic.com
- Claude Code (CLI agent): https://claude.com/claude-code
- Model Context Protocol (MCP): https://modelcontextprotocol.io
- Anthropic cookbook (practical examples): https://github.com/anthropics/anthropic-cookbook

## Graph Thinking & Ontologies
- "Graph Theory" — Euler's original insight (accessible explainer):
  https://en.wikipedia.org/wiki/Seven_Bridges_of_K%C3%B6nigsberg
- JSON-LD (how we write ontologies): https://json-ld.org
- Knowledge Graphs explained (Google): https://blog.google/products/search/introducing-knowledge-graph-things-not/

## The Bowie Cut-Up Connection
- Bowie's cut-up technique (BBC documentary clip): search "David Bowie cut-up technique BBC"
- William Burroughs' original method: "The Cut-Up Method of Brion Gysin" (1961)
- How transformers tokenise (the LLM parallel): https://huggingface.co/learn/nlp-course/chapter6/5

## Pattern Recognition — The Thread
- "Thinking in Systems" — Donella Meadows (patterns in complex systems)
- "The Pattern on the Stone" — Danny Hillis (computation as pattern)
- "A Pattern Language" — Christopher Alexander (patterns in design — influenced software patterns)

## Tools Used Today
- OAA Ontology Workbench (live): https://ajrmooreuk.github.io/Azlan-EA-AAA/PBS/TOOLS/ontology-visualiser/browser-viewer.html
- Claude: https://claude.ai
```

#### `extend-it/add-a-skill.md`

```markdown
# How to Add a Skill to Your Agent

A skill is just a structured instruction. Here's the template:

## Skill Template

**Name:** [What it does — verb + noun]
**Input:** [What it needs — be specific]
**Process:**
- Step 1: [What to do with the input]
- Step 2: [How to use the ontology]
- Step 3: [What to evaluate or generate]
**Output:** [What it produces — format matters]

## Example: Skill 3 — Competitive Edge Check

**Input:** The analysis from Skill 1 + recommendations from Skill 2
**Process:**
- For the proposed solution, identify 3 likely competitors or alternatives
- Score each competitor against the same VP ontology properties
- Identify where YOUR proposition is genuinely differentiated

**Output:** Competitive positioning summary with differentiation score

## How to Chain It

Add to your agent prompt:
"3. THEN: Run the Competitive Edge Check — compare against alternatives"

That's it. The chain now runs: Analyse → Recommend → Compete.
```

#### `extend-it/add-ontology-nodes.md`

```markdown
# How to Extend the Ontology

The mini ontology has 3 nodes: Problem, Solution, Benefit.
Real propositions need more. Here's how to add.

## Adding a Node

Add to the "entities" array:
{
  "@id": "vp:Customer",
  "label": "Customer",
  "description": "Who specifically has this problem",
  "properties": ["segment", "reachability", "willingnessToPay"]
}

## Adding a Relationship

Add to the "relationships" array:
{ "from": "vp:Customer", "to": "vp:Problem", "type": "experiences" }
{ "from": "vp:Solution", "to": "vp:Customer", "type": "servesNeedsOf" }

## The Pattern

Every node asks: WHAT is this thing, and what PROPERTIES matter?
Every edge asks: HOW do these things relate?

That's ontology design. You're encoding expertise into structure.
```

#### `extend-it/chain-patterns.md`

```markdown
# Common Skill Chain Patterns

## Sequential (what we built today)
Skill A → Skill B → Skill C
Each skill's output feeds the next. Simple, predictable.

## Fan-Out
Skill A → Skill B
        → Skill C
        → Skill D
One input, multiple analyses in parallel. Good for multi-perspective evaluation.

## Gate
Skill A → [Score > threshold?] → YES: Skill B → Skill C
                                → NO:  Skill D (remediation)
Conditional routing. The orchestrator decides which path based on results.

## Loop
Skill A → Skill B → [Good enough?] → YES: Output
                                    → NO:  Skill A (with feedback)
Iterative refinement. Each pass improves on the last.

## Which Pattern Fits?
- Evaluating something? → Sequential
- Need multiple viewpoints? → Fan-Out
- Quality matters? → Gate
- Refinement needed? → Loop
- Complex real-world workflow? → Combine them
```

---

## Logistics & Prep Checklist

### Amanda — Before Session

- [ ] Test OAA Workbench loads cleanly on the venue WiFi
- [ ] Pre-load VP-ONT in one browser tab (fallback if WiFi flakes)
- [ ] Pre-load full registry in second tab (cached)
- [ ] Have Claude open with agent-prompt.md ready to paste
- [ ] Print 3 copies of Bowie lyrics for cut-up prop (optional)
- [ ] Prepare 3 backup business ideas in case audience is shy
- [ ] Test screen sharing / projector resolution
- [ ] Toolkit folder zipped and hosted (GitHub repo or shared drive link)
- [ ] QR code printed/on slide pointing to toolkit download

### Milana — Before Session

- [ ] Rehearse THE HOOK delivery (5 min, conversational, no slides)
- [ ] Prepare 2-3 audience prompt questions for energy management
- [ ] Know the backup business ideas (can seed them naturally)
- [ ] Have toolkit link ready to share (QR code, message, etc.)
- [ ] Closing message prepared — the "what next" for the group

### Venue / Tech

- [ ] WiFi tested with OAA workbench
- [ ] Projector / screen working
- [ ] Power strips if participants bring laptops
- [ ] Fallback: mobile hotspot if venue WiFi fails
- [ ] Water, pens, index cards (for cut-up exercise / idea capture)

---

## Timing Buffer Plan

| If running... | Cut | Keep |
|---------------|-----|------|
| 5 min over | Q&A | Everything else |
| 10 min over | Extend-it walkthrough | Point to toolkit |
| 10 min under | Add audience Q&A time | Or demo a second idea through the agent |

---

## Role Summary

| Phase | Amanda | Milana |
|-------|--------|--------|
| THE HOOK | Leading the Euler/Bowie/LLM narrative | Setting up demo, managing room |
| THE SEE | Driving OAA demo live | Watching the room, prompting questions |
| THE THINK | Explaining concepts | Providing real-world analogies |
| THE BUILD | Live coding / demo | Circulating, collecting ideas, energy |
| THE TAKE | Answering tech questions | Leading close, sharing toolkit, next steps |

---

## Post-Session

- Share toolkit link with all attendees (Milana owns distribution)
- Collect 3 words from each attendee: what surprised them (feedback signal)
- Photo of the cut-up lyrics exercise if it works well (social content)
- Follow-up message in 48 hours with toolkit reminder + one extra resource

---

*"Euler found patterns in bridges. Bowie found patterns in fragments. LLMs find patterns in everything. Now you can too."*
