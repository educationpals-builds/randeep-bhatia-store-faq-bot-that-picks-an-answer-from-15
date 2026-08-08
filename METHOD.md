# The Five Checks: PRISM

When a setup claims to split work across multiple heads—retrieval, classification, routing—these five checks reveal whether the split actually holds under real traffic.

---

## P — Partition the Space

Every input must land in exactly one bucket. If two heads both claim "product questions," neither owns the boundary. Test: take a real message and ask which head is responsible. If the answer is "both" or "it depends," the partition leaks.

**In the Store FAQ bot:** "refund for wrong size on the Trail Jacket, not a shipping question" should land in one head. If the product-name head and the policy head both activate, the partition failed.

---

## R — Run in Parallel

Each head should be able to score its domain without waiting on another head's output. If Head B needs Head A's answer before it can act, you don't have parallel heads—you have a pipeline pretending to be a split.

**In the Store FAQ bot:** The retrieval head shouldn't need to know whether the intent head already classified "cancel" before it searches the help center.

---

## I — Individuate the Pattern

Each head must recognize its own signal, not borrow salience from a neighbor. When "Trail Jacket" outscores "refund" because the product-name pattern is louder, the refund head has no individual pattern—it's drafting on someone else's match.

**In the Store FAQ bot:** The word "refund" should light up the returns head on its own, regardless of whether "Trail Jacket" appears in the same sentence.

---

## S — Stitch the Spectra

After heads score independently, something must reconcile their outputs into one answer. If two heads return high confidence and nothing arbitrates, the user gets whichever head spoke last—or whichever the framework happened to poll first.

**In the Store FAQ bot:** When both "Nova Buds" (product) and "cancel" (action) score high, the stitch layer decides which FAQ to surface. No stitch means the bot picks arbitrarily.

---

## M — Map What Each Head Sees

You should be able to print, for any input, exactly what each head matched and why. If a head's reasoning is opaque—"it just picked shipping"—you can't debug the split. Mapping is the audit trail.

**In the Store FAQ bot:** For "how long do i have to return the Nova Buds after they ship," you should see: product head matched "Nova Buds," policy head matched "return," time head matched "how long." Without that map, you're guessing why shipping won.

---

## The Anti-Pattern: Collapse to Monochrome

When one head's signal drowns out all others, the split collapses. Every input looks the same color—product name, product name, product name—even when the shopper asked about refunds, cancellations, or sizing.

This is what "unowned" means in practice: no head owns the deciding signal, so the loudest head wins by default. The fix isn't to tune weights—it's to give the unowned signal ("refund," "return," "cancel") a head that actually claims it.

---

These five checks apply to any setup that claims to split work: FAQ bots, ticket routers, document classifiers, multi-agent pipelines. If the split doesn't survive PRISM, the setup is one head pretending to be many.
