# Build an Agent — Cheat Sheet

**Print this. Pin it. Use it.**

---

## The Recipe

```
KNOWLEDGE GRAPH  +  SKILLS  +  ORCHESTRATION  =  AGENT
(what it knows)    (what it    (when to do
                    can do)     what)
```

---

## Step 1 — Define the Knowledge Graph

Give your agent structured expertise. Not training — just a JSON map of concepts and relationships.

```json
{
  "entities": [
    { "id": "Problem",  "properties": ["severity", "frequency"] },
    { "id": "Solution", "properties": ["feasibility", "uniqueness"] },
    { "id": "Benefit",  "properties": ["measurable", "scalable"] }
  ],
  "relationships": [
    { "from": "Problem",  "to": "Solution", "type": "solvedBy" },
    { "from": "Solution", "to": "Benefit",  "type": "delivers" },
    { "from": "Benefit",  "to": "Problem",  "type": "validates" }
  ]
}
```

**Rule of thumb:** 3-7 entities is enough. If you need more, you need two agents.

---

## Step 2 — Write the Skills

A skill = one job. Input → Process → Output. Like a recipe card.

### Skill template

```markdown
## Skill: [Verb] [Noun]

**Input:**  What it receives (be specific)
**Process:**
- Step 1: What to do with the input
- Step 2: How to use the knowledge graph
- Step 3: What to evaluate or generate
**Output:** What it produces (format matters)
```

### Example skills

**Skill 1 — Analyse**
- Input: A business idea (free text)
- Process: Map to graph → score each entity 1-5 → find weakest link
- Output: Structured analysis with scores and the critical gap

**Skill 2 — Recommend**
- Input: The analysis from Skill 1
- Process: For weakest link → 3 actions → prioritise by impact vs effort
- Output: Prioritised action plan with validation criteria

**The chain:** Skill 1's output IS Skill 2's input. That's a skill chain.

---

## Step 3 — Wire the Agent (the prompt)

Paste this into Claude. Replace the bracketed sections with your own.

```markdown
You are a [DOMAIN] Agent. You help people [GOAL].

## Your Knowledge
You reason using this structure:
- [Entity 1] connects to [Entity 2] via [relationship]
- [Entity 2] connects to [Entity 3] via [relationship]
- [Add the rules: what must be true, what breaks if missing]

## Your Skill Chain
1. FIRST: Run [Skill 1 name] — [what it does]
2. THEN: Run [Skill 2 name] — [what it does]
3. (OPTIONAL) THEN: Run [Skill 3 name] — [what it does]

## Rules
- Be honest. Weak scores are more useful than false confidence
- Use the knowledge structure — don't freelance outside it
- Keep language clear and jargon-free
- Format output with clear sections matching the skill outputs
```

---

## Step 4 — Run It

1. Open [claude.ai](https://claude.ai)
2. Paste your agent prompt
3. Give it a real input — *"Here's my business idea..."*
4. Watch it run Skill 1 → Skill 2 in sequence
5. Test with a deliberately weak input to see it catch gaps

---

## Skill Chain Patterns

```
SEQUENTIAL        FAN-OUT           GATE              LOOP
A → B → C        A → B             A → [test?]       A → B → [good?]
                    → C               YES → B           YES → done
                    → D               NO  → C           NO  → A again
```

| Pattern | Use when |
|---------|----------|
| Sequential | Evaluating or building step by step |
| Fan-out | Need multiple perspectives on one input |
| Gate | Quality threshold before proceeding |
| Loop | Iterative refinement until good enough |

---

## Common Agent Types You Can Build

| Agent | Knowledge Graph | Skill 1 | Skill 2 |
|-------|----------------|----------|----------|
| **Value Proposition** | Problem → Solution → Benefit | Analyse VP | Recommend actions |
| **Content Reviewer** | Audience → Message → Channel | Assess fit | Suggest improvements |
| **Risk Assessor** | Threat → Impact → Mitigation | Identify risks | Prioritise responses |
| **Interview Prep** | Role → Competency → Evidence | Map experience | Generate questions |
| **Project Health** | Goal → Milestone → Blocker | Diagnose status | Recommend unblocks |

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Agent ignores the graph structure | Add: *"You MUST reference the knowledge structure in every response"* |
| Output is unstructured | Add: *"Format output with headers matching each skill's output spec"* |
| Skill 2 doesn't use Skill 1's output | Add: *"Skill 2 input is EXACTLY the output of Skill 1 — do not re-analyse"* |
| Agent adds things you didn't ask for | Add: *"Only use the entities and relationships defined — do not invent new ones"* |
| Responses are too long | Add: *"Each skill output must be under 200 words"* |

---

## Want to Go Further?

| Resource | Link |
|----------|------|
| Building Effective Agents | https://www.anthropic.com/research/building-effective-agents |
| Agent Patterns (code) | https://github.com/anthropics/anthropic-cookbook/tree/main/patterns/agents |
| Agent Skills | https://github.com/anthropics/skills |
| Claude Agent SDK (Python) | https://github.com/anthropics/claude-agent-sdk-python |
| Claude Agent SDK (TypeScript) | https://github.com/anthropics/claude-agent-sdk-typescript |

---

```
Three files. No code. That's an agent.

GRAPH (what it knows) + SKILLS (what it can do) + PROMPT (how it decides) = AGENT
```
