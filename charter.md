# Store FAQ bot that picks an answer from the help center

## Audit charter

This document records the full audit of a Store FAQ bot that picks an answer from the help center. The bot is supposed to match shoppers with the right help-center article, but shoppers ask about refunds and the bot answers with shipping times because it latched onto the product name.

**What breaks if the parts aren't really splitting the work:**  
Shoppers get the wrong policy and leave the cart

---

## Standard committed

> The answer matches the shopper's real ask — not a nearby FAQ about the same product

---

## Usage reality

Short mobile questions with product names in the middle

---

## Inputs audited

**Source:** Store help-desk chat logs

**Lines:**

1. how long do i have to return the Nova Buds after they ship
2. Nova Buds delivery says Friday — can i still cancel
3. refund for wrong size on the Trail Jacket, not a shipping question

---

## Check findings

| Check | Score |
|-------|-------|
| Unowned | 4 |
| Copies | 2 |
| Room | 1 |
| Stitch | 2 |
| Ablation | 1 |

---

## Deciding check

**Top crack:** unowned

Nothing currently owns catching "refund," "return," or "cancel" as the deciding signal. When a shopper writes "refund for wrong size on the Trail Jacket, not a shipping question," the bot sees "Trail Jacket" and scores it higher than "refund" — because no head is assigned to recognize refund-intent words as the primary signal. The shopper explicitly says "not a shipping question," yet the bot returns shipping info anyway. The support agent has to manually redirect, and by then the shopper may have already abandoned the cart.

---

## Call

Hold. Nothing currently owns catching "refund," "return," or "cancel" as the deciding signal, which is exactly why "Trail Jacket" outscores "refund" in that message.

**Pressure response:** Hold my rating — I'll defend it

---

## Tripwire

Re-run Daily for the first three days after the rebuild starts Monday, then weekly through Marisol's six-week window. If the deciding check drifts more than one step from this ruling → Kaveh, ahead of the board meeting.

---

## Summary

This audit found the Store FAQ bot fails the committed standard because no component owns intent-signaling words like "refund," "return," or "cancel." The unowned check scored highest (4). The call is to hold until that gap is addressed. Kaveh owns the alarm if the deciding check drifts.
