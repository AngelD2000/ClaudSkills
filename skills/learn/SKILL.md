---
name: learn
description: Generate a research-backed course plan for learning a subject or skill, then later grade projects the user completes from that plan. Use this whenever the user wants to learn a new topic, asks for a study/course plan, wants to "get good at" or "ramp up on" something, or submits finished work (code, a writeup, a repo) for feedback on a topic they're learning. Trigger on phrases like "help me learn X", "make me a course plan for X", "I want to get better at X", or when the user pastes a project/submission referencing a topic they've studied with this skill before. Don't use it for quick factual lookups or one-off "explain X" questions with no learning-plan intent.
---

# Learn

Turns a bare topic name into a real course plan — researched, not recycled from training data — and later grades the user's project submissions against the rubric that plan created.

## 0. Decide which mode this invocation is

Look at what the user actually sent along with the topic:

- **Just a topic name** (or a topic + "I want to learn this") → **Plan mode**: build a new course plan.
- **Substantial work attached** — pasted code, a writeup, a repo/gist link, a description of something they built — → **Feedback mode**: look for `learning-plans/<topic-slug>.md` first (in the current working directory, creating `learning-plans/` if needed). If it exists, grade the submission against its rubric. If no plan exists for this topic yet, say so and offer to generate one first.

Don't ask the user which mode they want — infer it from message content. If it's genuinely ambiguous (e.g., they paste something but it's unclear if it's their own project or reference material), ask.

## Plan mode

### 1. Classify the topic (silently — don't show this step to the user)

Decide whether the topic leans **research field** (a body of knowledge studied for understanding — economics, psychology, physics, history) or **applied/tool-based** (something used to build or do things — a programming language, a framework, a piece of software, a practical skill). Use judgment; most topics lean one way even if not purely one or the other. If it's a genuine 50/50 (e.g., "statistics," "machine learning"), blend both content approaches rather than forcing a pick.

The reason this split matters: a research field is best understood through its conceptual disagreements and frameworks, since there's no single "current best practice" — it's a matter of interpretation. A tool is best understood through what practitioners actually do with it today, since that changes fast and the goal is competence, not just comprehension.

### 2. Research the content — actually search, don't rely on memory

This is the part that makes the plan worth generating fresh instead of copy-pasting a template. Use web search for anything time-sensitive or contestable — your training data can be stale or generic on exactly the things that matter most (recent developments, live debates, what practitioners currently do).

**If research field:**
- The 5 most important mental models for the field — the conceptual tools an expert reaches for automatically. For each: name it, explain it plainly, and show what changes about how you see problems in this field once you have it.
- 3 places where experts genuinely disagree — not settled trivia, but live fault lines. For each, state the strongest version of both sides (steelman, not strawman) so the user understands why smart people land differently.
- 10 questions that would expose real understanding vs. memorized facts. Good ones require applying a mental model to a new situation, explaining why a common intuition is wrong, or resolving a tradeoff — not recalling a definition.

**If applied/tool-based:**
- What's actually new or changing right now (search for this — don't guess at "latest"). A tool-based skill taught from stale information wastes the user's time relearning things that already shifted.
- How practitioners really use it day to day — the default workflow, not the textbook one. Where it differs from how it's usually taught, say so.
- What separates code/systems/output that scales and stays maintainable from output that works once and rots — the practices that matter at 10x the scope.

**Either way:** infer the user's current level from how they phrased the request and anything you already know about their background (don't interrogate them for a skill-level questionnaire unless you truly have nothing to go on — a wrong guess here is cheap to correct later, an upfront questionnaire is friction).

### 3. Examples

Concrete, specific examples tied directly to the mental models / disagreements (research field) or the practices / workflows (applied) from step 2 — not generic textbook examples. If a mental model or practice can't be made concrete with a real example, it's a sign the explanation in step 2 was too abstract; tighten it.

### 4. Projects

2-4 hands-on projects, ordered easiest to hardest, that force the user to actually apply what step 2 covered rather than just read about it. Each project needs a rubric — concrete, checkable success criteria — because that rubric is what feedback mode will grade against later. Vague rubrics ("understand the concept") produce vague feedback; write rubrics as things you could check off against a submission.

### 5. Write the plan file

Save to `learning-plans/<topic-slug>.md` (create the directory if it doesn't exist) using roughly this shape — adapt headers to fit the research-field/applied split from step 1, don't force sections that don't apply:

```markdown
# <Topic> — Course Plan

## Content
[mental models + disagreements + questions, OR latest developments + practitioner usage + scalability]

## Examples
[concrete examples tied to the content above]

## Projects
### Project 1: <name>
<description>
**Rubric:**
- <criterion>
- ...
### Project 2: <name>
...
```

Tell the user where the file is and give a short summary — don't just dump the whole plan back into chat if it's long; let them open the file.

## Feedback mode

1. Read the existing `learning-plans/<topic-slug>.md` and find the rubric for the project the submission matches (ask if it's unclear which project this is for).
2. Grade against that rubric specifically, not against vague general quality — go criterion by criterion.
3. Structure the feedback as: what's met, what's missing or weak, and concrete next steps to close the gap. Be honest about gaps rather than softening them — the point of a rubric is to give feedback the user can act on, not reassurance.
4. Don't regenerate the plan or rubric in this mode. If the user's submission reveals the rubric itself was flawed (ambiguous, testing the wrong thing), flag that separately rather than silently reinterpreting it.
