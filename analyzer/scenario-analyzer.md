# Store FAQ bot that picks an answer from the help center — Analyzer Outline

Machine-readable analyzer for auditing whether the FAQ bot's checks actually split the work.

---

## Analyzer Identity

```yaml
analyzer_id: faq-bot-audit-analyzer
product_shell: Five-check auditor
specimen: Store FAQ bot that picks an answer from the help center
stakes: Shoppers get the wrong policy and leave the cart
standard_line: The answer matches the shopper's real ask — not a nearby FAQ about the same product
```

---

## Input Schema

```yaml
input:
  type: object
  required:
    - failing_input
    - bot_response
    - expected_intent
  properties:
    failing_input:
      type: string
      description: The shopper's original question (short mobile question with product names in the middle)
      example: "refund for wrong size on the Trail Jacket, not a shipping question"
    bot_response:
      type: string
      description: The FAQ bot's actual answer
    expected_intent:
      type: string
      enum: [refund, return, cancel, shipping, delivery, sizing, other]
    source:
      type: string
      default: "Store help-desk chat logs"
```

---

## Check Dimensions

```yaml
checks:
  - id: unowned
    label: Unowned territory
    description: No head currently owns catching "refund," "return," or "cancel" as the deciding signal
    measurement: mass-across-boundary
    threshold: "> 0.15 flags"
    current_rating: 4
    severity: top_crack

  - id: copies
    label: Redundant copies
    description: Multiple heads encoding the same product-name signal
    measurement: max_cross_head_similarity
    threshold: "> 0.80 flags"
    current_rating: 2

  - id: room
    label: Room for new signal
    description: Capacity for a new head to own intent keywords
    measurement: per_head_entropy_vs_uniform
    threshold: "< 0.3 flags (too concentrated)"
    current_rating: 1

  - id: stitch
    label: Stitch coherence
    description: How well heads combine at the concat layer
    measurement: ablation_delta
    threshold: "> 0.25 flags"
    current_rating: 2

  - id: ablation
    label: Ablation sensitivity
    description: Impact of zeroing one head before concat
    measurement: ablation_delta
    threshold: "> 0.30 flags"
    current_rating: 1
```

---

## Analysis Pipeline

```yaml
pipeline:
  - step: ingest
    action: Parse failing input from Store help-desk chat logs
    output: tokenized_query

  - step: extract_signals
    action: Identify product names and intent keywords in query
    signals:
      product_names: ["Nova Buds", "Trail Jacket"]
      intent_keywords: ["refund", "return", "cancel"]

  - step: run_checks
    action: Execute each check dimension
    for_each: checks
    produce: per_check_measurement

  - step: score
    action: Compare measurements against thresholds
    produce: check_scores

  - step: identify_top_crack
    action: Find highest-severity failing check
    current_result: unowned
    rationale: "Nothing currently owns catching 'refund,' 'return,' or 'cancel' as the deciding signal, which is exactly why 'Trail Jacket' outscores 'refund' in that message."

  - step: emit_ruling
    action: Generate call with severity story
    call: "Hold. Nothing currently owns catching \"refund,\" \"return,\" or \"cancel\" as the deciding signal, which is exactly why \"Trail Jacket\" outscores \"refund\" in that message."
```

---

## Output Schema

```yaml
output:
  type: object
  properties:
    specimen_analyzed:
      type: string
    check_results:
      type: array
      items:
        type: object
        properties:
          check_id:
            type: string
          rating:
            type: integer
            minimum: 1
            maximum: 5
          measurement_value:
            type: number
          threshold_breached:
            type: boolean
          finding:
            type: string
    top_crack:
      type: string
    severity_story:
      type: string
    call:
      type: string
    tripwire:
      type: object
      properties:
        cadence:
          type: string
        owner:
          type: string
        drift_threshold:
          type: string
```

---

## Reference Output (Builder's Run)

```yaml
specimen_analyzed: "Store FAQ bot that picks an answer from the help center"
check_results:
  - check_id: unowned
    rating: 4
    measurement_value: 0.42
    threshold_breached: true
    finding: "No head owns intent keywords; product name dominates"
  - check_id: copies
    rating: 2
    measurement_value: 0.67
    threshold_breached: false
    finding: "Some redundancy in product-name encoding"
  - check_id: room
    rating: 1
    measurement_value: 0.58
    threshold_breached: false
    finding: "Adequate capacity for new signal head"
  - check_id: stitch
    rating: 2
    measurement_value: 0.19
    threshold_breached: false
    finding: "Concat layer combines heads adequately"
  - check_id: ablation
    rating: 1
    measurement_value: 0.12
    threshold_breached: false
    finding: "Single-head ablation has minimal impact"
top_crack: unowned
severity_story: "Nothing currently owns catching 'refund,' 'return,' or 'cancel' as the deciding signal, which is exactly why 'Trail Jacket' outscores 'refund' in that message."
call: "Hold. Nothing currently owns catching \"refund,\" \"return,\" or \"cancel\" as the deciding signal, which is exactly why \"Trail Jacket\" outscores \"refund\" in that message."
tripwire:
  cadence: "Re-run Daily for the first three days after the rebuild starts Monday, then weekly through Marisol's six-week window."
  owner: "Kaveh"
  drift_threshold: "If the deciding check drifts more than one step from this ruling → Kaveh, ahead of the board meeting."
```

---

## Failing Inputs (from Store help-desk chat logs)

```yaml
test_inputs:
  - input: "how long do i have to return the Nova Buds after they ship"
    expected_intent: return
    product_mentioned: "Nova Buds"

  - input: "Nova Buds delivery says Friday — can i still cancel"
    expected_intent: cancel
    product_mentioned: "Nova Buds"

  - input: "refund for wrong size on the Trail Jacket, not a shipping question"
    expected_intent: refund
    product_mentioned: "Trail Jacket"
```

---

## Usage

Point this analyzer at any FAQ bot setup where the answer must match the shopper's real ask — not a nearby FAQ about the same product. The analyzer walks all five checks, produces measurements, identifies the top crack, and returns a scored audit with the call and tripwire.
