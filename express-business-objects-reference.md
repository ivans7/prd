# Express Edition Business Objects Reference

This document provides detailed definitions for all business objects available in Express Edition. For the complete platform object reference, see [Business Objects Reference](business-objects-reference.md). For conceptual differences between Express and Enterprise, see [Express Edition Conceptual Model](express-edition-conceptual-model.md).

---

## Object Description Format

| Aspect | Description |
|--------|-------------|
| **Definition** | What this object represents |
| **Role** | Business purpose within Express |
| **Customization Class** | Level of customization available (1-4) |
| **Behavior** | Pre-configured workflow / Static / System-managed |
| **Key Relationships** | Primary connections to other objects |
| **Enterprise Equivalent** | Corresponding object in Enterprise Edition |

### Customization Classes

| Class | Available Customization |
|-------|------------------------|
| **Class 1** | No customization |
| **Class 2** | Lookups only |
| **Class 3** | Lookups + Notifications + Templates |
| **Class 4** | Full: Lookups + Notifications + Templates + Business Policies + Business Rules |

---

## 1. Service Management Objects

### 1.1 Ticket

| Aspect | Description |
|--------|-------------|
| **Definition** | A task-based work record for tracking activities and assignments |
| **Role** | Primary object for tracking work to be done; task-focused rather than ITIL-categorized |
| **Customization Class** | **Class 4** — Full Express customization |
| **Behavior** | Pre-configured workflow. Business Policies (Prioritization, Routing, Escalation) and Business Rules available |
| **Key Relationships** | Requester (Person), Assignee (Person), Related Computers, Related Inventory Items, Parent Change Request |
| **Enterprise Equivalent** | Renamed from **Work Order**. ITIL objects (Incident, Service Request, Problem) are separate hidden classes, not consolidated into Ticket |

**Important:** Ticket is a renamed Work Order, not a consolidation of Incident and Service Request. In Enterprise, Work Order, Incident, Service Request, and Problem are all separate object classes.

### 1.2 Change Request

| Aspect | Description |
|--------|-------------|
| **Definition** | A proposal to add, modify, or remove any component that could affect IT environment |
| **Role** | Controls and coordinates changes with approval governance |
| **Customization Class** | **Class 4** — Full Express customization |
| **Behavior** | Pre-configured workflow with lifecycle stages and approval; Business Policies and Business Rules available |
| **Key Relationships** | Requester (Person), Approvers, Related Assets (shown instead of Related CI), Child Tickets (shown instead of Work Orders) |
| **Enterprise Equivalent** | Same object class. Enterprise adds: Service, Urgency, Impact attributes; Related CI; Work Orders tab; Escalation in Workflow Configuration |

**Express limitations:**
- No Service attribute
- No Urgency attribute
- No Impact attribute
- Related CI shown as "Related Asset"
- Work Orders tab shown as "Child Tickets"

### 1.3 Approval Request

| Aspect | Description |
|--------|-------------|
| **Definition** | A decision point requiring authorization before proceeding |
| **Role** | Enforces governance controls on Change Requests, Purchase Orders, and KB Articles |
| **Customization Class** | **Class 4** — Escalation Policy only |
| **Behavior** | Single-stage approval with two voting methods: All Approved or Majority Decision |
| **Key Relationships** | Parent object (Change Request, Purchase Order, KB Article), Approvers |
| **Enterprise Equivalent** | Multi-stage **Approval** with visible **Approval Stages** object and extended voting methods |

**Express approval parameters:**
- Approvers by Default (group or individual)
- Default Approver Group
- Default Approval Type (All Approved / Majority Decision)
- Approval Deadline (days)

### 1.4 Knowledge Base Article

| Aspect | Description |
|--------|-------------|
| **Definition** | A documented solution, procedure, or reference information |
| **Role** | Captures and shares knowledge for self-service and technician support |
| **Customization Class** | **Class 3** — Lookups + Notifications + Templates |
| **Behavior** | Pre-configured publication workflow with approval |
| **Key Relationships** | Related Tickets, Author (Person) |
| **Enterprise Equivalent** | Same object class with expanded workflow customization |

### 1.5 Announcement

