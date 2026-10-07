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

### Interviews and tickets (session 2)
- The two sources cover all 15 handlers with almost no overlap. The 4 interviewees (Dot, Ambrose, Kip, Halloran) have filed 1 ticket between them; the other 11 handlers filed 10–21 each. 86 of the 147 tickets repeat another ticket word for word, including the "responder quotes", so count handlers, not tickets, and ask Nadia why.
- 13 of 15 handlers reported callouts gone before the responder could answer. Misses rose for every responder (0–5% → 10–18%), not just the four. In the last two weeks of data, Farlight, The Undertow, Vesper and Meteor Mite got 4–14% of their usual ping share. Corporal Ashgrove and Halfmoon got about 0.75×, and The Gale, Sgt. Falkirk and Captain Vantage got more than 1.5×. Callouts nobody took were ~5% before release, 12.7–14% after, then 5.5% in the week of 31 Aug: the totals are recovering while the four stay cut out.
- Draft success measure for the fix: number of responders getting under half their usual ping share (now 4, target 0), plus callouts nobody took at 6% or less (4-week average). Guardrails: missed pings 4% or less; slowest-10% time from callout to the accepted ping 45s or less (now ~70–80s); nobody above 1.5× share; closest responder still pinged first (needs Wen); Supply. It needs Helen's sign-off. Ask Wen how far back acceptance history looks, which decides whether the four need a reset.
- Open flags: Dot's interview page records what looks like her full legal name (Sofia should redact it, per Security Policy 4.1). Handlers share sign-ins, so the override audit log can't show who acted. Two tickets from after release say maintenance was booked on marathon day (possible Supply impact). The Releases page lists nothing after 4.2, so did 4.3 ship? Q3 closed on 30 Sep, so Availability Confidence is now a missed commitment. Brief Helen early and lead with callouts nobody took.
- To hand off: Sofia gets the filter-reset warning, dark mode, bigger badges, handler alerts (evidence for the Q4 handler phone app), a different sound per responder, the tag legend and the screen reader gap. Supply gets requisition priority, replies to failure reports, catalog search, and cracked plates as a safety issue.
- Session 3 (rewind): `00-rook/data/callout-history.csv` (responder × week, pings sent and taken) matches the database exactly. Nearly all of the 4.2 drop is misses, not turn-downs: misses went from 1–3% to 21.5% in release week (still 12.7% by 31 Aug), while turn-downs fell to 14–18%, below the July level of ~21%. The four starved responders missed 40% in release week and 64% after, and their turn-downs didn't rise. Without them, acceptance is ~67% rather than the headline 73%.
- Callouts nobody took: 43/837 (5.1%) before, 54/482 (11.2%) from 10 Aug to 6 Sep, so ~29 more than the old rate predicts, ~22 of them ending on a miss (2 before). Worst areas: Southport (Stormwrack) and Riverside (Ironvale). Closest responder pinged first: 80% before → 63% after, the opposite of the committed goal. The draft headline for Helen leads with callouts nobody took plus that 80→63 number. We don't know what Helen prioritises, so ask Marcus.
- Routing seems to stop early: if the first ping goes to someone from another area and fails, routing always moves on (293/293). If it goes to the area's own responder and fails, routing stops ~65% of the time, the same before and after 4.2. 45 of the 54 unanswered callouts after release got only one ping. Ask Wen/Marcus for candidate-list logs. The routing code is in `00-rook/code/dispatch-routing/` (config: timeout 90→60, proximity weight 0.45→0.60) and I haven't read it yet. Check `history.py` for the miss penalty and lookback before asking Wen.
- The tickets are weak independent evidence: 14 of 15 "gone before I could answer" tickets were filed an exact whole number of hours after a real miss, to the second (none before release). Ask Nadia how tickets get created. Only Ambrose's ticket 3043 looks hand-written. Don't claim "tickets match the data" to Helen.
- Other leads from cross-checking tickets with the data: Ironvale's older phone has late push notifications (filed before release) and its misses went from 1 to 7. Halfmoon was travelling from 14 Aug (her drop may not be 4.2, but she was still pinged first at home, so what location does proximity use?). Duplicate invoice seats for Halfmoon and The Undertow while handlers ask "is the account still active". Export-history requests (churn signal?). The wiki has no definition of "active responder" for billing.
- The ping wait cut has no documented goal or success criteria anywhere (roadmap, release notes, changelog, handoff). Release notes went only to handlers; nothing shows responders were told, and several handlers asked what the timeout was. Responders accept pings; handlers can't answer on their behalf and can only override routing.
- Session 4 (x-ray vision, read `00-rook/code/dispatch-routing/`): 4.2 changed only three values in `config.py`: ping wait 90→60s, travel-time weight 45→60%, yes-rate weight 40→25% (skills 15%, unchanged). The yes-rate score (`history.py`) starts at 50/100, gets +8 for a take and −12 for a turn-down *or* a miss (`offer.py` treats them the same), and stays between 0 and 100. The only thing that adds points is taking a job: nothing fades over time, no reset, no handler override. A 2019 TODO about drifting back toward 50 was never built. Scores live only in program memory, so a restart may reset everyone to 50 (ask Wen).
- Answered Marcus's 14 Aug question: the weights apply to every responder the same way. Nothing checks tenure or past turn-downs, and there were no new responders after release. Replaying scores from the pings: all 16 were at 80–100 on release day, so there was no "already turning down" group. Vesper, Farlight, The Undertow and Meteor Mite fell to 0 and stayed there; the other 12 recovered to 72–100. Whether this was deliberate is still for Wen. Slack reply drafted, not posted.
- Correction to "starved": a score of 0 doesn't stop them being asked. Since 19 Aug the four got 20 pings, 16 of them as first choice, and missed 15 (took 1). They used to miss ~1 in 30. That looks more like pings not reaching their phones (matches the "my phone never goes off" tickets) than slow answers. The push code (`offer.py` push_to_device) is an empty placeholder. Ask Marcus/Wen whether pings to these four are being delivered.
- Routing stopping early is not in this code: `offer.py` goes down the whole list until someone says yes. Data: when a responder from another area misses or says no, routing always continues (293/293), usually to the local responder (245). When the local responder fails, it stops 82/126 times (Eastgate, the only area with two responders, stops 11/36). 82 of the 97 callouts nobody took got one ping, always to the local responder. The stop rule must live elsewhere (what "region" means in `availability.available_for`, which is an empty placeholder, or outside routing). The rate didn't change with 4.2; 4.2 made local responders miss more (6 → 37 first-ping misses). Removing the stop may help more than going back to 90s. Also, outsiders are often ranked above the local responder despite travel time being 60%, so ask how travel time and location are computed.
