# Prompt Engineering Concepts

## 1. Role Prompting

Giving a model a specific role or persona changes its output even when the
underlying task and model stay the same. In this homework, all four
experts (Database Read Expert, Database Write Expert, Content Expert,
Orchestrator) run on the same model — `openai/gpt-4o-mini`. Only their
`role`/`domain`/`specific_instructions` row from the `llm_roles` table
changes. This was the biggest lever in the project: three text fields turn
the same model into a SQL generator, a Python generator, or a
conversational assistant, with zero code duplication.

## 2. Few-Shot Prompting

Including a worked example anchors the model to the exact output format
you want, more reliably than instructions alone. The Read Expert, Write
Expert, and Orchestrator each get one example in `few_shot_examples` (e.g.
a real question paired with a full SQL query using a `JOIN`). The Content
Expert deliberately gets none, since its context changes with the resume
data each time. Effect: testing a join-heavy question, the Read Expert
matched the example's query structure instead of guessing at one.

## 3. Decomposition

Breaking a complex request into an ordered sequence of smaller sub-prompts
lets each piece get solved with a focused, specialized prompt instead of
one prompt trying to do everything at once. For a compound request like
"Does he know React? If not, add it," the Orchestrator's prompt forces it
to output a list of sub-prompt calls in order, which
`run_orchestrator_plan()` executes one at a time. Testing confirmed it
reliably splits this into a Read Expert call followed by a Write Expert
call, executed in that order.