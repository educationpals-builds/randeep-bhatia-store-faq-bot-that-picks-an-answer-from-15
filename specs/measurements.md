# Measurements for Store FAQ Bot Audit

Per-check measurements for auditing the Store FAQ bot that picks an answer from the help center.

**Pass bar:** The answer matches the shopper's real ask — not a nearby FAQ about the same product

---

## 1. Unowned Mass (Check: unowned)

**What it measures:** The fraction of input signal that no component explicitly claims as its responsibility.

**How to compute:**
1. Tokenize the shopper query (e.g., "refund for wrong size on the Trail Jacket, not a shipping question")
2. For each token, check whether any routing rule or intent classifier explicitly owns it
3. Count tokens with no owner (e.g., "refund," "return," "cancel" have no owner in the current setup)
4. Compute: `unowned_mass = unowned_tokens / total_tokens`

**Threshold:** Flag if `unowned_mass > 0.15` (more than 15% of signal is unowned)

**Worked example:**
- Input: "refund for wrong size on the Trail Jacket, not a shipping question"
- "refund," "wrong size," "not a shipping question" → unowned
- "Trail Jacket" → owned by product matcher
- Result: ~60% unowned mass → **FLAGGED**

---

## 2. Max Cross-Head Similarity (Check: copies)

**What it measures:** Whether multiple retrieval heads are doing redundant work by attending to the same features.

**How to compute:**
1. Extract per-head attention maps from the retrieval layer
2. Flatten each head's map to a 1D vector
3. L2-normalize each flattened vector: `v_norm = v / ||v||₂`
4. Stack normalized vectors into matrix M (rows = heads)
5. Compute similarity matrix: `S = M @ M.T`
6. Read the off-diagonal maximum: `max_sim = max(S[i,j] for i ≠ j)`

**Threshold:** Flag if `max_sim > 0.70` (two heads share more than 70% of their attention pattern)

**Worked example:**
- Head A attends to product names ("Nova Buds," "Trail Jacket")
- Head B also attends to product names
- Similarity between A and B: 0.82 → **FLAGGED** (redundant heads)

---

## 3. Per-Head Entropy vs. Uniform (Check: room)

**What it measures:** Whether each head has enough capacity to distinguish between candidate answers, or if attention is spread too thin.

**How to compute:**
1. For each head, get the attention distribution over FAQ candidates
2. Compute entropy: `H = -Σ p_i * log(p_i)`
3. Compute uniform entropy: `H_uniform = log(num_candidates)`
4. Compute ratio: `entropy_ratio = H / H_uniform`

**Threshold:** Flag if `entropy_ratio > 0.85` (attention is nearly uniform — head can't decide)

**Worked example:**
- Query: "how long do i have to return the Nova Buds after they ship"
- Head distributes attention across 12 FAQs about Nova Buds
- Entropy ratio: 0.91 → **FLAGGED** (no clear winner, head has no room to discriminate)

---

## 4. Stitch Consistency (Check: stitch)

**What it measures:** Whether the handoff between intent detection and answer retrieval preserves the shopper's actual question.

**How to compute:**
1. Record the intent label passed from classifier to retriever
2. Record the top retrieved FAQ's category
3. Compare: `stitch_match = (intent_label == faq_category)`
4. Over a batch, compute: `stitch_rate = matches / total`

**Threshold:** Flag if `stitch_rate < 0.80` (more than 20% of queries have mismatched handoffs)

**Worked example:**
- Query: "Nova Buds delivery says Friday — can i still cancel"
- Intent classifier outputs: "shipping_inquiry" (latched onto "delivery")
- Retriever returns: shipping FAQ (not cancellation FAQ)
- Stitch mismatch → contributes to flag

---

## 5. Ablation Delta (Check: ablation)

**What it measures:** How much the output changes when a single head is zeroed out before the final concatenation.

**How to compute:**
1. Run the full model on a query, record output logits: `y_full`
2. For each head h:
   - Zero out head h's contribution before concat
   - Run forward pass, record: `y_ablated_h`
   - Compute delta: `Δ_h = ||y_full - y_ablated_h||₂`
3. Report max delta: `ablation_delta = max(Δ_h)`

**Threshold:** Flag if `ablation_delta < 0.10` (no single head matters enough — work isn't split)

**Worked example:**
- Query: "refund for wrong size on the Trail Jacket, not a shipping question"
- Ablating the product-name head: Δ = 0.08
- Ablating all other heads: Δ < 0.05
- Max delta: 0.08 → **FLAGGED** (removing any head barely changes output — no real division of labor)

---

## Summary Table

| Check | Measurement | Compute | Threshold | Flag When |
|-------|-------------|---------|-----------|-----------|
| unowned | Unowned mass | unowned_tokens / total_tokens | > 0.15 | Too much signal has no owner |
| copies | Max cross-head similarity | max off-diagonal of normalized head similarity matrix | > 0.70 | Heads are redundant |
| room | Entropy ratio | head_entropy / uniform_entropy | > 0.85 | Head can't discriminate |
| stitch | Stitch rate | intent-to-faq category matches / total | < 0.80 | Handoff drops the question |
| ablation | Ablation delta | max L2 change when zeroing one head | < 0.10 | No head carries unique load |

---

## Current Ratings (from audit)

```json
{"unowned":4,"copies":2,"room":1,"stitch":2,"ablation":1}
```

**Top crack:** unowned (rated 4 — most severe)

**Deciding finding:** Nothing currently owns catching "refund," "return," or "cancel" as the deciding signal, which is exactly why "Trail Jacket" outscores "refund" in that message.
