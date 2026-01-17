# Express Edition Conceptual Model

This document describes Express Edition as a conceptually constrained projection of the underlying platform model. For the complete platform conceptual model, see [Platform Conceptual Model](platform-conceptual-model.md). For detailed object definitions, see [Express Business Objects Reference](express-business-objects-reference.md).

---

## 1. Purpose of Express Edition

### 1.1 Target Scenarios

Express Edition addresses organizations and scenarios characterized by:

| Scenario Characteristic | Express Fit |
|------------------------|-------------|
| **Organizational Size** | Small to mid-sized IT teams with straightforward support structures |
| **Process Maturity** | Organizations adopting structured IT management for the first time |
| **Service Complexity** | Single-tier or simple multi-tier support without complex escalation paths |
| **Asset Scope** | Computer and equipment tracking without complex financial asset management |
| **Customization Need** | Organizations preferring ready-to-use processes over custom workflow design |

### 1.2 Intentional Simplifications

Express intentionally reduces complexity in several conceptual areas:

**Simplified Service Desk Model**
Rather than exposing ITIL-aligned object classes (Incident, Service Request, Problem), Express provides a single **Ticket** object class for task-based work tracking. ITIL objects are hidden, not consolidated—Ticket is a renamed Work Order.

**Pre-configured Workflows**
Rather than requiring workflow design expertise, Express provides operational workflows with adjustable parameters. Organizations configure behavior within established patterns rather than designing patterns from scratch.

**Consolidated Asset View**
Rather than separating technical configuration (CI) from financial tracking (Asset), Express presents a unified view through **Inventory Items** and **Computers**, reducing the conceptual overhead of dual-object management.

**Out-of-Box Readiness**
Express is designed to be adoptable with a pre-configured baseline. Pre-built workflows and automation patterns provide a functional starting point while keeping customization within controlled boundaries.

---

## 2. Express as a Platform Projection

Express Edition is not a different product—it is the same platform with a constrained conceptual surface.

### 2.1 Conceptual Foundation

Express is best understood as a constrained projection of the same platform mechanisms:
- The platform concepts (business objects, relationships, workflow-driven behavior, and integrations) remain consistent.
- Express constrains *how far* those mechanisms can be customized, emphasizing configuration over workflow engineering.
- Upgrade expands the exposed conceptual surface (more object classes and deeper workflow customization) while preserving business data and relationships.

### 2.2 Projection Mechanisms

Express is formed from Enterprise through three mechanisms:

#### Aliasing (Renaming)

| Enterprise Term | Express Term |
|-----------------|--------------|
| Work Order | **Ticket** |
| Hardware | **Inventory Item** |
| Trigger | **Business Rule** |

#### Hiding (Not Removing)

Hidden platform object classes are not exposed as part of the Express conceptual surface:

| Hidden Category | Objects |
|-----------------|---------|
| **ITIL Service Desk** | Incident, Problem, Service Request |
| **Project Management** | Project, Project Task |
| **Service Management** | Service, SLA, Service Catalog Item |
| **Advanced CMDB** | Document, Configuration, Network |
| **Financial Assets** | Asset (as separate object) |

#### Module Consolidation

| Enterprise Modules | Express Module |
|-------------------|----------------|
| Configuration Management + Asset Management | **IT Assets** |

### 2.3 Built-in Components

Express includes components that are separate in Enterprise:

| Component | Express Status |
|-----------|---------------|
| **Inventory data population capabilities** | Inventory can be populated through multiple methods (manual, import, integrations, network discovery, agent-based inventory, external tools). No inventory tool is required or conceptually privileged by the platform. |

---

## 3. Object Customization Model

Express defines four classes of objects based on customization depth. This is an architectural principle, not just a limitation.

### 3.1 Customization Classes

