---
name: skill-tree-builder
description: >
  Help someone design a skill orchestrator — a parent skill that calls multiple sub-skills in sequence to complete a larger workflow. Use when someone says "build a skill tree", "create an orchestrator skill", "chain my skills together", "I need a master skill that runs multiple skills", "combine my skills into one workflow", "build a daily routine skill", or wants to connect multiple existing skills into a single end-to-end process. Also triggers on "how do I call one skill from another", "orchestrator pattern", or "multi-step skill".
---

# Skill Tree Builder — Design an Orchestrator Skill

You're helping someone build a **skill orchestrator** — a parent SKILL.md that calls multiple sub-skills in sequence to run a larger workflow end-to-end. Think of it as a conductor that knows the setlist and calls each musician in order.

**Examples of orchestrators:**
- A "daily prep" skill that runs inbox triage → Slack review → call prep → assembles a morning briefing
- A "release notes" skill that runs changelog generation → draft review → formatting → publishing
- A "prospect research" skill that runs company lookup → contact enrichment → scoring → brief assembly

**How this conversation should feel:** Collaborative. You're helping them think through the architecture — what the sub-skills are, what order they run, what happens between each one, and how the outputs come together. Ask one question at a time.

---

## Step 1: Understand the Big-Picture Workflow

Start by understanding the full end-to-end process they want to automate.

**Open with:**
> "What's the full workflow you want to run end-to-end? Describe the whole thing from start to finish — what happens first, what happens last, and what the final deliverable looks like."

**Then follow up based on what they tell you:**
- "How often do you run this? Daily, weekly, on-demand?"
- "What triggers it? A specific moment, a time of day, a request from someone?"
- "What does the final output look like when everything's done?"

**What you're listening for:**
- The overall shape of the workflow (beginning, middle, end)
- Natural breakpoints where one discrete task ends and another begins — these become sub-skills
- What context is shared across the whole workflow vs. specific to one step
- What the assembled final output should look like

---

## Step 2: Identify the Sub-Skills

Help them break the workflow into discrete, independent tasks. Each one becomes a sub-skill.

**Ask:**
> "Let's break this into pieces. What are the distinct tasks inside this workflow? Each one should be something that could be useful on its own — not just as part of this bigger process."

**Guide them with these questions:**
- "If someone asked you to do JUST this one piece in isolation, would it make sense? Could you hand it to someone and say 'do this part'?"
- "Do you ever run any of these steps on their own, outside of this workflow?"
- "Are any of these steps something you'd want to trigger independently with their own phrase?"

**The independence test — this is critical:**
Every sub-skill in an orchestrator must work as a standalone skill. A user should be able to run `inbox-triage` on its own OR as part of `daily-prep`. If a sub-skill only makes sense when called by the parent, it's not a real sub-skill — it's just a step in the orchestrator body.

**Help them name each sub-skill.** Use kebab-case names that describe what the skill does: `inbox-triage`, `call-prep`, `slack-review`, `changelog-gen`.

**For each sub-skill, capture:**
- What it does (one sentence)
- What input it needs (if any — from shared context or the user)
- What output it produces (the deliverable the orchestrator will capture)
- Whether it already exists or needs to be built

---

## Step 3: Design the Shared Context

Before calling any sub-skills, the orchestrator gathers context that multiple sub-skills will need. This is Step 1 of every orchestrator.

**Ask:**
> "What information does the orchestrator need to gather upfront — things that multiple sub-skills will use? For example: today's calendar, the user's current project, a specific account name, a date range."

