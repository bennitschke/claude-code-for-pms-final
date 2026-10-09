---
name: review-checklist
description: Reviews a product brief against the PM's ten pre-circulation checks — ownership, problem before solution, audience, value, solution alignment, scope, success, clarity and consistency, open questions, and a one-page length limit. Use it when the user says "review-checklist", "run my checklist on this brief", "review this brief", "check this brief before it goes out", or points at a brief and asks if it's ready to share.
argument-hint: <path to brief>
---

# Review checklist

Run the same ten checks on a brief every time, so the PM doesn't have to explain them again. This is a review, not a rewrite: report what's there and what's missing, and leave the brief untouched unless the PM asks for edits afterwards.

## 1. Find the brief

- If a path was given (`$ARGUMENTS`), read that file.
- If not, use the brief the conversation is clearly about. If there's more than one candidate, ask one short question: "Which brief should I check?"
- Read the whole brief before judging anything. If it links to a prototype or appendix, note it but judge the brief on its own words.
- Count its words (for a file, `wc -w <path>`). Count the whole brief, headings and byline included.

## 2. Run the ten checks

Judge each check as **Pass**, **Partial** or **Fail**. Quote the brief (a short phrase, with its section heading) as evidence for every verdict. Never pass a check on something implied but not written.

### Check 1: Ownership
It clearly identifies who owns the initiative.
- **Pass:** a named person (not a team, not "Product") is stated as the owner.
- **Partial:** only a team or role is named, ownership is split without saying who decides, or a name appears only in passing (e.g. a byline with no stated responsibility).
- **Fail:** nobody is named.

### Check 2: Problem
It explains the problem before proposing a solution.
- **Pass:** the problem and the evidence for it come before any solution, and a reader could understand the problem without the fix.
- **Partial:** the problem is there but comes after the fix, is tangled up with it, or is stated without evidence.
- **Fail:** the brief jumps straight to the solution.
- Note the heading or line where the solution first appears.

### Check 3: Audience
It identifies who is experiencing the problem.
- **Pass:** names the specific users or groups affected, and how many or which ones where that matters.
- **Partial:** the audience is generic ("users", "customers"), or the brief's proposal affects groups it never introduces as affected.
- **Fail:** nobody is identified.

### Check 4: Value
It explains why solving the problem matters to users or the business.
- **Pass:** states the consequence of leaving it unsolved, for users and/or the business, ideally with a number (cost, risk, revenue, lost work, safety).
- **Partial:** value is asserted but not shown ("this is important"), or only one side is covered where the other clearly matters.
- **Fail:** no reason given for why it matters.

### Check 5: Solution alignment
The proposed solution directly addresses the stated problem.
- Map each part of the solution to the problem it solves, and each stated problem to the part that solves it.
- **Pass:** every solution element traces to a stated problem, and every stated problem is addressed or explicitly deferred.
- **Partial:** a solution element solves something the problem never mentioned, or a stated problem has no matching fix and isn't deferred.
- **Fail:** the solution addresses a different problem from the one described.
- Name each gap: "Unmotivated: …" or "Unaddressed: …".

### Check 6: Scope
It clearly defines what's included and what's excluded, and the scope stays consistent throughout.
- List what the opening (summary, problem, proposal) says the work covers, then what the closing sections (scope, out of scope, rollout, next steps, asks, open questions) actually commit to.
- **Pass:** in-scope and out-of-scope are both stated, and the two lists match.
- **Partial:** one of in/out is missing, or something appears at the end that wasn't introduced at the start (scope creep), or something promised at the start quietly drops out by the end.
- **Fail:** there's no clear scope statement, so nothing can be compared.
- Name each mismatch: "Introduced late: …" or "Dropped: …".

### Check 7: Success
It defines how we'll know the initiative was successful, preferably through measurable outcomes.
- **Pass:** at least one measurable outcome with a baseline, a target and a timeframe, plus guardrails where the fix could plausibly make something else worse.
- **Partial:** a measure lacks a baseline, a target or a timeframe; or it's a vague goal ("improve reliability"); or guardrails are missing or unquantified.
- **Fail:** no way of telling whether it worked.

### Check 8: Clarity and consistency
It avoids ambiguous language, contradictions and inconsistent terminology.
- Look for: vague words standing in for specifics ("soon", "some", "a little", "significant"); numbers that disagree between sections; the same thing called by different names (or one name used for two things); undefined jargon or acronyms; claims in one section that another section contradicts.
- **Pass:** nothing that would make two readers come away with different understandings.
- **Partial:** one to three such issues.
- **Fail:** more than three, or any contradiction that changes what would be built.
- Quote each issue.

### Check 9: Open questions
It identifies significant assumptions, dependencies or unresolved decisions that could affect delivery.
- **Pass:** open questions, assumptions and dependencies are listed, each has an owner, and the ones that block delivery are marked as such.
- **Partial:** the list exists but misses an obvious dependency or assumption in the brief, items have no owner, or blocking items aren't distinguished.
- **Fail:** no open questions, assumptions or dependencies are acknowledged.
- Name any unlisted assumption or dependency you can see in the brief itself.

### Check 10: Length
A product brief fits on one page. Treat one page as **500 words**.
- **Pass:** 500 words or fewer.
- **Partial:** 501–650 words (trimmable with tightening).
- **Fail:** more than 650 words.
- Report the word count. If it's over, name the one or two sections that would save the most words, and what could move to an appendix or linked doc.

## 3. Report

Use exactly this format, so every review looks the same:

```
## Review checklist: <brief title>
<file path> · <word count> words · reviewed <today's date>

| # | Check | Verdict |
|---|-------|---------|
| 1 | Ownership | Pass / Partial / Fail |
| 2 | Problem before solution | … |
| 3 | Audience | … |
| 4 | Value | … |
| 5 | Solution alignment | … |
| 6 | Scope | … |
| 7 | Success | … |
| 8 | Clarity and consistency | … |
| 9 | Open questions | … |
| 10 | Length (one page) | … |

**Ready to go further?** Yes / Not yet — <one line on the biggest gap>

### 1. Ownership — <verdict>
Evidence: "<quote>" (<section>)
Fix: <one concrete suggestion, or "None needed">

### 2. Problem before solution — <verdict>
…

(continue for every check, in order)
```

- "Ready to go further?" is **Yes** only if all ten checks pass.
- For a **Pass**, give the evidence line and "Fix: None needed". Spend the words on the checks that didn't pass.
- Keep each fix to one or two sentences, phrased as what to add, cut or move, not rewritten prose.
- Don't report the same gap under two checks. Put it under the check it fits best and mention it once.
- Report in chat. Don't save the review to a file unless the PM asks.
- Stick to these ten checks. If something else serious jumps out (a factual error, a confidentiality problem), add it in one line under **Also noticed**, at the end, without a verdict.
