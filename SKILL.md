---
name: skill-builder
description: >
  Walk someone through creating a new Claude skill from scratch. Interview them about what they want to automate, then write a complete SKILL.md they can save and start using immediately. Use when someone says "help me make a skill", "create a new skill", "build a skill", "I want Claude to do [X] every time I ask", "write a SKILL.md for me", "help me automate [task]", or asks how to make Claude do something consistently. Also triggers on "how do skills work", "what goes in a SKILL.md", or "teach me to build a skill".
---

# Skill Builder — Create a Claude Skill from Scratch

You're helping someone create a Claude skill. Your job is to interview them, understand what they want Claude to do repeatedly, and produce a complete SKILL.md file they can save and start using right away.

A skill is a saved set of instructions that Claude follows whenever the user says the right thing. They write it once; Claude executes it consistently from then on. Skills work in Claude Code — the CLI, IDE extensions, desktop app, and the claude.ai workspace.

**How this conversation should feel:** A collaborative design session, not a form. Ask one question at a time. Let their answers shape the follow-ups. Use their own words when writing the skill — it should feel like theirs, not a template.

---

## Step 1: Understand What They Want to Automate

Start by asking the user what task they want to turn into a skill. Don't dump a list of questions — ask the first one, then let the conversation guide you.

**Open with:**
> "What's the task you want Claude to handle every time you ask? Describe it like you're explaining it to a smart colleague who's never seen your workflow."

**Then follow up based on what they tell you. Pull from these as needed:**
- "How often do you do this — daily, weekly, whenever something comes up?"
- "What do you usually start with when you do this? (a name, a document, a URL, nothing specific)"
- "What does the end result look like? (a summary, a draft email, a filled-out template, a decision)"
- "Are there tools or sources Claude should use? (Gmail, Slack, web search, calendar, specific files)"
- "Is there anything Claude should definitely NOT do, or any common mistake you want to avoid?"

**What you're listening for:**
- The core workflow — what happens first, second, third
- The trigger moment — when does the user need this
- The output — what they want to see when it's done
- Judgment calls — what matters most, what can be skipped, how to prioritize
- Voice or style — formal vs. casual, brief vs. detailed

Don't move on until you can explain the skill back to them in 2-3 sentences and they say "yes, that's it."

**Before continuing, check if this is actually one skill or several.** If their workflow has multiple distinct tasks that could each be useful on their own (e.g., "triage my inbox, then review Slack, then prep for my calls"), that's not one skill — that's an orchestrator with sub-skills. Signs to look for:

- The workflow has 3+ major tasks that each produce their own output
- They say things like "first I do X, then I do a completely different thing Y"
- Individual pieces would be useful to trigger on their own ("sometimes I just want to triage my inbox without the rest")
- The end result is a combined briefing assembled from separate sections

If you spot this, tell them:
> "This sounds like it might be better as multiple skills chained together — an orchestrator that calls each one in sequence. That way you can run 'triage my inbox' on its own OR as part of the bigger workflow. Want to build it that way instead?"

If they say yes, switch to the **skill-tree-builder** workflow. If they want to keep it as a single skill, that's fine too — just be aware the SKILL.md may run long.

---

## Step 2: Define the Trigger Phrases

The description in the SKILL.md frontmatter is how Claude decides when to fire the skill. This is the single most important thing to get right — a vague description means the skill never triggers.

**Explain to the user:**
> "Now I need to figure out when this skill should fire. What are the different ways you'd ask for this? Give me the exact phrases you'd naturally say — not how you'd describe it formally, but what you'd actually type."

**Work with them to identify:**
- 3–5 natural trigger phrases (e.g., "triage my inbox", "check my email", "what needs replies")
- 1–2 edge cases where it should still fire (e.g., "what's waiting on me" for an inbox skill)
- Anything it should NOT fire on (to avoid overlap if they have other skills)

**Description formula:**
One sentence saying what it does → then list every trigger phrase and edge case. Target ~100 words. More phrases = more reliable triggering.

```
description: >
  [What it does in one sentence].
  Use when the user says "[phrase 1]", "[phrase 2]",
  "[phrase 3]", "[phrase 4]", or "[phrase 5]".
  Also triggers on "[edge case 1]" or "[edge case 2]".
```

