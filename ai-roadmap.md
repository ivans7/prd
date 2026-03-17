# Alloy Navigator AI: roadmap

**Goal:** define a realistic AI roadmap for Alloy Navigator based on the current product baseline and the platform's technical constraints.

## Summary

Alloy already has a working AI foundation. The roadmap should therefore focus less on basic demo-style AI features and more on expanding practical value for end users, technicians, and service operations.

There is one critical constraint:
- AI for workflow generation, editing, and safe execution should not be treated as a near-term priority.
- That area should move forward only after the relevant workflow layer is rewritten in `C#`.

This changes the priority order. In the near and mid term, Alloy should invest in:
- operational intelligence
- asset-aware grounding
- manager insights
- controlled action drafts

It is also important to note:
- a full `Technician Copilot` inside the main work interface is not part of the current release yet;
- it should be treated as one of the first major AI milestones in the next product version.

## Roadmap principles

Priorities should progress in this order:
- `control`, `observability`, and `quality`
- `technician copilot`, `triage`, `portal`, and `technician productivity`
- `service operations intelligence`
- `asset-aware AI` and `controlled actions`
- only after the workflow layer is rewritten in `C#`: `workflow generation / editing`

## Phase 1: strengthen the foundation

- Document object AI actions and the current AI scenarios clearly.
- Add AI observability: usage, latency, cost, failure rate, and feedback.
- Introduce prompt and version control for object AI actions.
- Improve explainability by showing which sources were used in each response.
- Build an evaluation harness for key use cases: summarization, category suggestion, next steps, change risk analysis, and portal assistant.

**Outcome:** a transparent and controllable AI layer that can be scaled safely.

## Phase 2: reach parity on core service desk AI

- Deliver `Technician Copilot / Agent Assist` inside the main work interface.
- Add assignment suggestion and priority suggestion.
- Add similar ticket and duplicate detection.
- Improve the SSP assistant with follow-up questions, KB-first guidance, and draft ticket creation.
- Generate draft KB articles from ticket resolutions.
- Add similar case recommendations for technicians.

**Outcome:** Alloy gains a full technician-facing AI layer and closes the main gaps versus Freshservice, SysAid, and InvGate on core service desk AI.

## Phase 3: expand into service operations intelligence

- Add incident clustering and problem candidate detection.
- Add SLA breach risk prediction.
- Expand risk analysis for Change Requests.
- Add a manager copilot for backlog, SLA, category, trend, and KB gap analysis.
- Add escalation and reassignment recommendations for tickets at risk.

**Outcome:** Alloy reduces the gap versus Jira, ManageEngine, and Halo on operational AI.

## Phase 4: build differentiators without depending on workflow rewrite

- Expand grounding beyond incidents and KB to include:
  - assets / CI
  - software
  - contracts
  - vendors
  - locations
  - change history
- Add asset-aware AI suggestions for technicians and change managers.
- Add a controlled action layer in draft-only mode:
  - draft field updates
  - draft child work orders
  - draft change tasks
  - draft requester responses
  - human approval required
- Add AI explanations for existing automation logic:
  - explain current rule behavior
  - explain why a record was routed a certain way
  - identify possible overlap or conflict between existing rules

**Outcome:** Alloy gains areas of potential differentiation without depending on workflow layer rework.

## Phase 5: workflow-aware AI after the C# rewrite

This should be treated as a separate phase after the workflow layer is rewritten in `C#`.

Only then does it make sense to add:
- draft business rule creation from natural language
- AI-assisted workflow editing
- AI-assisted automation generation
- simulation and validation of generated workflow changes
- safe application of workflow changes with approval and audit trail

**Outcome:** workflow-aware AI is built on a maintainable and safe technical foundation rather than on the legacy workflow layer.

## Strategic focus

The most realistic and valuable Alloy differentiators in the near and mid term are:
- **Secure AI by design**: permission-aware retrieval and enterprise control.
- **Technician productivity AI**: technician copilot, object-specific AI actions, similar cases, better triage, and better summaries.
- **Operational intelligence**: clustering, SLA risk, and manager insights.
- **Asset-aware AI**: recommendations based on operational context, not just ticket text.
- **Explainable AI**: clear sources and understandable reasoning behind AI suggestions.

## Recommendation

The roadmap should not revolve around AI-based workflow authoring in the near term. Until the relevant layer is rewritten in `C#`, that path will remain expensive and risky. In the meantime, Alloy should focus on AI capabilities that already align well with the existing data model, RAG layer, permissions model, and object actions.
