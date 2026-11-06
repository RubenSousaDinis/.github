---
name: contractor-workflow
description: "How to take a feature or change from request to reviewable PR: investigate before asking, size the work by risk (🔴/🟡/🟢), write a short brief (goal, blocking questions, assumptions, plan, verification) before coding, then write the PR with a risk map, decisions, evidence and how to check it. Use before implementing any non-trivial change, and whenever opening a PR or writing a PR description."
---

# Contractor workflow

Two halves of one habit: agree on the work before writing it, then hand it over so it can be
reviewed by risk instead of line by line.

## Before implementing a feature or change

Work like a contractor who bills for rework: a wrong assumption is yours to avoid, an unnecessary
question is the user's to pay.

### 1. Investigate before asking

Read the relevant code, tests, configs and dependency manifests first. Anything findable in about
a minute of searching is research, not a question: test framework, language version, lint rules,
error-handling conventions, directory layout, existing abstractions. If the codebase contradicts
itself, raise that.

### 2. Size it with the risk levels below (Pull requests → Risk map)

- **🟢, or a small change with one obvious correct form** (typo, rename, a few lines): just do it.
- **🟡 with nothing blocking**: write the brief below, then proceed without waiting. The user stops
  you if an assumption is wrong.
- **🔴, or any blocking question**: write the brief and **stop until the user approves**. Be more
  suspicious than usual of your own assumptions.
- **Multi-day or multi-session work**: use superpowers (brainstorming → writing-plans →
  executing-plans) instead, so the plan lives in a file.

If the user already said "just do it" or approved a plan in this conversation, don't ask again.

### 3. The brief

**Goal.** One paragraph restating the request in your own words, with the acceptance criteria
you'll hold yourself to. If the restatement is wrong, this is the cheapest place to find out.

**Blocking questions (0–3).** Only where a wrong answer means throwing work away, not adjusting
it. Each one comes with your recommended default, so the answer can be "yes to all". If nothing
blocks, say "none".

**Assumptions.** At most about 8, numbered, specific and falsifiable, most-likely-wrong first.
"Inputs are under 10k rows and fit in memory" is one; "the code should be maintainable" is not.
Cover only what the task touches: data (shape, volume, trust, malformed input), failure (timeout,
partial write, downstream 500: retry, fail loud or degrade), boundaries (callers, public vs
internal, compatibility), state (concurrency, idempotency, transactions, ordering), environment
(runtime, deploy target, what it may reach), scope (what you're deliberately not doing).

**Plan.** Files to create or change, key function and type signatures, order of work. Where you
chose between real alternatives, name the rejected one and why in one clause (these become the
PR's Decisions).

**Verification.** How you'll prove it works: the commands you'll run, the screen or request
you'll check, and what you expect to leave unverified (these become the PR's Evidence).

### 4. While implementing

Implement what was agreed. If an assumption turns out wrong or the plan doesn't survive contact
with the code, stop and say so. Don't quietly switch to a different design, and don't press on
with an approach you now believe is wrong.

## Pull requests

Every PR is written so the reviewer can review it by risk, check the evidence, and judge the decisions,
without reading every line with the same attention. AI writes more code than anyone can review
line by line, so the PR has to say where to look.

### Before opening a PR

- Run the tests, the type checker and the linter that the repo has. Fix failures before opening.
- If the change has no test that would catch it breaking, add one, or say why it can't have one.
- One logical change per PR. If the work grew into two, split it.

### PR description, in this order

Use the template at `pull_request_template.md` in this `.github` repo (installed as
`~/.github/pull_request_template.md`) (same sections as below) unless the repo
has its own `.github/pull_request_template.md`, in which case fill that one and add any missing
section from this list at the end. With `gh`, write the filled body to a file and pass
`--body-file`.

**1. What and why** — two or three sentences: the problem, and what this PR changes. No list of
files.

**2. Risk map** — every changed area gets a level, highest first:

| Level | What counts | How the reviewer reads it |
|---|---|---|
| 🔴 High | Money, payments, auth, permissions, secrets, signing or keys, smart contracts, migrations and anything that deletes or rewrites data, public API contracts, concurrency | Read line by line |
| 🟡 Medium | Business logic, queries, caching, error handling, external integrations | Read the diff and the tests |
| 🟢 Low | Tests, docs, renames, formatting, generated code, config with no runtime effect | Skim, trust the checks |

List each area as `level · path or area · one line on why it has that level`. If nothing is
🔴, say "No high-risk changes". Never downgrade something to make the PR look smaller.

**3. Decisions** — the choices a reviewer could disagree with, one bullet each: what was decided,
the alternative considered, and why this one. Include anything that wasn't in the request. This
is the part the reviewer reads instead of the code; omit only if there were no real choices.

**4. Evidence** — what was actually run, not claims:
- The exact commands and their results (test counts, type-check output, a short log excerpt).
- For UI, a screenshot or recording. For an API, a real request and response.
- What was **not** verified, and why. "Not tested against production data" beats silence.

**5. How to check it** — the two or three steps the reviewer can run themselves to confirm it works.

### Rules

- Report failures as failures. A skipped or flaky test is listed under Evidence, not hidden.
- Don't write "should work" or "probably": either it was run, or it goes under "not verified".
- Keep the description short. If a section has nothing, write one line saying so.
- Never open, push or merge a PR unless the user asked for it in this conversation.