| Aspect | Description |
|--------|-------------|
| **Definition** | A message, advisory, or notice distributed to platform users |
| **Role** | Communicates information to technicians and self-service portal users |
| **Customization Class** | **Class 3** — Lookups + Notifications + Templates |
| **Behavior** | Pre-configured publication workflow |
| **Key Relationships** | Target audience |
| **Enterprise Equivalent** | Same object class |

---

## 2. Asset Management Objects

### 2.1 Computer

| Aspect | Description |
|--------|-------------|
| **Definition** | An end-user computing device (desktop, laptop, workstation, server) |
| **Role** | Tracks computing assets with technical specifications, assignment, and software inventory |
| **Customization Class** | **Class 3** — Lookups + Notifications + Templates |
| **Behavior** | Pre-configured workflow with standard deployment states |
| **Key Relationships** | Assigned Person, Organization, Location, Software Installations |
| **Enterprise Equivalent** | Same object class; gains optional linkage to separate **Asset** for financial tracking |

### 2.2 Inventory Item

| Aspect | Description |
|--------|-------------|
| **Definition** | Generic tracked equipment (servers, peripherals, network devices, components) |
| **Role** | Tracks physical IT equipment with technical specifications |
| **Customization Class** | **Class 3** — Lookups + Notifications + Templates |
| **Behavior** | Pre-configured workflow with standard deployment states |
| **Key Relationships** | Location, Organization |
| **Enterprise Equivalent** | Renamed to **Hardware**; gains optional linkage to separate **Asset** for financial tracking |

### 2.3 Software License

| Aspect | Description |
|--------|-------------|
| **Definition** | A legal entitlement to use a specific software product under defined terms |
| **Role** | Tracks software entitlements and enables compliance comparison against installations |
| **Customization Class** | **Class 3** — Lookups + Notifications + Templates |
| **Behavior** | Pre-configured workflow for allocation and expiration tracking |
| **Key Relationships** | Tracked Software, Computers, Vendor, Contract |
| **Enterprise Equivalent** | Same object class with expanded workflow customization |

### 2.4 Tracked Software

| Aspect | Description |
|--------|-------------|
| **Definition** | A link between discovered software products and license entitlements |
| **Role** | Bridges discovered software to licenses for compliance calculations |
| **Customization Class** | **Class 1** — No customization |
| **Behavior** | Pre-configured compliance status tracking |
| **Key Relationships** | Software Catalog Items, Software Licenses |
| **Enterprise Equivalent** | Same object class |

### 2.5 Software Catalog Item

| Aspect | Description |
|--------|-------------|
| **Definition** | A recognized software application in the software catalog |
| **Role** | Provides master product data imported from Discovery |
| **Customization Class** | **Class 1** — No customization |
| **Behavior** | System-managed (read-only from Discovery import) |
| **Key Relationships** | Tracked Software, Vendor |
| **Enterprise Equivalent** | Same object class |

### 2.6 Consumable

| Aspect | Description |
|--------|-------------|
| **Definition** | Expendable supplies tracked by quantity (toner, cables, batteries) |
| **Role** | Manages inventory levels of non-serialized supplies |
| **Customization Class** | **Class 3** — Lookups + Notifications + Templates |
| **Behavior** | Pre-configured quantity tracking with threshold notifications |
| **Key Relationships** | Location, Vendor, Threshold Notification Rules |
| **Enterprise Equivalent** | Same object class; integrates with **Stock Room** for advanced inventory management |

### 2.7 Threshold Notification Rule

| Aspect | Description |
|--------|-------------|
| **Definition** | A rule that triggers notification when inventory reaches a threshold |
| **Role** | Alerts when consumable or asset quantities fall below defined levels |
| **Customization Class** | **Class 3** — Lookups + Notifications + Templates |
| **Behavior** | System-managed automation |
| **Key Relationships** | Consumables |
| **Enterprise Equivalent** | Replaced by **Stock Rule** with expanded capabilities (auto-transfer from other Stock Rooms, auto-order from Vendor) |

### 2.8 Discovered Installation

