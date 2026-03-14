---
name: prd-spec
description: Use when the task involves writing or refining a PRD, feature specification, user stories, acceptance criteria, scope, non-goals, edge cases, rollout notes, or launch requirements.
---

# PRD Spec

Use this skill when the user needs a product decision turned into an executable specification.

This repository focuses on Alloy-style configurable products. Keep the spec aligned with the existing domain language and constraints:
- object classes and modules matter more than loose feature labels
- workflow behavior is assembled from actions, triggers, notifications, templates, forms, functions, and policies
- Express and Enterprise must not be treated as equivalent capability envelopes
- business logic should be distinguished from platform capability

## Workflow

1. Confirm the feature or initiative being specified.
2. Lock the scope:
   - goal
   - non-goals
   - target users
   - constraints
3. Translate intent into buildable requirements:
   - user flows
   - functional requirements
   - acceptance criteria
   - edge cases
   - dependencies
4. Add launch discipline where relevant:
   - rollout notes
   - risks
   - instrumentation expectations
   - unresolved questions
5. For Alloy/ITSM specifications, explicitly state:
   - affected module and object classes
   - whether the change is platform capability, business logic, or editioning
   - which workflow surfaces are touched
   - whether the proposal must work in Express, Enterprise, or both

## Output Rules

- Make requirements testable.
- Distinguish required behavior from implementation suggestions.
- Avoid fake precision when source inputs are weak.
- If the document mixes strategy and spec, split them into separate sections.
- Call out unsupported assumptions, especially around Express customization limits.
- If the requirement depends on business rules, policies, routing, escalation, or notifications, name them explicitly.

## References

- For PRD sections and acceptance criteria patterns, read [references/templates.md](references/templates.md).
