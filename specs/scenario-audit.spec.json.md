{
  "spec_name": "Store FAQ bot that picks an answer from the help center",
  "spec_version": "1.0.0",
  "description": "Review routine for auditing whether the Store FAQ bot correctly splits work between intent detection and answer retrieval — ensuring shoppers get the policy they asked about, not a nearby FAQ about the same product.",
  "standard_line": "The answer matches the shopper's real ask — not a nearby FAQ about the same product",
  "stakes": "Shoppers get the wrong policy and leave the cart",
  "usage_reality": "Short mobile questions with product names in the middle",

  "change_triggers": [
    {
      "trigger_id": "ct-01",
      "name": "intent_model_update",
      "description": "Any change to the intent classification model or its training data"
    },
    {
      "trigger_id": "ct-02",
      "name": "faq_content_change",
      "description": "New FAQ entries added or existing entries modified in the help center"
    },
    {
      "trigger_id": "ct-03",
      "name": "retrieval_config_change",
      "description": "Changes to embedding model, similarity thresholds, or ranking logic"
    },
    {
      "trigger_id": "ct-04",
      "name": "product_catalog_update",
      "description": "New products added that share names with policy-related terms"
    }
  ],

  "calendar_floor": {
    "initial_cadence": "daily",
    "initial_duration_days": 3,
    "initial_start": "Monday (rebuild start)",
    "steady_state_cadence": "weekly",
    "steady_state_duration": "six-week window",
    "owner": "Marisol"
  },

  "reviewer_roles": [
    {
      "role": "primary_reviewer",
      "name": "Marisol",
      "responsibility": "Owns the six-week review window; runs weekly checks after initial daily period"
    },
    {
      "role": "escalation_owner",
      "name": "Kaveh",
      "responsibility": "Receives alert if deciding check drifts more than one step from ruling; acts ahead of board meeting"
    }
  ],

  "sampling_rule": {
    "source": "Store help-desk chat logs",
    "sample_size": 10,
    "sample_method": "random_from_last_period",
    "required_characteristics": [
      "Contains product name in the middle of the question",
      "Includes at least one policy-related term (refund, return, cancel, exchange)",
      "Mobile-length queries (under 20 words)"
    ],
    "example_sentences": [
      "how long do i have to return the Nova Buds after they ship",
      "Nova Buds delivery says Friday — can i still cancel",
      "refund for wrong size on the Trail Jacket, not a shipping question"
    ]
  },

  "checks": [
    {
      "check_id": "unowned",
      "name": "Unowned",
      "description": "Nothing currently owns catching 'refund,' 'return,' or 'cancel' as the deciding signal",
      "rating": 4,
      "is_top_crack": true,
      "measurement": {
        "name": "mass_across_boundary",
        "description": "Fraction of attention mass that crosses from intent-detection head to retrieval head on policy keywords",
        "threshold": 0.15,
        "flag_condition": "below_threshold",
        "unit": "fraction"
      }
    },
    {
      "check_id": "copies",
      "name": "Copies",
      "description": "Multiple heads attending to the same product-name tokens instead of splitting work",
      "rating": 2,
      "is_top_crack": false,
      "measurement": {
        "name": "max_cross_head_similarity",
        "description": "Maximum cosine similarity between L2-normalized flattened per-head attention maps (stack, matmul against transpose, read off-diagonal max)",
        "threshold": 0.85,
        "flag_condition": "above_threshold",
        "unit": "cosine_similarity"
      }
    },
    {
      "check_id": "room",
      "name": "Room",
      "description": "Heads have capacity to attend to policy terms but are not using it",
      "rating": 1,
      "is_top_crack": false,
      "measurement": {
        "name": "per_head_entropy_vs_uniform",
        "description": "Ratio of actual attention entropy to maximum possible entropy (uniform distribution) per head",
        "threshold": 0.6,
        "flag_condition": "below_threshold",
        "unit": "ratio"
      }
    },
    {
      "check_id": "stitch",
      "name": "Stitch",
      "description": "How well the outputs from different heads combine to produce the final answer",
      "rating": 2,
      "is_top_crack": false,
      "measurement": {
        "name": "stitch_coherence",
        "description": "Correlation between combined head outputs and correct answer embedding",
        "threshold": 0.7,
        "flag_condition": "below_threshold",
        "unit": "correlation"
      }
    },
    {
      "check_id": "ablation",
      "name": "Ablation",
      "description": "What happens when you zero out one head before the concat — does the answer change?",
      "rating": 1,
      "is_top_crack": false,
      "measurement": {
        "name": "ablation_delta",
        "description": "Change in output logits when zeroing one head before concatenation",
        "threshold": 0.1,
        "flag_condition": "below_threshold",
        "unit": "logit_delta"
      }
    }
  ],

  "attach_or_it_doesnt_count_gate": {
    "required_attachments": [
      "Raw chat log excerpt with timestamps",
      "Per-check measurement values with computation logs",
      "Screenshot or export of the bot's actual response",
      "Annotated comparison: shopper's ask vs. FAQ matched"
    ],
    "validation": "Review cycle is invalid if any required attachment is missing"
  },

  "ruling": {
    "call": "Hold. Nothing currently owns catching \"refund,\" \"return,\" or \"cancel\" as the deciding signal, which is exactly why \"Trail Jacket\" outscores \"refund\" in that message.",
    "verdict": "Hold my rating — I'll defend it"
  },

  "tripwire": {
    "condition": "If the deciding check drifts more than one step from this ruling",
    "action": "Alert Kaveh, ahead of the board meeting",
    "full_text": "Re-run Daily for the first three days after the rebuild starts Monday, then weekly through Marisol's six-week window. If the deciding check drifts more than one step from this ruling → Kaveh, ahead of the board meeting."
  }
}