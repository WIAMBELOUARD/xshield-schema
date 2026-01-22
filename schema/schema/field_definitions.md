# X-SHIELD Field Definitions (v0.1)

This document defines each field so generation + rendering are consistent.

## 1) Identification
- **schema_version (string)**: Schema version (e.g., "0.1").
- **episode_id (string)**: Unique ID for the self-healing episode (e.g., "s04_ep1").
- **session_id (string)**: Scenario/session (e.g., "s04").
- **timestamp_start (ISO-8601)**: When the self-healing episode begins.
- **timestamp_end (ISO-8601)**: When the episode ends (action applied / finalized).

## 2) Trigger (why did self-healing start?)
**trigger.trigger_type (enum)**  
One of:
- REPEATED_FAILURE
- UNCERTAINTY_SPIKE
- INCONSISTENCY
- AGENT_TIMEOUT
- OUTPUT_INVALID
- CONFLICTING_RECOMMENDATIONS

Optional:
- **threshold**: threshold used by rule (e.g., 0.6)
- **observed_value**: observed measure (e.g., 0.8)
Required:
- **trigger_rule_id**: identifier of the rule that fired (e.g., "T1")

## 3) Evidence (3–5 top facts)
**evidence[]** entries must be short and audit-friendly.
Each entry contains:
- **fact**: human-readable fact name (e.g., "Wrong answers in last 5 items")
- **value**: metric/value (number or short string)
- **window**: scope (e.g., "last_5", "t-30s..t")
- **source_event_type**: log event type producing this evidence
Optional:
- **source_event_ids**: list of event IDs supporting the fact

## 4) Diagnosis (what do we think is happening?)
- **diagnosis.hypothesis (enum)**:
  - MISCONCEPTION
  - DISENGAGEMENT
  - AMBIGUITY
  - SYSTEM_FAULT
  - DATA_QUALITY
  - UNKNOWN
- **diagnosis.confidence (0..1)**: numeric confidence
- **diagnosis.diagnosis_rule_id**: rule identifier (e.g., "D2")
Optional:
- **diagnosis.alternatives[]**: up to 3 alternative hypotheses with confidences

## 5) Recovery action (what do we do?)
- **recovery_action.action_type (enum)**:
  - PREREQUISITE_STEP
  - EASIER_ITEMS
  - DIAGNOSTIC_ITEM
  - HINT
  - FALLBACK_AGENT
  - RETRY
  - CIRCUIT_BREAK
  - DEGRADED_MODE
- **recovery_action.parameters (object)**: flexible parameters (MVP-friendly)
- **recovery_action.action_rule_id**: rule identifier (e.g., "R3")

## 6) Expected effect (how do we know it worked?)
- **expected_effect.metric**: metric to monitor (e.g., "error_rate_last_5")
- **expected_effect.target_direction**: UP / DOWN / STABLE
- **expected_effect.check_after_steps**: integer steps after which we check
Optional:
- **expected_effect.success_criteria**: short human-readable threshold

## 7) Explanation (human-facing + technical)
- **explanation.teacher_summary**: 2–3 sentences, non-technical
- **explanation.technical_trace**: compact trace referencing rules and computed values
Optional:
- **explanation.limitations**: short uncertainty/limitations statement

## 8) Audit (traceability)
- **audit.orchestrator.name** and **audit.orchestrator.version**
- **audit.agents_involved[]**: list of agent names + roles (+ optional versions)
- **audit.source_event_range.first_event_id / last_event_id**
- **audit.hashes.log_sha256**: hash of raw log slice used
- **audit.hashes.explanation_sha256**: hash of explanation output
Optional:
- **audit.notes**: short note (e.g., "fallback due to timeout")
