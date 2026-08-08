# Five-check auditor

**Worked example:** Store FAQ bot that picks an answer from the help center

---

## What this is

A discipline for auditing whether your setup's checks actually split the work. Walk five checks, score each, surface the deciding crack, and ship a call with a tripwire.

**Stakes in the worked example:** Shoppers get the wrong policy and leave the cart

---

## Verdict

**Hold.** Nothing currently owns catching "refund," "return," or "cancel" as the deciding signal, which is exactly why "Trail Jacket" outscores "refund" in that message.

---

## Tripwire

Re-run Daily for the first three days after the rebuild starts Monday, then weekly through Marisol's six-week window. If the deciding check drifts more than one step from this ruling → Kaveh, ahead of the board meeting.

---

## One-paste rebuild block

```
Specimen: Store FAQ bot that picks an answer from the help center
Standard: The answer matches the shopper's real ask — not a nearby FAQ about the same product
Deciding check: unowned
Call: Hold
Tripwire owner: Kaveh
Cadence: Daily × 3 days, then weekly × 6 weeks
```

See [charter.md](charter.md) for the full audit with scored checks, severity story, and source lines.

---

## For strangers

Paste your own failing setup—what it does, who gets hurt when it fails, and a few real failing inputs. The auditor walks five checks conversationally, proposes findings with the measurement that would confirm each, and returns a scored audit with a severity story, a call, and a tripwire.

---

## Sample asks

- "My support ticket router keeps assigning billing complaints to the technical team because the word 'account' appears in both categories. Three real tickets attached."
- "The lead-scoring bot flags every message mentioning 'enterprise' as high-priority, even when it's a student asking about enterprise pricing for a class project."
- "Our appointment reminder system sends the wrong time zone to customers who book from mobile—here are four complaints from this week."

<!-- educationpals-build-verified -->
