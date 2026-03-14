---
name: metrics-and-experiments
description: Use when the task involves defining product metrics, KPI trees, north star metrics, success criteria, experiment design, rollout guardrails, or post-launch evaluation.
---

# Metrics And Experiments

Use this skill when the user needs a measurement framework or experiment plan for a product decision.

For Alloy/ITSM topics, define metrics against real product behaviors:
- ticket creation, assignment, routing, escalation, closure, reopen
- change request throughput and approval latency
- automation adoption for rules, policies, notifications, and templates
- admin efficiency, workflow configurability, and edition upgrade triggers
- avoid vanity outputs such as raw object counts without behavioral meaning

## Workflow

1. Define the decision being measured:
   - product investment
   - launch
   - funnel optimization
   - retention change
2. Build the minimum useful measurement model:
   - north star or primary success signal
   - leading indicators
   - guardrails
   - segmentation cuts if needed
3. If experimentation is required, specify:
   - hypothesis
   - variant or intervention
   - success criteria
   - failure criteria
   - operational risks
4. Close with an interpretation plan so metrics are actionable.
5. In Alloy/ITSM contexts, name:
   - the object class or workflow being measured
   - operational guardrails for service quality
   - whether results should be segmented by edition, role, or process type

## Output Rules

- Do not list vanity metrics as success metrics.
- Tie each metric to a product behavior or business outcome.
- When metrics are weak proxies, say so.
- Prefer a small measurement set that supports a decision.
- If a change affects support operations, include guardrails for SLA, backlog, escalation, or reassignment behavior.

## References

- For KPI trees and experiment templates, read [references/templates.md](references/templates.md).