**Common shared context sources:**
- Calendar (today's meetings, external calls)
- Current date and time
- A name, company, or account the user provided
- Active project or repo context
- Prior conversation context or notes

**Why this matters:** If three sub-skills all need today's calendar, fetch it once in the orchestrator's shared context step — don't make each sub-skill re-fetch it independently. This saves time and keeps the information consistent.

---

## Step 4: Design the Calling Sequence

Now map out the order the sub-skills run and what happens between each one.

**Ask:**
> "What order should these run in? Does the output of one feed into the next, or are they independent of each other?"

**Key decisions to make together:**

**Sequential vs. independent:**
- If sub-skill B needs the output of sub-skill A → they must run sequentially
- If sub-skills A and B don't depend on each other → the order matters less, but sequential is still the default pattern (Claude runs one at a time)

**What to capture between steps:**
Each sub-skill produces output. The orchestrator should explicitly capture and hold that output before moving to the next sub-skill. This is important — if you don't capture it, the output may scroll out of context in long workflows.

**Conditional steps:**
- "Are any steps conditional? For example: 'only run call-prep if there are external meetings on the calendar today.'"
- "Should the orchestrator skip a sub-skill if there's nothing to process?"

**For each sub-skill in the sequence, document:**
1. The sub-skill name
2. What shared context or prior output to pass to it
3. What to capture from its output
4. Any conditions for running or skipping it

---

## Step 5: Design Failure Handling

Orchestrators should degrade gracefully — a partial result beats no result.

**Ask:**
> "If one of these sub-skills fails or produces nothing, should the whole thing stop? Or should it keep going and just note what's missing?"

**The right default:** Keep going. If inbox triage fails but Slack review and call prep succeed, the user still gets a useful (if incomplete) briefing. Note the failure in the output so the user knows what to follow up on manually.

**Pattern to use:**
```
If [sub-skill] fails or returns no results:
- Note the failure in the final output
- Continue to the next sub-skill
- Do not stop the entire workflow
```

**The only exception:** If a later sub-skill truly depends on an earlier one's output (e.g., call-prep needs the calendar data from Step 1), then a failure in the dependency should skip the dependent step — not crash the whole orchestrator.

---

## Step 6: Design the Final Assembly

The last step of every orchestrator combines sub-skill outputs into one deliverable.

**Ask:**
> "When all the sub-skills have run, what should the final output look like? Is it just all the outputs stacked together, or do you want a synthesized summary? What format works best for you?"

**Common assembly patterns:**
- **Stack and label:** Each sub-skill's output as a section under a header (simplest, works well for briefings)
- **Synthesize:** Pull key items from each output into a unified summary (more polished, takes more instruction)
- **Action list:** Extract just the action items from each sub-skill's output into a single to-do list
- **Template fill:** Plug outputs into a predefined template structure

**Help them design the template for the final output.** Use markdown headers for each section, and include placeholders showing where each sub-skill's output goes.

---

## Step 7: Write the Orchestrator SKILL.md

Assemble everything into a complete orchestrator SKILL.md. Present it in a code block.

**Orchestrator structure:**

```
---
name: [orchestrator-name]
description: >
  [What the full workflow does, end-to-end].
  Use when the user says "[phrase 1]", "[phrase 2]",
  "[phrase 3]", or "[phrase 4]". Also triggers on
  "[edge case phrase]".
---

# [Workflow Title] — Orchestrator

[1-2 sentences: what this orchestrator does, what
the user gets at the end, and why it exists as a
combined workflow rather than individual skills.]

---

## Step 1: Gather Shared Context

[Fetch calendar, date, user input, or anything
multiple sub-skills will need. Document what was
found before proceeding.]

---

## Step 2: Run [Sub-Skill 1]

Follow the `[sub-skill-1]` skill fully.
Capture the complete output before moving on.

[Any context to pass in from Step 1.]
[What to capture from the output.]

---

## Step 3: Run [Sub-Skill 2]

Follow the `[sub-skill-2]` skill fully.
Capture the complete output before moving on.

[Any conditions for running or skipping.]

---

## Step N: Compose & Deliver

Combine all outputs into the final deliverable:

[Assembly instructions — what goes where,
how to synthesize, what format to use.]

### If any sub-skill failed:
Note which step produced no output and why.
Still deliver the partial result — a partial
briefing is better than no briefing.

---

## Output Format

[Final template with headers and placeholders
showing where each sub-skill's output goes.]
```

**Quality checklist:**
- [ ] Shared context is gathered once, upfront — not re-fetched by each sub-skill
- [ ] Each sub-skill is called by name with explicit "follow the `[name]` skill fully"
- [ ] Output is captured after each sub-skill before moving to the next
- [ ] Failure handling: orchestrator continues on individual sub-skill failure
- [ ] Final assembly step combines everything into one deliverable
- [ ] Each sub-skill also works independently (test this)
- [ ] Description has 3-5 trigger phrases for the orchestrator itself

---

## Step 8: Check Sub-Skill Coverage

Before wrapping up, check whether all the sub-skills actually exist.

**For each sub-skill in the orchestrator:**

**If it already exists:** Confirm the name matches exactly what the orchestrator calls. Mismatched names are a common source of orchestrator failures.

**If it needs to be built:** Let the user know and offer to help.
> "The orchestrator calls `[sub-skill-name]`, but that skill doesn't exist yet. Want me to help you build it? We can use the skill-builder workflow to create it."

**Remind them:**
> "Each sub-skill should have its own SKILL.md in its own folder:
> ```
> ~/.claude/skills/[orchestrator-name]/SKILL.md
> ~/.claude/skills/[sub-skill-1]/SKILL.md
> ~/.claude/skills/[sub-skill-2]/SKILL.md
> ```
> The orchestrator and sub-skills are siblings — they live at the same level, not nested inside each other."

---

## Step 9: Help Them Set It Up

**Setup:**
> Save the orchestrator SKILL.md just like any other skill:
> ```
> ~/.claude/skills/[orchestrator-name]/SKILL.md
> ```
> Make sure all sub-skills it calls also exist in their own folders at the same level.

**Testing approach:**
> 1. **Test each sub-skill independently first.** Run each one on its own and make sure it works before testing the full orchestrator. A broken sub-skill is much harder to diagnose from inside a chain.
> 2. **Then test the full orchestrator.** Run it end-to-end and check that each sub-skill's output was captured and the final assembly looks right.
> 3. **Test failure handling.** If you can, trigger a scenario where one sub-skill has nothing to process. Confirm the orchestrator continues and notes the gap.

**Then ask:**
> "Want to adjust the sequence, change how the outputs are assembled, or build any of the sub-skills that don't exist yet?"

Iterate until the orchestrator and all its sub-skills are in place and tested.
