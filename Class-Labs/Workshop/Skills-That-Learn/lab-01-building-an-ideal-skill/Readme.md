# Lab 1: Design the Best Skill

## From a Prompt You Retype to a Skill You Trust

![image](../images/banner.png)

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Part 1: Why Use a Skill Instead of a Prompt](#part-1-why-use-a-skill-instead-of-a-prompt)
- [Part 2: What's Inside a Skill](#part-2-whats-inside-a-skill)
- [Part 3: The Six Design Principles](#part-3-the-six-design-principles)
- [Part 4: How a Skill Gets Built](#part-4-how-a-skill-gets-built)

---

## Overview

Every week you promise someone something — a proposal, an answer, a call — and a few of those promises slip. Finding them means digging through your inbox, and a prompt you retype won't behave the same way twice.

The fix is a **skill**: a set of instructions Claude loads the same way every time. But a skill is only as good as the way it's designed. A vague one works in a demo and fails on a Tuesday.

This lab is about **design only**. You won't install or run anything yet. You'll take apart a real **Follow-Up skill** — it finds who you owe a follow-up, drafts a reply to each in your own voice, and emails you one summary, without ever sending a follow-up itself — and learn what makes it reliable.

Here's the flow for this lab:

- **See why a skill beats a prompt.** Three problems a prompt can't solve.
- **Take a skill apart.** The seven parts every strong skill has, and how to word them.
- **Learn the six design principles.** The rules behind every line of this skill, ready to copy into your own.
- **See how it was built.** Interview, draft, test, harden — and try the interview yourself.

The same skill carries through the whole workshop. In [Lab 2](../lab-02-scheduling-your-skill/Readme.md) you'll download it, run it and schedule it. In [Lab 3](../lab-03-continuous-learning-loop/Readme.md) you'll teach it from your feedback. In [Lab 4](../lab-04-company-brain/Readme.md) you'll give it company context.

---

## Prerequisites

✅ **Claude app** installed and signed in.

That's all. Nothing to download in this lab — the skill itself comes in Lab 2.

---

## Part 1: Why Use a Skill Instead of a Prompt

You could paste a long prompt every Monday: "Look through my email, find follow-ups, write drafts…" It works once. Then you forget a rule, tweak the wording, and get a different result. The problem isn't that the prompt is bad — it's that it lives in your clipboard and your memory, not anywhere Claude can load it the same way every time.

A skill fixes three things:

| Problem with a prompt | What a skill does instead |
|---|---|
| You retype it, and it drifts | The instructions are written once and loaded the same way every run |
| A prompt can't hold hard-won rules | A skill can carry hundreds of lines of rules, each learned from a real failure |
| You can't hand it to a teammate or schedule it | A skill is a file — you can share it, version it and schedule it |

That last row is where this workshop is going.

---

## Part 2: What's Inside a Skill

### What's a Skill

A skill is a folder Claude can load on demand. Its core is one Markdown file, `SKILL.md`, with two layers:

| Layer | What it holds | When Claude reads it |
|-------|---------------|----------------------|
| **Frontmatter** | A `name` and a `description` | Always — to decide *when* to use the skill |
| **Instructions** | Everything below the frontmatter | Only after the skill is picked — to decide *how* to behave |

That split is why you can install many skills without slowing Claude down. It carries a one-line description of each, and loads the full instructions only for the one it needs.

### The Description Is a Trigger, Not a Summary

The description is the most under-written part of most skills. If it's vague, Claude never picks the skill; if it's too broad, Claude picks it at the wrong time.

| | Example |
|---|---|
| ❌ Weak | `description: "Helps with follow-ups."` |
| ✅ Strong | `description: "Use when the user asks who to follow up with, who went silent, or what they promised and never sent. Reads mail and calendar, writes reply drafts in the user's voice, never sends them. Not for bulk email or CRM updates."` |

A strong description has three things: **when** to use it (the phrases people actually say), **what** it does, and **what it is not for**.

### The Skeleton

Here is the skeleton of the Follow-Up skill's `SKILL.md`. You'll open the real file in Lab 2.

```
---
name: follow-up-loop
description: "Weekday follow-ups assistant. Use when the user asks to run follow-ups..."
---

# Follow-Ups Loop

## 1. Purpose and scope        ← the outcome, what it reads and writes, what it never does
## 2. Prerequisites            ← what it needs, and what happens without it
## 3. How a run works          ← the steps, each with an exit check
## 4. Step 0: validation       ← the checks before anything is read
## 5. First-run setup          ← one-time setup, asked before anything is created
## 6. Steps 1 to 5 in short    ← read, select and rank, draft, record, summarise
## 7. Guardrails               ← the hard rules, each with its reason, plus the stop gates
## 8. Checkpoints              ← the places it waits for you
## 9. Memory                   ← what it remembers between runs
## 10. QA                      ← the checks before it finishes
## 11. Output                  ← the shape of the result
## 12. Feedback                ← how your replies improve it (Lab 3)
## 13. Troubleshooting and hand-off
```

Read it top to bottom and you can see the order of a run: **limits → evidence → decision → checks → output.**

### Supporting Files

A skill can also carry supporting files. This one is a folder:

| File | What it is |
|------|------------|
| `SKILL.md` | The main instructions |
| `config.md` | Every number in one place — the 10-day floor, the draft cap, the run lock |
| `references/` | Long detail, loaded one step at a time: reading rules, selection and ranking, drafting style, the summary email, the learning loop |
| `assets/learning-store.html` | The dashboard the skill publishes for what it learns (Lab 3) |
| `tests/test-prompts.md` | Prompts to re-run after every change |
| `HANDOFF.md` and `CHANGELOG.md` | How to install and own it, and what changed in each version |

Keep `SKILL.md` for what Claude needs on every run. Move long detail into supporting files, so it's only loaded when a step needs it.

### The Seven Parts

Every strong skill has these seven parts, whatever the task:

| # | Part | Its job | In the Follow-Up skill | Where to find it |
|---|------|---------|------------------------|------------------|
| 1 | **Description** | Names the skill and says when to use it | Re-run, or iterate on, your follow-ups | Frontmatter |
| 2 | **Governing rules** | Set the hard limits | Drafts only, never sent. One summary email, to yourself. Nothing attached | Sections 1 and 7 |
| 3 | **Run contract** | State what it needs, and what happens without it | Mail and Calendar required. Missing one means a Connect button and a stop | Sections 2 and 4 |
| 4 | **Evidence gathering** | Decide where facts come from and how far to read | Sent mail, inbox, Drafts, calendar and chat in one batch, every page read, a 10-day no-gaps floor | Section 6, `references/reading-rules.md` |
| 5 | **Decision rules** | Turn evidence into a judgment | What counts as a follow-up, and what to suppress | Section 6, `references/selection-and-ranking.md` |
| 6 | **Quality gates** | Check every output before it ships | A nine-point completeness gate, a four-point correctness gate, a pre-send gate | Sections 6, 7 and 10 |
| 7 | **Output format** | Fix the shape of the result | One summary email in a fixed order, whose subject line carries three counts that must reconcile | Section 11, `references/summary-email.md` |

> **Why this matters:** weak skills have only parts 1 and 5. They describe the task but not the limits, the checks or the output — so they work in a demo and fail on a Tuesday.

### How to Write the Instructions

Part 3 covers *what* to put in a skill. These habits cover *how to word it*:

| Habit | Instead of | Write |
|-------|-----------|-------|
| **Use numbers, not adjectives** | "Read enough history" | "Read the last 10 days, every page" — and keep every number in `config.md` |
| **Say what to do when unsure** | "Be careful with drafts" | "Draft it unless a check found something concretely wrong" |
| **Give the reason beside the rule** | "Never skip the Drafts folder" | A two-column table: the rule, and the failure it came from |
| **One job per section** | A long paragraph of mixed rules | One step, one purpose, one exit check |
| **Order the sections the way a run happens** | Rules scattered by topic | Limits, then evidence, then decision, then checks, then output |

---

## Part 3: The Six Design Principles

Every rule in this skill exists because something went wrong without it. These six are the ones to copy into any skill you build.

### 1. Make the dangerous action impossible, not discouraged

The skill never sends a follow-up. It creates a *draft*. The human's final click is the safety net.

> *"Drafts only, never send a follow-up"* — and the reason beside it: *"The user's name is on it; a wrong send cannot be undone."*

If a skill can do something you can't undo, don't write "be careful." Remove the ability.

### 2. Look for evidence, not for keywords

Searching only for the word "follow-up" misses most real commitments, because people write "I'll send that over" and "let me check and come back to you."

> *"Do not look only for the literal phrase "follow-up." Most real commitments never use it."*

Tell the skill the language people actually use, and what an unresolved commitment looks like.

### 3. Draft when unsure, flag when wrong

A real run once produced zero drafts on a day with more than a dozen live candidates, because a connector was rate-limited and the skill couldn't be fully certain. A discarded draft costs you one click. A missed follow-up can cost a relationship.

> *"Draft it unless a check below found something concretely, affirmatively wrong — never merely because information was incomplete."*

Define two separate outcomes: *incomplete* (draft it, name the gap) and *wrong* (flag it, don't draft).

### 4. Copy the person, never invent them

Drafts are written by studying your 15 to 20 most recent sent emails — your greeting, your sentence length, your sign-off. The signature is copied exactly as you wrote it.

> *"Never construct, complete, or "clean up" a signature."*

Give the skill a real source of truth to copy from, and ban the generic AI phrases ("I hope this email finds you well") outright.

### 5. Fix the output format

The summary email always has the same sections in the same order. The subject line carries three counts — new drafts, drafts still waiting, items that need you — and they must match the body.

> *"The three numbers must reconcile with the body."*

A fixed format is what lets you read the result in 30 seconds on day 30 as easily as on day 1.

### 6. Turn every failure into a rule

Look at the rule "An unsent draft never suppresses an item." It exists because a real run saw an unsent draft, marked the item as already handled, and told the user "nothing needs a follow-up" while two written follow-ups sat unsent for five days.

Skills get ideal by being *hardened*, not by being written perfectly. When a run goes wrong, write down the rule that would have prevented it. Lab 3 automates exactly this.

---

## Part 4: How a Skill Gets Built

You don't write a skill like this top to bottom. It gets built in four passes:

| Phase | What happens | Output |
|-------|--------------|--------|
| **1. Interview** | Claude asks you questions *before* writing anything: who is this for, what counts as a follow-up, what must never happen, what a good result looks like | A clear brief |
| **2. Draft** | Claude writes the first `SKILL.md` from the brief — the seven parts above | A first version |
| **3. Test on real data** | Run it on a real inbox and read every draft it produced | A list of what went wrong |
| **4. Harden** | Turn each failure into a rule, add it, run again | A version you can trust |

### Try Phase 1 Yourself

The interview is the step most people skip — and it's why their skills come out generic. In Claude, paste this prompt:

```
I want to build a skill that finds the follow-ups I owe people and writes
reply drafts for me, but never sends anything.

Before you write anything, interview me. Ask one question at a time about:
- who the skill is for and what their pipeline looks like
- what counts as a follow-up, and what should be ignored
- what must never happen
- how the output should look when I receive it

When you have enough, summarise the brief back to me and wait for my approval.
```

![image](./images/1.png)

Notice what Claude does — it asks, it doesn't assume. That brief becomes the governing rules and decision rules in the skill. You don't need to finish the interview. The finished skill is waiting for you in Lab 2.

---

## What You Learned

- **A skill is a prompt that's been hardened.** The Follow-Up skill isn't clever wording — it's seven parts, from a trigger description to a fixed output format, each one there because a real run went wrong without it.

- **The description is a trigger.** It says when to use the skill, what it does, and what it's not for.

- **Good skills limit what Claude can do, not just what it should do.** The skill creates drafts instead of sending, and stops at the connector gate instead of guessing. Those aren't polite suggestions — they're the safety net.

- **Word rules with numbers, reasons and a fallback.** "10 days, every page" beats "enough history"; a reason beside each rule tells you when it's safe to change; and "when unsure, draft" beats "be careful."

- **Skills are hardened, not written perfectly.** The interview gets you a good first version. Real runs get you a trustworthy one.

---

You now know what a strong skill looks like on paper. Next you'll put one to work: download the finished Follow-Up skill, run it on your own inbox, and see what it feels like to use.

[Go to Lab 2: Run and Schedule Your Skill →](../lab-02-scheduling-your-skill/Readme.md)
