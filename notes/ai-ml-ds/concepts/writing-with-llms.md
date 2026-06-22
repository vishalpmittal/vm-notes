# Writing with LLMs

## Key Takeaways

- Writing has **three distinct layers**: outline, ideas, and actual text. LLMs are only useful on the third
- Use LLMs to unblock phrasing — *not* to ideate, structure, or generate stake in the topic
- **LLMs converge on the average** of what already exists on a topic; humans diverge based on their own experience and conclusions
- The test for whether a piece of writing should exist: does the reader gain something they couldn't get by prompting an LLM directly? If no, don't ship it
- Code has testable outcomes (it runs or it doesn't). Writing quality depends on whether the author's *reasoning* comes through — a much harder thing for an LLM to manufacture
- Before outlining, separate **topic** from **thesis** — "I want to write about RAG" is a subject; "Most teams reach for RAG before validating that retrieval is their bottleneck" is a claim. Only the claim is yours
- Poor business communication costs the U.S. economy ~$4B/year in *visible* losses alone — unfunded projects, unadopted tools, and unrecognized work are the larger invisible tax

## The Three Layers

| Layer | What it is | LLM useful? |
|---|---|---|
| **Outline** | The skeleton — what sections exist, in what order, why | No — depends on your sense of what the reader needs |
| **Ideas** | The substance — claims, examples, conclusions, perspective | No — LLMs converge on average; you converge on *yours* |
| **Text** | The wording — sentence polish, transitions, phrasing | Yes — LLMs are good editorial assistants |

The mistake is using LLMs at the wrong layer. Asking for an "outline of a blog post on X" gets you a generic outline. Asking to "polish this paragraph" gets you a better paragraph.

## What LLMs Are Actually Good At

- **Unblocking phrasing** — when a sentence isn't landing, handing it to an LLM keeps momentum without ceding authorial control
- **Editorial passes** — flagging redundancy, suggesting transitions, tightening prose
- **Format conversions** — bullets → prose, prose → table, casual → formal

The role is *editorial assistant*, not *co-author*. The author keeps the pen.

## What LLMs Can't Do

> "Give five writers the same topic and ask them to write about it. You'll get five different posts, because each writer brings their own perspective... An LLM given the same prompt will converge on what most writing about that topic looks like."

The fundamental limit: LLMs generate **competent but forgettable** content because their training objective is the average of what exists. Originality is a *deviation* from the average — by construction, LLMs don't deviate.

What LLMs miss:

- **Stake** — why *you* think this matters
- **Specific experience** — the project, the failure, the conversation that informs the claim
- **Counter-intuitive conclusions** — these are anti-correlated with the training average
- **Voice** — the cadence and word choice that signal a person, not a process

## The Reader's Test

Posts that exist solely because they're easy to generate don't justify the reader's attention. The test:

> Can the reader get this directly from an LLM by prompting it themselves?

- **Yes** → don't ship it. You're adding nothing.
- **No** → the piece carries your specific reasoning, experience, or perspective. Ship it.

This is a higher bar than "is the writing competent?" — competent is now free.

## Code vs Writing: Why the Disciplines Differ

| | Code | Writing |
|---|---|---|
| Quality signal | Tests pass / production works | Reader gains an insight unavailable elsewhere |
| Verifiability | Objective (machine grades) | Subjective (reader grades, slowly) |
| AI failure mode | Bugs, security flaws | Average-sounding fluency that says nothing |
| Defense | Multi-model review, repeated passes | Author's own reasoning visible in every section |

See [ai-assisted-code-review.md](ai-assisted-code-review.md) for the code-discipline equivalent — preserving the human's verification role.

## Practical Workflow

1. **Outline by hand.** Sketch what the reader should know, in what order, and *why this piece exists*
2. **Draft the ideas by hand.** Even rough — what's the claim? what's the example? what's the conclusion you actually hold?
3. **Use the LLM for text polish only.** Hand it specific sentences or paragraphs that aren't landing
4. **Read the result yourself.** If it sounds like it could be by anyone, rewrite until it sounds like you
5. **Apply the reader's test before shipping**

## Step 0: Topic vs. Thesis

The outline-by-hand step is itself preceded by a prior step the original framing skips: deciding what you actually think. A topic without a claim produces a generic post — the model can't supply the missing thesis.

| Topic (subject) | Thesis (claim) |
|---|---|
| "I want to write about RAG" | "Most teams reach for RAG before validating that retrieval is their actual bottleneck" |
| "I want to write about multi-agent systems" | "I spent six weeks building an orchestration layer that collapsed to a for-loop after testing — the abstraction added complexity, not capability" |
| "I want to write about prompt engineering" | "The prompts that work in production look almost nothing like the ones in research papers" |

**The exercise:** before drafting anything, write **one messy, unedited paragraph** capturing your core claim. Keep it nearby as your compass — every section either supports it or gets cut. If you can't write that paragraph, you don't have a thesis yet; you have a topic.

## The 7 Cs (Compact Self-Edit Checklist)

A useful pass-before-shipping checklist from *Technical Writing Essentials*:

- **Clear** — easy to understand on one read
- **Coherent** — sections logically connected, transitions explicit
- **Concise** — no wasted words
- **Concrete** — specific examples, not abstractions
- **Correct** — accurate facts and numbers
- **Complete** — necessary context included
- **Courteous** — respectful tone, no condescension

LLMs are reasonably good at flagging violations of *Clear*, *Concise*, and *Coherent* — and **bad** at *Concrete* (they tend to abstract toward the average) and *Complete* (they can't know what context your reader needs).

## Signal Erosion: Why Overused AI Tropes Hurt You

When AI tools deploy the same rhetorical devices indiscriminately — emoji-prefixed bullets, "let me break this down...", bolded takeaways every paragraph, em-dash-heavy cadence — those devices stop signaling emphasis and start signaling *machine*. Technically sophisticated readers increasingly pattern-match on the tropes and disengage before reading. Once trust erodes, rebuilding it is hard. Use rhetorical devices sparingly enough that they still mean something.

## See Also

- [ai-assisted-code-review.md](ai-assisted-code-review.md) — the code equivalent: AI as reviewer/polisher, human keeps judgment
- [../harness-engineering/ai-native-engineering.md](../harness-engineering/ai-native-engineering.md) — the 40/20/40 time allocation that puts thinking *before* generation, applies to writing too

---

**Source:** https://aiagentssimplified.substack.com/p/what-ive-learned-about-writing-after
**Source:** https://aiagentssimplified.substack.com/p/how-to-write-an-authentic-technical
**Date:** 2026-06-02, updated 2026-06-12
**Tags:** writing, llms, ai-assisted-writing, creativity, perspective, editorial, content, originality, technical-writing, thesis, 7-cs, signal-erosion
