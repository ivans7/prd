---
name: product-insights
description: Use when the task involves product discovery, analysis of the user's own product, user pain diagnosis, workflow review, funnel or adoption analysis, opportunity mapping, or prioritization before writing specifications.
---

# Product Insights

Use this skill when the user needs to understand the product problem, current product behavior, or improvement opportunities before locking a specification.

This repository is centered on Alloy-style products and ITSM workflows. Prefer the product language already used in the source materials:
- platform capabilities vs business logic
- workflows, actions, triggers, notifications, templates, policies
- Tickets, Change Requests, Approval Requests, Inventory Items, Knowledge Base, Service Desk, IT Assets
- Express vs Enterprise capability boundaries when they affect feasibility

## Workflow

1. Frame the analysis target:
   - product area, workflow, object class, or user journey
   - affected users or operators
   - desired business or support outcome
2. Choose only the relevant lenses:
   - problem discovery and unmet needs
   - workflow friction and unnecessary steps
   - value proposition clarity
   - activation, conversion, or adoption gaps
   - feature-model coherence across objects and modules
3. For Alloy/ITSM contexts, explicitly check:
   - whether the issue belongs to platform machinery or configurable business logic
   - whether the proposal assumes Enterprise-only capabilities in Express
   - whether the affected scope is Ticket-only, Change-only, Approval-only, or cross-object
4. Produce a diagnosis:
   - observed issues
   - likely causes
   - supporting evidence
   - what to change first
   - what remains uncertain

## Output Rules

- Separate facts, assumptions, and inferences.
- Distinguish user pain from implementation pain.
- When discussing workflows, name the exact objects and automation surfaces involved.
- Do not recommend unsupported Express behavior such as custom forms, custom functions, or arbitrary programming during object creation.
- End with a ranked action list or decision memo, not just observations.

## References

- For reusable product audit, discovery, and workflow review formats, read [references/templates.md](references/templates.md).
