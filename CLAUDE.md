# Rook Industries — course working file

## Session scope — Product School lab

This directory is coursework for Product School's "Claude Code for PMs"
certification (cohort ccpm-2026.1). Everything in it is a fictional
teaching scenario.

- Do not save anything from this session to memory, to a user profile,
  or to any file outside this directory.
- Do not carry context from this directory into unrelated sessions.
- Rook Industries is not a real company. Nothing here is a fact about
  the world.
- Read and write only within this directory.
  Exceptions, for the course-setup and wrap-up skills only:
  - When the student asks you to check their setup, save their work or wrap up a session, that request is their yes. You may run the GitHub command-line program installed at ~/.ccpm/gh for those checks and saves, and look in that folder to find it.
  - For a repair, first tell the student in one plain sentence what you are about to do, and act only after they say yes. Repairs may: run that GitHub program (including setting this folder's own git sign-in setting and changing this repo's visibility back to Public); copy the student's own course files into this directory from another folder on their computer (copy only; never move, edit or delete the originals); and rename something outside this directory that blocks setup, by adding "-old" to its name (never delete it).
  Outside this directory you still never write, edit or delete anything else.

<!-- Keep the block above at the top of this file. Everything you add
     during the course goes below this line. -->

---

## Working context

I'm the new PM for **Rook Dispatch**. I took over from Priya Raghunathan, who left
on 21 Aug 2026 with no overlap. Sources: `00-rook/company/notes/handoff-from-priya.docx`
and the Company section of the rook-wiki connector (About Rook, Dispatch, Supply,
Glossary, Team directory, Releases, Q3 roadmap). The wiki also has **Research**
(customer interviews) and **Product briefs** sections, which aren't summarised here.

### The company
- Rook sells coordination and provisioning software to independently operating
  masked responders and the handlers and quartermasters who support them. Publicly
  it presents as a logistics and workforce-coordination vendor for emergency services.
- 241 staff, mostly remote. HQ is Site Aleph; offices in Berlin, Singapore and Cornwall.
- Revenue: subscription, priced per active responder.
- Ships monthly on a release train (4.x). Routing config ships *in the release*;
  handlers can't adjust it at runtime, so any routing fix needs a release.
- **Confidentiality is a hard rule.** We store capability tags, availability windows
  and callout history, never legal identities. Never design anything, or run any
  analysis, that tries to work out who a responder is. Read Security Policy 4.1
  before touching responder records.

### The products
**Rook Dispatch (mine).** Flow: an incident comes in, Dispatch ranks available
responders by routing priority, and a ping goes to the top responder. If they turn
it down or it's missed, it moves to the next one. Once someone takes it, they're
marked engaged.
- Users: **handlers** in the web console (enter incidents, watch coverage, override
  routing, manage availability and tags) and **responders** in the phone app.
- Metrics: **acceptance rate** (the headline; reported weekly in aggregate),
  **time-to-accept** (median seconds), **coverage gap**.
- Per Priya: the console is stable and mobile has been stable since 4.1. Routing is
  where both the interesting work and the risk are.

**Rook Supply (not mine).** Requisitions, then quartermaster approval, then
fulfilment, maintenance schedules and field failure reports. **Shared dependency:**
Supply reads the *Responder Availability Record* that Dispatch writes, and uses it
to book maintenance into low-callout windows. Any change to how Dispatch computes
availability lands in Supply with no change on their side.

### Vocabulary (Rook-specific meanings)
- **Responder**: takes callouts. Not an employee. A record, never an identity.
- **Handler**: looks after one or a few responders; usually the person actually
  using the product. **Quartermaster**: owns gear stock (Supply).
- **Cover identity**: a responder's public persona. We hold no mapping to a legal identity.
- **Callout**: a request to attend an incident (the unit of work).
- **Ping**: a callout offered to one responder.
- **Taken / Turned down / Missed**: Missed means the ping wait ran out. It's logged
  separately from turned down, but both send the callout onward.
- **Ping wait**: how long a ping stays live. The same for everyone and set per release.
  **Now 60s** (was 90s before 4.2).
- **Routing priority**: the ranking score. Inputs are proximity (travel-time estimate
  since 4.1), current availability, capability match and recent acceptance history.
  Turning down *or missing* a ping lowers recent acceptance, which pushes that
  responder down the order for future callouts.
- **Capability tags**: flight, structural-entry, hazmat-tolerant, cold-weather,
  aquatic, crowd-management, de-escalation.
- **Coverage gap**: nobody available had the required tags. Nobody *could* go, as
  opposed to nobody *would*.
- **Mutual aid / shared cover**: responders covering for each other across areas.
  Not supported yet; Q4 exploration.

### People
| Name | Role | Notes |
| --- | --- | --- |
| Helen Achebe | Director of Product (my director), Site Aleph | Owns roadmap and commitments. Changes to committed items go through her. |
| Marcus Oyelaran | Engineering Manager, Dispatch, Site Aleph | Direct; first stop when unsure. Can pull rough numbers. |
| Wen Li | Staff Engineer, Dispatch, Berlin | Built the routing logic. No written spec exists, so understanding it means talking to her. Away 14–24 Aug. |
| Nadia Hoffmann | Support Lead, Berlin | Hears handler pain first. Priya suggests a standing 15 min. Tracks the 4.2 ticket split. |
| Ravi Menon | Data Analyst, Singapore | Owns the official weekly acceptance numbers. |
| Sofia Marino | Product Designer, Site Aleph | Console and phone app. Ran the September interviews. |