| Class | Customization Level | Objects |
|-------|--------------------| --------|
| **Class 1** | No customization | Discovered Installations, Manufacturers |
| **Class 2** | Lookups only | Organization, Location, Vendor |
| **Class 3** | Lookups + Notifications + Templates | Computer, Inventory Item, Purchase Order, Contract, Software License, Knowledge Base Article, Person, Library Item |
| **Class 4** | Full Express customization | Ticket, Change Request, Approval Request |

### 3.2 Customization Capabilities by Class

| Capability | Class 1 | Class 2 | Class 3 | Class 4 |
|------------|---------|---------|---------|---------|
| Lookups (classifiers) | - | ✓ | ✓ | ✓ |
| Notifications | - | - | ✓ | ✓ |
| Templates | - | - | ✓ | ✓ |
| Business Policies | - | - | - | ✓* |
| Business Rules (triggers) | - | - | - | ✓** |

\* Business Policies: Full set (Prioritization, Routing, Escalation, Additional Automation) for Ticket and Change Request; Escalation only for Approval Request.

\*\* Business Rules: Available for Ticket and Change Request only. Approval Request does not support Business Rules.

### 3.3 Class 4 Objects: Full Express Customization

Ticket, Change Request, and Approval Request have the highest customization level, but with different capabilities:

**Business Policies by Object:**

| Policy Type | Ticket | Change Request | Approval Request |
|-------------|--------|----------------|------------------|
| Prioritization | ✓ | ✓ | - |
| Routing | ✓ | ✓ | - |
| Escalation | ✓ | ✓ | ✓ |
| Additional Automation | ✓ | ✓ | - |

Approval Request supports only Escalation Policy.

**Note:** Express constrains how automation is authored and extended. Conceptually, the platform still executes workflow-defined logic; Express limits authoring depth and centralizes safe tuning through configuration parameters.

---

## 4. Object Creation Model

Express shifts object creation toward a more declarative, template-driven model.

### 4.1 Full Platform: Programmable Creation

In the full platform conceptual model, object creation can be governed by user-configured workflow logic, including initialization, validation, and automation side effects.

### 4.2 Express: Declarative Creation

In Express, object creation is primarily driven by templates and controlled inputs, emphasizing predictability and ease of administration. Complex creation-time automation that would require workflow engineering is intentionally constrained.

### 4.3 Conceptual Implication

| Aspect | Express | Enterprise |
|--------|---------|------------|
| Creation logic | Declarative (field values) | Programmable (any operations) |
| Side effects on create | Not possible | Fully supported |
| Creation complexity | Simple, predictable | Unlimited |

---

## 5. Workflow Constraints

### 5.1 Actions Constraints

| Capability | Express | Enterprise |
|------------|---------|------------|
| Author new workflow actions | Not available | Full capability |
| Modify action logic depth | Constrained | Full capability |
| Control action availability/governance | Available | Available |

**Principle:** Express administrators can control *which* actions are available, but not *what* actions do.

### 5.2 Available Workflow Capabilities

**Workflow Items (Express-specific):**

| Item | Express Availability |
|------|---------------------|
| Templates | Available for declarative object initialization |
| Business Policies | For Tickets, Change Requests; Approval Requests (Escalation only) |
| Business Rules | Limited triggers for Tickets, Change Requests |

**Workflow Configuration:**

| Configuration | Express Availability |
|---------------|---------------------|
| Notifications | Enable/Disable, edit text |
| Workflow Parameters | Available |
| Lookups/Classifiers | Available |
| User Defined Fields | Available |

### 5.3 Unavailable Workflow Capabilities

| Capability | Status |
|------------|--------|
| Deep workflow authoring | Constrained relative to the full platform |
| Custom form authoring | Not available |
| Custom function authoring | Not available |
| Validation Rules | Not available |
| Object-level Macros | Not available |

---

## 6. Business Object Differences

For detailed object definitions, see [Express Business Objects Reference](express-business-objects-reference.md).

### 6.1 Terminology Mapping