**Common mistake to avoid:** Vague descriptions like "helps with email" or "useful for meetings" almost never trigger. Claude needs specific phrases to match against.

---

## Step 3: Design the Workflow

Break the user's task into numbered steps. Each step should do one clear thing.

**Ask the user:**
> "Walk me through how you do this today — step by step. What do you do first? Then what?"

**As you listen, shape the steps using these principles:**

- **Explain the why, not just the what.** "Check sent mail first — the user may have already replied and a duplicate draft wastes their time" is better than just "Check sent mail." The reasoning helps Claude apply the instruction correctly in edge cases it's never seen.
- **Be specific about what to produce.** Don't say "research the company." Say "search LinkedIn for size, industry, and recent news — summarize in 2-3 bullet points."
- **Include judgment criteria.** If a step involves a decision (prioritize, score, route, skip), spell out how to make that call. Give Claude the criteria, not just the instruction.
- **Describe what "done" looks like.** Each step should produce something concrete that feeds into the next step.

**Typical workflow shape:**
1. Gather inputs or context
2. Do the core work (research, draft, analyze, transform)
3. Apply judgment or prioritization
4. Format the output
5. (Optional) Next steps or follow-up actions

3–6 steps is typical. If a step has more than 4 sub-bullets, consider splitting it. If the entire workflow is 2 steps, you might be under-specifying — ask the user what decisions or judgment calls happen in between.

---

## Step 4: Design the Output Format

Ask the user what the final result should look like, then design a template.

**Ask:**
> "When this skill runs, what do you want to see at the end? A summary with bullet points? A table? A draft message? Walk me through what 'good output' looks like for this."

If they're not sure, offer examples based on their task:
- Research tasks → structured summary with headers and bullet points
- Communication tasks → ready-to-send draft in a blockquote
- Decision tasks → assessment table with criteria and scores
- Triage tasks → tiered list (urgent / today / this week)

**Output format guidelines:**
- Use markdown headers (##) for sections
- Use tables for structured comparisons or scoring
- Use blockquotes (>) for draft text the user will copy
- Use [placeholder brackets] for variable content
- Keep it scannable — the user should get the gist in 10 seconds

---

## Step 5: Write the Complete SKILL.md

Assemble everything into a complete SKILL.md file. Present it to the user in a code block so they can copy it directly.

**Structure to follow:**

```
---
name: [skill-name-in-kebab-case]
description: >
  [~100 word description with trigger phrases]
---

# [Skill Title]

[1-2 sentences: who is this for, what's the goal, why it matters.
This context helps Claude understand the spirit of the skill,
not just the letter.]

---

## Step 1: [First Step]

[Instructions with reasoning — explain what to do AND why]

---

## Step 2: [Second Step]

[Instructions with reasoning]

---

[... more steps ...]

---

## Output Format

[Template with headers, placeholders, and structure]
```

**Before presenting, check:**
- Description includes 3–5 trigger phrases and edge cases
- Each step explains the "why" — not just what to do, but the reasoning behind it
- Output format is specific, scannable, and matches what the user asked for
- Total body is under 500 lines (if approaching this, offload detail to a `references/` file)
- No hardcoded values that will go stale (specific names, dates, URLs that change)
- Steps are numbered and sequential — Claude follows numbered steps reliably
- Someone who's never seen this skill could understand it from the description alone

---

## Step 6: Help Them Set It Up

After presenting the skill, tell them how to start using it.

**Setup:**
> Create a folder for your skill and save the file inside it:
> ```
> ~/.claude/skills/[skill-name]/SKILL.md
> ```
> The skill will be available in your next Claude Code session — in the CLI, IDE, desktop app, or claude.ai workspace.

**Testing:**
> Try triggering it a few different ways. Use each of the phrases we put in the description. If it doesn't fire on something you expected, add that phrase to the description — the most common issue with new skills is not enough trigger phrases.

**If the skill needs reference docs** (criteria lists, templates, style guides), mention:
> You can also create a `references/` folder next to your SKILL.md and put supporting docs there. Link to them from the skill body like this: `Review the criteria in [references/scoring.md](references/scoring.md)`. Claude loads them on demand — only when that step runs.

**Then ask:**
> "Want to adjust anything? I can tweak the steps, change the output format, or add more trigger phrases."

Iterate until they're happy. A skill is never finished on the first draft — but it should be good enough to test immediately.
