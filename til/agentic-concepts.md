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

Anthropic, "Building Effective Agents" (2024) — the post this terminology comes
from. Linked in the course resources (lecture 1 / the repo README).
