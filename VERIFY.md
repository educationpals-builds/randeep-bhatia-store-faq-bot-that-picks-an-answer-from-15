# Verify: Store FAQ bot that picks an answer from the help center

Use this checklist to confirm the Five-check auditor surfaces the deciding-check finding and demands a numeric measurement for it.

---

## Stranger verification steps

1. **Open /play** with the seeded specimen already loaded (Store FAQ bot that picks an answer from the help center).

2. **Paste a failing input** from the specimen set:
   > refund for wrong size on the Trail Jacket, not a shipping question

3. **Confirm the tool surfaces the deciding check.**  
   The audit must flag **unowned** as the top crack—nothing currently owns catching "refund," "return," or "cancel" as the deciding signal.

4. **Confirm the tool demands a numeric measurement.**  
   The finding for "unowned" must include a concrete measurement request, such as:
   - "How many of the last 50 refund-related questions were routed to the correct policy answer?"
   - "What percentage of messages containing 'return' or 'cancel' matched the shopper's real ask?"

   A vague statement like "check if it works" fails. The measurement must be countable.

---

## Pass criteria

| Check | Requirement |
|-------|-------------|
| Deciding check surfaced | The audit names **unowned** as the top crack |
| Measurement demanded | The finding includes a numeric measurement (count, percentage, or ratio) |
| Standard applied | The audit references the pass bar: "The answer matches the shopper's real ask — not a nearby FAQ about the same product" |

---

## If verification fails

- If the tool does not surface "unowned" as the deciding check, review the check_ratings in charter.md and confirm the prompt pack embeds them correctly.
- If no numeric measurement is demanded, the prompt pack is missing the measurement requirement in its audit instructions.

---

## Tripwire reminder

After verification passes, the standing alarm applies:

> Re-run Daily for the first three days after the rebuild starts Monday, then weekly through Marisol's six-week window. If the deciding check drifts more than one step from this ruling → Kaveh, ahead of the board meeting.
