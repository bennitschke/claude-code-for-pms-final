# A Missed Ping Isn't a No

One-pager · Product, Dispatch · 9 October 2026 · For Helen Achebe

## Problem
Since 4.2 shipped on 12 August, Farlight has stopped getting work. Farlight's handler is Linda Pruitt. Through July, Farlight was pinged 11–13 times a week and took 8–10. Release week brought 10 pings and 4 taken. Then it was 3, then 1, and by the week of 31 August, none. Farlight didn't start turning callouts down. The pings started being **missed**.

Routing has always scored a miss exactly like a turn-down: it lowers **recent acceptance history**, and the only way back up is to take a callout. That didn't matter while misses were rare (1–3% of pings). Then 4.2 cut the **ping wait** from 90s to 60s, and misses jumped to 21.5% in release week. On top of that, pings to Farlight and three others seem not to be reaching their phones: since 19 August those four were sent 20 pings, mostly as first choice, and missed 15. So Farlight sank in **routing priority** and can't climb back, because pings that never arrive can't be taken. From Linda's console it looks like Farlight was simply forgotten. Linda has no way to see why. Kip sees the same thing with Meteor Mite.

This isn't only a fairness issue. Callouts nobody took doubled after 4.2 (5.1% → 11.2%), and the closest responder was pinged first less often (80% → 63%). That second number is the opposite of what 4.2 promised.

## Who it's for
- **Farlight**, a responder who went quiet through no choice of their own. They take callouts on their phone and are the only one who can say yes.
- **Linda Pruitt**, Farlight's handler. She watches coverage in the console and is the one fielding "is something broken?"

## What changes for them
1. **Dispatch knows whether a ping arrived.** Every ping records whether it reached the responder's phone. If it didn't arrive, it isn't a miss.
2. **A ping that wasn't delivered never counts against a responder.** A delivered ping that's missed still counts, but a little less than a turn-down. Silence is not a no. *(Exact amount: see open questions.)*
3. **The ping wait goes back to 90s**, so responders have time to get a thumb on the screen.
4. **One-time reset for responders 4.2 pushed down.** Everyone whose recent acceptance score is lower than it was on 12 August goes back to their 12 August score. That's 10 of the 16 responders: Farlight, Vesper, The Undertow and Meteor Mite (all now at 0), plus Ironvale, Sgt. Falkirk, Stormwrack, The Drift, Nightwell, Captain Vantage and The Longcast (now 72–96).
5. **Linda can see it.** When a ping to Farlight doesn't arrive, Farlight's coverage card says so. "Phone never goes off" turns from a mystery into something Linda can act on, like checking the device.
6. **Handlers and responders are told what happened.** This covers what went wrong in 4.2, what we're changing, and that we're resetting scores and why. Handlers get it in a form they can pass on to their responders.

All of this ships together, in one release. The reset in particular can't go out before delivery is fixed. Otherwise the four responders would miss their way back to 0 within about a week.

**What Farlight would notice:** their phone goes off again, they're back where they stood before 4.2, and having a bad signal no longer costs them future work.
**What Linda would notice:** the quiet card explains itself, and she has something true to tell Farlight.

## Success measure
- Responders getting under half their usual ping share: **4 → 0**.
- Callouts nobody took: **6% or less** (4-week average). The pre-4.2 level was 5.1%, and that was with the early-stop behaviour below already in place, so this target doesn't depend on fixing it.
- Guardrails: missed pings 4% or less; nobody above 1.5× their usual share; closest responder still pinged first.

Needs Helen's sign-off.

## What it deliberately doesn't do
- **Isn't a revert of 4.2.** The proximity weighting stays.
- **Doesn't change how a turn-down is scored.** A responder who says no still moves down the order.
- **Doesn't make scores ease back over time.** This doesn't settle Wen's 2019 question in `history.py` about whether a bad stretch should fade on its own. It stays open, on purpose.
- **No handler override of a responder's score**, and no new controls in the console. The reset happens once; it isn't a button anyone can press again.
- **Doesn't fix routing stopping early.** When the local responder fails, routing stops about two-thirds of the time, and 82 of 97 callouts nobody took got only one ping. That rule isn't in the routing code and predates 4.2. It gets its own investigation.
- **No new data about who a responder is.** Delivery status is stored as a fact about the ping, never about the person (Security Policy 4.1).
- **Doesn't change what Supply reads.** The Responder Availability Record is set only by responders and handlers; routing doesn't touch it. Delivery status lives on the ping, not in that record.
- Doesn't touch filter persistence, Availability Confidence or mutual aid.

## Open questions
- **How much less does a delivered miss cost?** Decided: less than a turn-down. Today it costs −12, the same as a turn-down, and always has; 4.2 didn't change that. The exact number is to be set with Wen and Marcus. Check it against the 4% missed-ping guardrail so that ignoring pings doesn't become free.
- **Why aren't pings arriving?** The push code in the routing service is an empty placeholder, so the real delivery path lives somewhere else. Marcus and Wen. This is the biggest unknown: items 1, 2 and 5 depend on it.
- **Rebuilding 12 August scores.** Scores live only in program memory. My replay from the pings log produced the list above. Wen needs to confirm it matches what's live, and whether a restart has already reset anyone to 50.
- **Reset side effects.** Ironvale's handler reported late push notifications on an older phone *before* 4.2, so a reset may not hold. Sgt. Falkirk and Captain Vantage are already above 1.5× their usual share, so their reset pushes against that guardrail. We'll watch both after release.
- **Out-of-cycle release?** Routing config only ships in a release. Marcus scopes the delivery work; Helen decides.
- **Communications.** Who writes the note, whether responders hear about it from their handler, in the phone app, or both (4.2's notes reached handlers only), and that Nadia's support team is briefed before release.