| Aspect | Description |
|--------|-------------|
| **Definition** | A record of software discovered on a Computer |
| **Role** | Provides raw discovery data for software compliance |
| **Customization Class** | **Class 1** — No customization |
| **Behavior** | System-managed from Discovery |
| **Key Relationships** | Computer, Software Catalog Item |
| **Enterprise Equivalent** | Same object class |

### 2.9 Manufacturer

| Aspect | Description |
|--------|-------------|
| **Definition** | A hardware or software manufacturer/brand |
| **Role** | Reference data for categorizing products |
| **Customization Class** | **Class 1** — No customization |
| **Behavior** | Static reference data |
| **Key Relationships** | Computers, Inventory Items, Software Catalog Items |
| **Enterprise Equivalent** | Same object class |

---

## 3. Equipment Lending Objects

### 3.1 Library Item

| Aspect | Description |
|--------|-------------|
| **Definition** | An item in the Equipment Lending Library available for temporary user checkout |
| **Role** | Enables check-out/check-in of loanable equipment |
| **Customization Class** | **Class 3** — Lookups + Notifications + Templates |
| **Behavior** | Pre-configured lending workflow |
| **Key Relationships** | Reservations, Location |
| **Enterprise Equivalent** | Same object class (called **Equipment**) |

### 3.2 Reservation

| Aspect | Description |
|--------|-------------|
| **Definition** | A booking record for equipment checkout |
| **Role** | Schedules and tracks equipment loans with check-out/check-in dates |
| **Customization Class** | **Class 3** — Lookups + Notifications + Templates |
| **Behavior** | Pre-configured reservation workflow |
| **Key Relationships** | Library Item, Borrower (Person) |
| **Enterprise Equivalent** | Same object class (**Equipment Reservation**) |

---

## 4. Procurement Objects

### 4.1 Purchase Order

| Aspect | Description |
|--------|-------------|
| **Definition** | A formal request to acquire goods or services from a vendor |
| **Role** | Tracks procurement from request through receipt |
| **Customization Class** | **Class 3** — Lookups + Notifications + Templates |
| **Behavior** | Pre-configured workflow with approval |
| **Key Relationships** | Vendor, PO Items, Approvers |
| **Enterprise Equivalent** | Same object class with expanded workflow customization |

### 4.2 PO Item

| Aspect | Description |
|--------|-------------|
| **Definition** | A line item within a Purchase Order specifying quantity and product |
| **Role** | Details individual products or services being ordered |
| **Customization Class** | **Class 1** — No customization |
| **Behavior** | System-managed as part of Purchase Order |
| **Key Relationships** | Purchase Order, Product |
| **Enterprise Equivalent** | Same object class |

### 4.3 Vendor

| Aspect | Description |
|--------|-------------|
| **Definition** | An external organization that supplies products or services |
| **Role** | Maintains supplier information for procurement |
| **Customization Class** | **Class 2** — Lookups only |
| **Behavior** | Static reference data |
| **Key Relationships** | Purchase Orders, Products, Contracts, Software Licenses |
| **Enterprise Equivalent** | Same object class |

### 4.4 Product

| Aspect | Description |
|--------|-------------|
| **Definition** | A product or service available for purchase |
| **Role** | Provides reference catalog for procurement |
| **Customization Class** | **Class 3** — Lookups + Notifications + Templates |
| **Behavior** | Static reference data |
| **Key Relationships** | Vendor, Purchase Orders |
| **Enterprise Equivalent** | Same object class (**Product Catalog Item**) |

### 4.5 Contract

| Aspect | Description |
|--------|-------------|
| **Definition** | A formal agreement with an external party for products, services, or support |
| **Role** | Tracks vendor agreements and expiration |
| **Customization Class** | **Class 3** — Lookups + Notifications + Templates |
| **Behavior** | Pre-configured expiration tracking |
| **Key Relationships** | Vendor, Covered Items |
| **Enterprise Equivalent** | Same object class with expanded workflow customization |

---

## 5. Organizational Objects

### 5.1 Person

| Aspect | Description |
|--------|-------------|
| **Definition** | An individual who interacts with the platform as a user, technician, or contact |
| **Role** | Identifies individuals for assignment, notification, and accountability |
| **Customization Class** | **Class 3** — Lookups + Notifications + Templates |
| **Behavior** | Pre-configured with Active/Inactive status |
| **Key Relationships** | Organization, Location, Assigned Assets, Submitted Tickets |
| **Enterprise Equivalent** | Same object class; gains **Group** membership for advanced assignment |