| Express Term | Enterprise Equivalent | Relationship |
|--------------|----------------------|--------------|
| Ticket | Work Order | Same class, renamed |
| Inventory Item | Hardware | Same class, renamed |
| Business Rule | Trigger | Same class, renamed |
| Business Policy | Trigger | Express-specific simplification for routing, escalation, prioritization |
| Product | Product Catalog Item | Same class, renamed (for procurement; not to be confused with Service Catalog Item, which is hidden) |
| Library Item | Equipment | Same class, renamed |
| Threshold Notification Rule | Stock Rule | Simplified capability |

**Critical clarification:** Ticket in Express is a renamed **Work Order**, not a consolidation of Incident and Service Request. ITIL objects (Incident, Problem, Service Request) are hidden, not merged.

### 6.2 Change Request Specifics

Change Request exists in both editions but with differences:

| Aspect | Express | Enterprise |
|--------|---------|------------|
| Related work decomposition | Uses Tickets for task execution | Uses Work Orders for task execution |
| Related configuration linkage | May use simplified linkage semantics | Supports broader CI semantics |
| Service classification context | Reduced | Expanded |
| Automation tuning surface | Via Business Policies and configuration parameters | Via full workflow mechanisms |

**Note:** Change Request and Ticket are separate object classes. Views, widgets, and reports for "Tickets" do not include Change Requests.

### 6.3 Approval Model

| Aspect | Express | Enterprise |
|--------|---------|------------|
| Supported objects | A constrained set of workflow-governed objects | Broadly applicable to workflow-governed objects |
| Approval model | Simplified, single-stage approval | Multi-stage approval model |
| Voting methods | Constrained | Expanded |
| Customization approach | Configuration- and parameter-driven | Full workflow-driven customization |

Approval Request is a special object type (not a ticket).

### 6.4 Hidden Object Classes

These objects exist in the platform but are not surfaced in Express:

| Object | Domain | Reason |
|--------|--------|--------|
| Incident | Service Management | ITIL complexity deferred |
| Service Request | Service Management | ITIL complexity deferred |
| Problem | Service Management | Requires incident correlation maturity |
| Service | Service Management | Requires service catalog architecture |
| Service Catalog Item | Service Management | Requires service definition |
| SLA | Service Management | Requires service-level management |
| Project, Project Task | Project Management | Full domain deferred |
| Asset | Financial | Consolidated into CI objects |
| Configuration | Configuration Items | Advanced CI type |
| Network | Configuration Items | Advanced CI type |
| Document | Configuration Items | Advanced CI type |
| Stock Room | Inventory | Simplified to threshold rules |
| Approval Stage | Governance | Single-stage model |
| Department | Organizational | Advanced organizational structure |

---

## 7. System Constraints

### 7.1 Self Service Portal

| Constraint | Description |
|------------|-------------|
| Ticket creation | Only one Create Action supported |
| Approval Requests | Available (can be enabled/disabled) |
| Available Approval actions | Approve, Reject only |

### 7.2 Security and Roles

| Constraint | Description |
|------------|-------------|
| Administrator role | Built-in, cannot be edited |
| Access Scope | Not supported |
| Custom roles | Limited capability |

### 7.3 System Resources

| Resource | Express Constraint |
|----------|-------------------|
| Service Accounts | Single pre-configured account (ALLOY_SYSTEM) |
| Support Hours calendars | Single calendar (Help Desk Support Hours) |
| Dashboards | Single Dashboard (customizable by administrator) |

---

## 8. Analytical Perspective

### 8.1 Process Modeling in Express

Business Analysts working with Express should understand:

| Analysis Approach | Express Suitability |
|-------------------|---------------------|
| Task-based work tracking | Fully supported via Tickets |
| Change control with approval | Fully supported via Change Requests |
| Asset inventory | Fully supported via Computers and Inventory Items |
| Software compliance | Fully supported via License management |
| Procurement tracking | Fully supported via Purchase Orders |

