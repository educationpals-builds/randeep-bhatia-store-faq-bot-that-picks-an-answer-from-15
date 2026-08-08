# Store FAQ Bot That Picks an Answer From the Help Center

## One-Paste Spec for the Five-check Auditor

This blueprint defines a conversational auditor calibrated on a store FAQ bot that picks an answer from the help center. The auditor walks five checks, scores each, identifies the deciding crack, and returns a ruling with an alarm.

---

## Stakes

Shoppers get the wrong policy and leave the cart

---

## Pass Bar (Standard Line)

The answer matches the shopper's real ask — not a nearby FAQ about the same product

---

## Usage Reality

Short mobile questions with product names in the middle

---

## Specimen Sentences (From Store help-desk chat logs)

```
how long do i have to return the Nova Buds after they ship
Nova Buds delivery says Friday — can i still cancel
refund for wrong size on the Trail Jacket, not a shipping question
```

---

## Five Checks and Ratings

| Check | Rating | Description |
|-------|--------|-------------|
| unowned | 4 | Work that no part currently owns |
| copies | 2 | Duplicate effort across parts |
| room | 1 | Headroom for growth or change |
| stitch | 2 | Handoff quality between parts |
| ablation | 1 | What breaks when one part is removed |

---

## Top Crack

**unowned**

The deciding check is "unowned" — the highest-rated failure mode.

---

## Severity Story

Nothing currently owns catching "refund," "return," or "cancel" as the deciding signal. When a shopper types "refund for wrong size on the Trail Jacket, not a shipping question," the bot latches onto "Trail Jacket" and returns shipping info instead of the refund policy. The shopper leaves the cart.

---

## Ship Call

Hold. Nothing currently owns catching "refund," "return," or "cancel" as the deciding signal, which is exactly why "Trail Jacket" outscores "refund" in that message.

---

## Watch Tripwire

Re-run Daily for the first three days after the rebuild starts Monday, then weekly through Marisol's six-week window. If the deciding check drifts more than one step from this ruling → Kaveh, ahead of the board meeting.

---

## Cycle Verdict

Hold my rating — I'll defend it

---

## How a Stranger Uses This Auditor

A stranger describes their own failing setup:
1. What it is supposed to do
2. Who gets hurt when it fails
3. A few real failing inputs

The Five-check auditor walks all five checks conversationally, proposes findings with the measurement that would confirm each, and returns:
- A scored audit (ratings per check)
- The top crack (deciding check)
- A severity story grounded in a real failing input
- A call (ship / hold / conditional)
- A tripwire (cadence, owner, drift threshold)

---

## Required Measurements

Each finding must name the measurement that confirms it:

- **mass-across-boundary**: How much work crosses from one part to another
- **max cross-head similarity**: L2-normalized flattened per-head maps, stack, matmul against transpose, read the off-diagonal
- **per-head entropy versus uniform**: Distribution spread compared to baseline
- **ablation delta**: Zero one head before the concat, measure the change

Thresholds are defined in `specs/measurements.md`.

---

## Audit Routine Reference

- **Change triggers**: Any update to FAQ content or bot routing logic
- **Calendar floor**: Weekly minimum, daily during rebuild
- **Reviewer roles**: Kaveh (alarm owner), Marisol (six-week window owner)
- **Sampling rule**: Pull from Store help-desk chat logs
- **Gate**: Attach measurements or it doesn't count