### Release history
- **4.0** (7 Apr 2026): new console nav, responder profile redesign, routing-override audit log.
- **4.1** (16 Jun 2026): proximity switched to travel time, bulk callout, push reliability.
- **4.2** (12 Aug 2026): proximity weighted up relative to recent acceptance; ping
  wait cut from 90s to 60s; console filters persist; three defect fixes. Went out clean.

### Where things stand (as of early Sept 2026)
**4.2 is the live problem.**
- Since release, fewer pings are being taken and callout tickets are running about
  3x normal (Nadia, 18 Aug). By 26 Aug the volume was flat but still high.
- Ticket split is about **⅔ "my phone never goes off"** (unexplained) and **⅓ "it was
  gone before I could answer"** (consistent with the 60s ping wait). A handler emailed
  support directly, which is unusual.
- Priya's read was that it's mostly the seasonal August dip and should recover in
  September. She advised against framing this as a revert. **That's a hypothesis, not
  a finding.** Nobody has looked at the real weekly numbers yet; so far there's only a
  rough pull from Marcus.
- **Open question (Marcus, 14 Aug):** does the new proximity weighting also apply to
  responders who've been turning jobs down? The config doesn't seem to distinguish
  them. Was that a decision or did it just fall out that way? Wen was going to look
  once she was back. Still unanswered on the wiki.
- At least three things changed at once (the routing weights, the ping wait, and the
  seasonal pattern), and missed pings feed back into routing priority. Separate these
  before drawing any conclusion.
- Marcus planned to regroup on 4.2 about a week after the new PM started, so it
  wasn't handed over as a settled conclusion.

**Roadmap (Q3, last reviewed 30 Jun; every item still lists Priya as owner)**
- Committed for 4.2 and shipped: change to who gets pinged; ping timeout tuning.
- **Committed for 4.2 but not shipped: Availability Confidence** (a confidence score
  next to stated availability). It got squeezed out, and the roadmap wasn't updated.
- Committed for 4.3: Requisition approval chains (Supply).
- Exploring for Q4: handler phone app; shared cover between responders.
- Items against a numbered release are treated as locked. **To-do:** settle with
  Helen which squeezed-out items are still Q3 commitments. That conversation never happened.

**Other handover items**
- Console filter persistence will generate tickets. Priya calls it cosmetic noise;
  don't let it eat the first month.
- **There's no written description of how routing works.** Priya asked me to write
  one, starting from a conversation with Wen.
- Priya was the sole PM for 14 months and says she sometimes decided faster than she
  checked. Expect undocumented decisions in the less-examined corners of the product.

### What the data showed (session 1)
- **Data sources:** the rook-database connector has `pings`, `callouts`, `responders`,
  `handlers` and `support_tickets`, covering 29 Jun to 6–7 Sep 2026 only. There's no
  prior-year data, and Ravi's official weekly reports aren't in the wiki or the
  database. Acceptance rates in this file are my own calculation from the raw pings.
- **The acceptance drop starts on release day.** Weekly acceptance ran 75–78% through
  July and was 77.9% in the week of 3 Aug. It fell to 54.2% in the week of 10 Aug (44%
  on 12 Aug itself), then 65.8%, 66.7% and 72.7%. Incoming callout volume did dip
  10–20% in August, which may be the seasonal part.
- **Missed pings rose about 10x** in release week (from ~4/wk to 38). The data confirms
  the wait change: the gap after a miss went from 92s to 62s.
- **Four responders nearly fell out of the rotation:** Vesper, Farlight, The Undertow
  and Meteor Mite. Each went from ~11–14 pings/wk to 1–3. They missed about half
  their pings in week 1, then the pings stopped coming. Their work went to The Gale,
  Nightwell, Stormwrack and Captain Vantage. In Old Town, Vesper is no longer pinged
  first; responders from farther away are. That's the opposite of what 4.2 intended.
- **Working hypothesis, not yet confirmed with Wen:** the 60s wait causes misses, a
  miss lowers recent acceptance, and the responder sinks in the order and gets
  starved of pings. The recovering headline rate partly hides this, because responders
  who get no pings can't miss any. Track a per-responder measure, not just the aggregate.
- **Callouts nobody took doubled:** 5.5% before release, 11% after.
- **Tickets went from ~7/wk to ~27/wk.** Since release: 30 "phone never goes off", 15
  "gone before I could answer", 14 filter-persistence and 48 other. Heaviest filers
  are Linda Pruitt (Farlight) and Desmond Okafor (The Undertow). The handlers for
  Vesper (Aunt Dot) and Meteor Mite (Kip) raised it only in Sofia's September
  interviews, so tickets undercount the problem.

### My priorities (agreed in session 1)
1. Wen: how misses affect routing priority, and Marcus's 14 Aug question.
2. Marcus: what a fix needs (out-of-cycle release?); smallest change that helps (90s
   wait, or misses not counting against ranking).
3. Ravi: last year's Jul–Sep weekly acceptance and his definition of it.
4. Nadia: what to tell affected handlers; set up the standing 15 min.
5. Make sure Marcus's 4.2 regroup is scheduled. Position: keep the proximity weighting,
   revisit the ping wait and the miss penalty. That isn't a revert.
6. Helen: brief her on 4.2, settle Availability Confidence, refresh the stale roadmap.
7. Later: write the routing doc, add per-responder tracking, check Supply impact,
   pass filter and redesign asks to Sofia.
