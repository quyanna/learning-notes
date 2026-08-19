# Agentic AI — running notes

One file for the concepts from the Ed Donner agentic course (the theory days,
where there's no lab to hold the idea). Newest section at the bottom.

---

## Agent vs workflow

*Source: Udemy, The Complete Agentic AI Engineering Course, week 1 day 2,
video 9 — "What Is an AI Agent? Definitions, Workflows vs Agents Explained".*

### The definition to keep

> An agent is an **LLM with tools in a loop to achieve a goal.**

Attributed in the video to Simon Willison, late 2025. Two older definitions are
worth recognising but not memorising: "AI systems that can do work for you
independently" (OpenAI-flavoured, vague), and "a system where an LLM controls
the workflow" (Anthropic / Hugging Face, early 2025).

### The part he says three times

An LLM does not *do* anything. It takes tokens in and predicts the tokens most
likely to follow. That is the whole model.

Everything else is **your code**: reading those output tokens, deciding they
mean "call this tool", calling it, feeding the result back, looping, and
deciding the goal is met.

So "the LLM controls the workflow" is loose language. The agent is the code
wrapped around the model, not the model.

### Workflow vs agent (Anthropic's split)

Both are **agentic systems**. The difference is who decides what happens next.

| | Workflow | Agent |
|---|---|---|
| Path | Predefined, known in advance | Open-ended, decided as it runs |
| Testable | Yes — you can enumerate the paths | Not really — it might loop indefinitely |
| Who decides | Code, though an LLM may pick a branch | The LLM, continuously |
| Example | Deep Research: clarify → search → report → summarise | Claude Code, Codex, GPT Agent / Operator |

Lots of real systems sit between the two — open-ended but capped at, say, five
iterations. The split is a way of describing something, not a box to file it in.

**The production point:** commercial systems mostly lean *workflow*, because
repeatable and testable beats open-ended. Building a workflow does not make it
less agentic.

### My own case

Lab 1's exercise — business area → pain point in that area → agentic solution
to that pain point — is a **workflow**. Three predefined steps, path fixed in
code, no LLM choosing what comes next. Anthropic's name for that exact shape is
**prompt chaining** (video 10).

### To read later, not now

Anthropic, "Building Effective AI Agents" —
https://www.anthropic.com/engineering/building-effective-agents
The post this terminology comes from. Verified 2026-08-18.

---

## The design patterns

*Source: week 1 day 2, video 10 — "Agentic AI Design Patterns: Chaining,
Routing, Orchestrator". From Anthropic's "Building Effective AI Agents".*

**Caveat he leads with:** these are not software-engineering design patterns.
They are indicative — ideas to provoke thinking, not a catalogue to file a
problem under. Real systems show elements of several. Don't memorise the names.

Five workflow patterns:

1. **Prompt chaining** — one big task split into fixed subtasks, each LLM call
   feeding the next. Optional gate (a decision in code) between steps.
   *Lab 1's exercise is this.*
2. **Routing** — one LLM classifies the input and picks which specialised LLM
   call handles it. The point is separation of concerns: a call specialised on
   technical questions beats one prompt carrying everything.
3. **Parallelization** — **my code** splits the work, farms it to N LLM calls,
   **my code** aggregates.
4. **Orchestrator-worker** — same picture, but an **LLM** decides how to split
   the task and an **LLM** synthesises the results. The split is dynamic.
5. **Evaluator-optimizer** — a generator LLM produces, an evaluator LLM accepts
   or rejects and sends it back. Universally called **LLM as a judge**.

The difference between 3 and 4 is the only tricky one: who decides the split —
my code, or a model.

And one agent pattern: an LLM in a loop, acting on an environment and taking
feedback, until it decides the goal is met. Which is the definition again.

**The confusion this clears up:** orchestrator-worker has an LLM controlling
what happens, and it is still a *workflow*. Workflow means the code path is
predefined, not that no LLM decides anything.

---

## Risks, monitoring and guardrails

*Source: week 1 day 2, video 11 — "Agentic AI Risks, Guardrails, Evals and
Traps to Avoid".*

### The core risk

Flexibility is the benefit and the Achilles heel: **unpredictability**. The path
through the system varies, the outputs vary (not deterministic), and therefore
the **cost per run** varies too.

### Mitigation 1 — monitoring

- **Observability** — traces showing what actually happened on each LLM call,
  in development and in production.
- **Evals**, of two kinds:
  - *Against the end business goal* — the most important one. Leads generated,
    customers contacted, revenue from agent-made sales. A real-world number.
  - *Against the output itself* — the evaluator-optimizer pattern, i.e. LLM as
    a judge.

### Mitigation 2 — guardrails

OpenAI's definition: they "ensure your agents behave safely, consistently, and
within your intended boundaries."

Concretely, guardrails are **just code**. Scaffolding around the agent that
checks inputs and outputs against rules. The LLM generated an output — you are
not obliged to use it. Check it first.

---

## The two traps

### Trap 1 — solution-first instead of problem-first

"I need an agent for my business." → *What business problem does it solve?* →
"A strategy agent." → *That's still not a problem.*

His real example: a client wanted a "culture agent". Pressed hard, the actual
problem was **morale**, and underneath that, **higher attrition than desired** —
which is measurable. An agent might be one of several solutions; an employee
survey might come first.

**The insidious half:** LLMs are trained to generate *believable* content. That
is the objective they were optimised for. Believable is not the same as right.
The only way to know the output is useful is to measure it against a business
outcome and iterate until it moves. Without a metric, a culture agent will
happily emit plausible advice forever.

### Trap 2 — anthropomorphizing

The red flag: reaching for a whiteboard and drawing named agent roles first —
trade manager, market research agent, trader agent, risk manager. Treating LLMs
as if they were people. They are token predictors.

**The rule, and the answer to the routing question parked in video 10:**

> Start with **one LLM call, one prompt, one objective.** Measure it against the
> business metric. Then split into more calls or agents **because it measurably
> improves performance**, not because the division sounds right to a human.

Role-shaped architectures sometimes *do* win — teams-and-responsibilities is
often a good way to divide up calls. But arrive there by experiment, on
purpose, not by starting there.

### On hallucination

As an AI **user**, complaining about hallucination is fair. As an AI
**engineer**, it is not a move available to you. The model does exactly what it
was built to do: predict plausible tokens. Interpreting those tokens, adding
guardrails, checking accuracy — that is the job. *"We align next token
prediction with a business outcome. That's the job."*

---

## Terminology: "agentic engineer" is ambiguous

Two different meanings, and people use both:

- someone who **uses agents to engineer** (Claude Code, Codex),
- someone who **engineers agents** (this course).

Be explicit about which one you mean.
