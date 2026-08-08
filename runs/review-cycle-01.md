# Review Cycle 01: Store FAQ bot that picks an answer from the help center

Reference audit cycle for the Five-check auditor, grounded in the builder's specimen.

---

## Packet

**Setup under audit:** Store FAQ bot that picks an answer from the help center  
**Stakes:** Shoppers get the wrong policy and leave the cart  
**Pass bar:** The answer matches the shopper's real ask — not a nearby FAQ about the same product  
**Usage reality:** Short mobile questions with product names in the middle  
**Source:** Store help-desk chat logs

### Sample lines audited

| Line # | Raw input |
|--------|-----------|
| 1 | how long do i have to return the Nova Buds after they ship |
| 2 | Nova Buds delivery says Friday — can i still cancel |
| 3 | refund for wrong size on the Trail Jacket, not a shipping question |

---

## Per-check measurements produced

| Check | Rating | Measurement | Value | Threshold | Status |
|-------|--------|-------------|-------|-----------|--------|
| unowned | 4 | mass-across-boundary | 0.72 | ≤ 0.25 | **FAIL** |
| copies | 2 | max cross-head similarity (L2-normalized, off-diagonal) | 0.41 | ≤ 0.30 | FAIL |
| room | 1 | per-head entropy vs uniform | 0.89 | ≥ 0.70 | PASS |
| stitch | 2 | ablation delta (zero one head before concat) | 0.18 | ≤ 0.15 | FAIL |
| ablation | 1 | ablation delta (full head removal) | 0.09 | ≤ 0.15 | PASS |

**Deciding check:** unowned (rating 4)

---

## Caught-or-missed line

**Line 3:** `refund for wrong size on the Trail Jacket, not a shipping question`

- **Bot output:** Returned shipping FAQ for Trail Jacket ("Your Trail Jacket ships in 3–5 business days…")
- **Expected output:** Refund policy for wrong-size items
- **Verdict:** MISSED — the bot latched onto "Trail Jacket" and ignored "refund" and "wrong size"
- **Root cause:** Nothing currently owns catching "refund," "return," or "cancel" as the deciding signal, which is exactly why "Trail Jacket" outscores "refund" in that message.

---

## Call

**Hold.** Nothing currently owns catching "refund," "return," or "cancel" as the deciding signal, which is exactly why "Trail Jacket" outscores "refund" in that message.

**Cycle verdict:** Hold my rating — I'll defend it

---

## Tripwire

Re-run Daily for the first three days after the rebuild starts Monday, then weekly through Marisol's six-week window. If the deciding check drifts more than one step from this ruling → Kaveh, ahead of the board meeting.

---

## Time cost

| Phase | Duration |
|-------|----------|
| Packet assembly | 2 min |
| Five-check walk | 8 min |
| Measurement collection | 5 min |
| Severity story + call | 3 min |
| Tripwire setup | 2 min |
| **Total cycle** | **20 min** |

---

*This cycle is the reference run for the Five-check auditor. A stranger auditing their own failing setup follows the same packet → measurements → caught-or-missed → call → tripwire sequence.*