| Analysis Approach | Express Consideration |
|-------------------|----------------------|
| ITIL Incident vs Request analysis | Not structurally supported; Tickets are task-focused |
| Problem trend analysis | No Problem object; requires external analysis |
| Service-level compliance | No SLA object; use priority-based due dates |
| Project resource planning | No Project object; track via Tickets or externally |
| Financial asset valuation | No separate Asset; basic cost in CI |

### 8.2 Customization Boundaries

When planning Express implementations:

| Requirement | Express Approach |
|-------------|------------------|
| "Different ticket types" | Use Categories within Ticket class |
| "Automatic assignment" | Configure Routing Business Policy |
| "Escalation on aging" | Configure Escalation Business Policy |
| "Custom creation logic" | Not possible; use Templates for defaults only |
| "Complex approval chains" | Simplify to single-stage or defer to Enterprise |
| "Custom automation" | Work within Business Rules or defer to Enterprise |

---

## 9. Upgrade Path to Full Platform

### 9.1 What Expands

| Concept | Express State | Enterprise Expansion |
|---------|---------------|---------------------|
| **Ticket** | Renamed Work Order | Work Order + Incident, Service Request, Problem become available |
| **Inventory Item** | Renamed Hardware | Hardware + separate Asset for financial tracking |
| **Business Rules** | Limited triggers | Full Triggers with Functions |
| **Templates** | Field initialization only | Create Actions with Programming |
| **Actions** | Enable/Disable only | Full creation and editing |
| **Approvals** | Single-stage | Multi-stage with Approval Stages object |
| **Service Management** | Not available | Service, SLA, Service Catalog |
| **Project Management** | Not available | Project, Project Task |

### 9.2 What Remains Unchanged

| Aspect | Continuity |
|--------|------------|
| Object Identity | Records preserve identity and referential relationships |
| Data Content | Field values migrate to corresponding fields |
| Relationships | Links between objects preserved |
| History | Audit trail and activity history maintained |
| Attachments | Files remain with their objects |
| Organizational data | Persons, Organizations, Locations intact |

### 9.3 Terminology Mapping on Upgrade

| Express Term | Enterprise Term | Notes |
|--------------|-----------------|-------|
| Ticket | Work Order | |
| Inventory Item | Hardware | |
| Business Rule | Trigger | |
| Business Policy | Trigger | Business Policies are Express-specific; replaced by full Triggers in Enterprise |
| Threshold Notification Rule | Stock Rule | |

---

## Appendix A: Express Formation Principles

Express is formed from Enterprise through:

1. **Aliasing** — renaming objects and UI sections for simplicity
2. **Hiding** — removing ITIL objects, CMDB advanced types, SLA, Projects from UI
3. **Simplified object creation** — Templates instead of Create Actions, no Programming
4. **Object classification** — 4 classes with different customization depths
5. **Workflow constraints** — Business Policies for Tickets, Change Requests (Approval Requests: Escalation only); Business Rules for Tickets, Change Requests only
6. **Action lockdown** — Enable/Disable only, no creation or editing
7. **Approval simplification** — single Approval Stage instead of multiple

---

## Appendix B: Conceptual Constraint Summary

| Constraint Area | Express Boundary |
|-----------------|------------------|
| Object Classes | Fixed set; no custom classes |
| Object Creation | Templates as workflow items (replace Create Actions); no Programming |
| Actions | Enable/Disable only; no creation/editing |
| Triggers (Business Rules) | For Tickets, Change Requests only; limited operations |
| Functions | Cannot create; can call pre-built |
| Business Policies | Express-specific; for Tickets, Change Requests (full), Approval Requests (Escalation only) |
| Approval Stages | Single stage; object hidden |
| Service Catalog | Not available |
| SLA | Not available |
| Forms | Cannot create |
| Validation Rules | Not available |

---

*Document Version: 2.4*
*Source: ANX Product Specification, Platform Documentation*