### 5.2 Organization

| Aspect | Description |
|--------|-------------|
| **Definition** | A business entity (company, division, external customer) |
| **Role** | Groups persons and assets by organizational affiliation |
| **Customization Class** | **Class 2** — Lookups only |
| **Behavior** | Static hierarchy |
| **Key Relationships** | Persons, Assets, Contracts |
| **Enterprise Equivalent** | Same object class; gains **Department** for finer structure |

### 5.3 Location

| Aspect | Description |
|--------|-------------|
| **Definition** | A physical place where assets or persons are situated |
| **Role** | Tracks geographical distribution of IT resources |
| **Customization Class** | **Class 2** — Lookups only |
| **Behavior** | Static hierarchy |
| **Key Relationships** | Computers, Inventory Items, Persons |
| **Enterprise Equivalent** | Same object class |

---

## Appendix A: Express Object Summary

| Category | Count | Objects |
|----------|-------|---------|
| Service Management | 5 | Ticket, Change Request, Approval Request, KB Article, Announcement |
| Asset Management | 9 | Computer, Inventory Item, Software License, Tracked Software, Software Catalog Item, Consumable, Threshold Notification Rule, Discovered Installation, Manufacturer |
| Equipment Lending | 2 | Library Item, Reservation |
| Procurement | 5 | Purchase Order, PO Item, Vendor, Product, Contract |
| Organizational | 3 | Person, Organization, Location |
| **Total** | **24** | |

---

## Appendix B: Objects by Customization Class

| Class | Level | Objects |
|-------|-------|---------|
| **Class 1** | No customization | Discovered Installation, Manufacturer, Tracked Software, Software Catalog Item, PO Item |
| **Class 2** | Lookups only | Organization, Location, Vendor |
| **Class 3** | Lookups + Notifications + Templates | Computer, Inventory Item, Purchase Order, Contract, Software License, KB Article, Person, Library Item, Consumable, Threshold Notification Rule, Reservation, Product, Announcement |
| **Class 4** | Full Express customization | Ticket, Change Request, Approval Request |

---

## Appendix C: Terminology Mapping

| Express Term | Enterprise Term | Notes |
|--------------|-----------------|-------|
| Ticket | Work Order | Same class, renamed. NOT Incident + Service Request |
| Inventory Item | Hardware | Same class, renamed |
| Business Rule | Trigger | Same class, renamed |
| Threshold Notification Rule | Stock Rule | Simplified capability |
| Library Item | Equipment | Same class, renamed |
| Product | Product Catalog Item | Same class, renamed |
| Customization | Workflow and Business Logic | UI section renamed |

---

## Appendix D: Objects Not Available in Express

These objects exist in the platform but are hidden in Express:

| Object | Domain | Status in Express |
|--------|--------|-------------------|
| Incident | Service Management | Hidden (not merged into Ticket) |
| Service Request | Service Management | Hidden (not merged into Ticket) |
| Problem | Service Management | Hidden |
| Work Order | Service Management | Renamed to "Ticket" |
| Service | Service Management | Hidden |
| Service Catalog Item | Service Management | Hidden |
| SLA | Service Management | Hidden |
| Recurrent Ticket | Automation | Hidden |
| Asset | Financial | Hidden (consolidated into CI) |
| Hardware | Asset Management | Renamed to "Inventory Item" |
| Configuration | Configuration Items | Hidden |
| Network | Configuration Items | Hidden |
| Document | Configuration Items | Hidden |
| Stock Room | Inventory | Hidden (simplified to Threshold Rules) |
| Stock Rule | Inventory | Simplified as "Threshold Notification Rule" |
| Project | Project Management | Hidden |
| Project Task | Project Management | Hidden |
| Department | Organizational | Hidden |
| Approval Stage | Governance | Hidden (single-stage model) |
| Approval Group | Governance | Hidden |

---

*Document Version: 2.1*
*Source: ANX Product Specification*
*Total Objects: 24*
