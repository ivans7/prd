# Business Objects Reference

This document provides detailed definitions for all business objects managed by the platform. For conceptual overview and platform principles, see [Platform Conceptual Model](platform-conceptual-model.md).

---

## Object Description Format

Each object is described using a consistent structure:

| Aspect | Description |
|--------|-------------|
| **Definition** | What this object represents |
| **Role** | Business purpose within the platform |
| **Behavior** | How the object is managed (Workflow-governed / Static / System-managed) |
| **Key Relationships** | Primary connections to other object types |

**Behavior Types:**
- **Workflow-governed**: Object behavior (states, transitions, automation) is defined by user-configured workflows
- **Static**: Object serves as reference data with minimal state changes
- **System-managed**: Object is created/maintained by platform mechanisms

---

## 1. Service Management Objects

Objects that support the handling of service-related activities and requests.

### 1.1 Incident

| Aspect | Description |
|--------|-------------|
| **Definition** | A record representing an unplanned interruption or reduction in service quality |
| **Role** | Captures, tracks, and facilitates resolution of service disruptions |
| **Behavior** | Workflow-governed. State transitions, assignments, escalations, and notifications are defined by workflow configuration |
| **Key Relationships** | Person (requester, assignee), Group, Service, Configuration Item, Problem, Change Request, Knowledge Article, SLA |

### 1.2 Service Request

| Aspect | Description |
|--------|-------------|
| **Definition** | A formal request for something to be provided—access, information, or a standard change |
| **Role** | Processes user requests through fulfillment workflows |
| **Behavior** | Workflow-governed. Approval requirements, fulfillment steps, and notifications are workflow-defined |
| **Key Relationships** | Person (requester, fulfiller), Group, Service Catalog Item, Work Order, Approval, SLA |

### 1.3 Change Request (RFC)

| Aspect | Description |
|--------|-------------|
| **Definition** | A proposal to add, modify, or remove any component that could affect services |
| **Role** | Controls and coordinates changes to minimize risk and disruption |
| **Behavior** | Workflow-governed. Approval stages, implementation scheduling, and review processes are workflow-configured |
| **Key Relationships** | Person (requester, implementer), Approval Group, Configuration Item, Work Order, Incident, Problem, SLA |

### 1.4 Problem

| Aspect | Description |
|--------|-------------|
| **Definition** | A record representing the underlying cause of one or more incidents |
| **Role** | Supports root cause analysis and prevention of incident recurrence |
| **Behavior** | Workflow-governed. Investigation stages, resolution tracking, and known error documentation are workflow-defined |
| **Key Relationships** | Incident, Configuration Item, Change Request, Knowledge Article, Person (assignee) |

### 1.5 Work Order

| Aspect | Description |
|--------|-------------|
| **Definition** | A discrete unit of work, typically part of fulfilling a larger request or change |
| **Role** | Decomposes complex activities into assignable, trackable tasks |
| **Behavior** | Workflow-governed. Task progression, completion logic, and parent-object interaction are workflow-configured |
| **Key Relationships** | Service Request (parent), Change Request (parent), Incident (parent), Person (assignee), Group |

### 1.6 Approval

| Aspect | Description |
|--------|-------------|
| **Definition** | A decision point requiring authorization before proceeding |
| **Role** | Enforces governance and authorization controls on significant actions |
| **Behavior** | Workflow-governed. Approval stages, voting methods, and delegation rules are workflow-defined |
| **Key Relationships** | Change Request, Service Request, Person (approver), Approval Group |

### 1.7 SLA

| Aspect | Description |
|--------|-------------|
| **Definition** | A Service Level Agreement defining response and resolution time targets |
| **Role** | Establishes measurable service quality commitments and enables compliance tracking |
| **Behavior** | Static configuration. Defines targets that workflow automation references for escalation and prioritization |
| **Key Relationships** | Service, Incident, Service Request, Change Request, Business Policy (escalation) |

### 1.8 Service

| Aspect | Description |
|--------|-------------|
| **Definition** | An IT capability offered to users and the business |
| **Role** | Defines what IT provides and supports service-level tracking |
| **Behavior** | Workflow-governed. Service catalog presentation and SLA association are workflow-configured |
| **Key Relationships** | Service Catalog Item, SLA, Configuration Item, Incident, Service Request, Person (owner) |

### 1.9 Service Catalog Item

| Aspect | Description |
|--------|-------------|
| **Definition** | A requestable item or service offering presented to end users |
| **Role** | Provides a user-facing menu of available services and requests |
| **Behavior** | Static configuration. Defines request templates, approval requirements, and fulfillment workflows |
| **Key Relationships** | Service, Service Request, Template, Approval Group, Form |

### 1.10 Recurrent Ticket

| Aspect | Description |
|--------|-------------|
| **Definition** | A template for automated submission of tickets according to a schedule |
| **Role** | Enables scheduled creation of Incidents, Problems, Change Requests, or Work Orders |
| **Behavior** | System-managed. Requires Automation Server; executes on configured schedule |
| **Key Relationships** | Incident, Problem, Change Request, Work Order (created tickets) |

### 1.11 Announcement

| Aspect | Description |
|--------|-------------|
| **Definition** | A message, advisory, or notice distributed to platform users |
| **Role** | Communicates information to technicians and self-service portal users |
| **Behavior** | Workflow-governed. Publication and visibility rules are workflow-configured |
| **Key Relationships** | Person (audience), Organization |

---

## 2. Project Management Objects

Objects that support project planning and execution.

### 2.1 Project

| Aspect | Description |
|--------|-------------|
| **Definition** | A container for managing IT projects with tasks, timelines, and deliverables |
| **Role** | Organizes project work, tracks progress, and coordinates with service desk activities |
| **Behavior** | Workflow-governed. Project states, task management, and completion rules are workflow-defined |
| **Key Relationships** | Project Task, Incident, Problem, Change Request, Work Order (related tickets), Person (manager) |

### 2.2 Project Task

| Aspect | Description |
|--------|-------------|
| **Definition** | A unit of work within a project, supporting hierarchical decomposition |
| **Role** | Breaks down project work into assignable, schedulable activities |
| **Behavior** | Workflow-governed. Task states, dependencies, and completion are workflow-defined |
| **Key Relationships** | Project (parent), Project Task (parent/child hierarchy), Person (assignee) |

---

## 3. Configuration Items (CI)

Configuration Items represent managed components of IT infrastructure and serve as the **technical identity** of what is being managed. All CIs share common characteristics: they can be related to other CIs, linked to tickets, and tracked for changes. Financial tracking is represented separately through the **Asset** object, optionally linked to a CI.

### 3.1 Computer

| Aspect | Description |
|--------|-------------|
| **Definition** | An end-user computing device (desktop, laptop, workstation, server) |
| **Role** | Tracks a computing device as a configuration item (CI), including technical specifications, operational context, and software inventory |
| **Behavior** | Workflow-governed. Deployment states, retirement handling, and update handling are workflow-configured |
| **Key Relationships** | Person (assigned user), Organization, Location, Software Installation, Asset (optional financial overlay), Incident |

### 3.2 Hardware

| Aspect | Description |
|--------|-------------|
| **Definition** | Physical IT equipment other than computers (network devices, peripherals, storage, components) |
| **Role** | Tracks physical infrastructure as a configuration item (CI), including technical specifications and operational context |
| **Behavior** | Workflow-governed. Deployment, maintenance, and decommissioning states are workflow-defined |
| **Key Relationships** | Location, Organization, Asset (optional financial overlay), Contract, Incident, Component |

### 3.3 Network

| Aspect | Description |
|--------|-------------|
| **Definition** | A network segment or network entity (not a device) |
| **Role** | Stores information about computer networks and their IP address ranges |
| **Behavior** | Workflow-governed. Network status and configuration tracking are workflow-defined |
| **Key Relationships** | IP Address, Location, Computer, Hardware, Incident |

### 3.4 Document

| Aspect | Description |
|--------|-------------|
| **Definition** | A CI representing important documentation (policies, procedures, diagrams) |
| **Role** | Tracks managed documents as configuration items with version control |
| **Behavior** | Workflow-governed. Document states and review cycles are workflow-defined |
| **Key Relationships** | Any CI (related), Service, Change Request |

### 3.5 Configuration

| Aspect | Description |
|--------|-------------|
| **Definition** | A description of a specific configuration or assembly (e.g., "Internet Access Configuration", "Email Configuration") |
| **Role** | Documents standard configurations that can be applied to or referenced by other CIs |
| **Behavior** | Workflow-governed. Configuration status and update tracking are workflow-defined |
| **Key Relationships** | Computer, Hardware, Network, Service (Associated CI), Document |

**Note:** Configuration is distinct from Configuration Item (CI). CI is the ITIL term for any managed component; Configuration is a specific object type describing a configuration/assembly.

**Note:** Tracked Software is also a CI type but is covered in [Section 6.3 Software Management Objects](#63-tracked-software) as it primarily serves software licensing compliance.

---

## 4. Asset and Financial Objects

Objects for financial tracking, ownership, and procurement. Note: Asset is a financial wrapper, not a base class for CIs.

### 4.1 Asset

| Aspect | Description |
|--------|-------------|
| **Definition** | A financial record tracking ownership, cost, and depreciation of an IT resource |
| **Role** | Stores financial data about a CI while the CI record tracks technical information |
| **Behavior** | Workflow-governed. Asset lifecycle states and financial tracking are workflow-defined |
| **Key Relationships** | Computer, Hardware, Software License, Consumable (Associated CI), Organization (owner), Contract |

**Important:** Asset is NOT a base class. It is an optional financial overlay that can be associated with a CI (Computer, Hardware, Software License, or Consumable) to track financial aspects separately from technical configuration.

**Conceptual note:** For many organizations, a single physical item is represented by a CI record (technical identity) and, optionally, a linked Asset record (financial identity). Not every CI must have an Asset, and financial governance can be applied independently of technical lifecycle management.

### 4.2 Contract

| Aspect | Description |
|--------|-------------|
| **Definition** | A formal agreement with an external party for products, services, or support |
| **Role** | Tracks vendor agreements, terms, renewal dates, and covered items (CIs and related Asset records) |
| **Behavior** | Workflow-governed. Expiration tracking and renewal management are workflow-configured |
| **Key Relationships** | Vendor, Hardware, Software License, Asset, Document, Person (owner) |

### 4.3 Purchase Order

| Aspect | Description |
|--------|-------------|
| **Definition** | A formal request to acquire goods or services from a vendor |
| **Role** | Tracks procurement from request through receipt and payment |
| **Behavior** | Workflow-governed. Approval, fulfillment, and receipt confirmation are workflow-defined |
| **Key Relationships** | PO Item, Vendor, Product, Contract, Approval |

### 4.4 PO Item

| Aspect | Description |
|--------|-------------|
| **Definition** | A line item within a Purchase Order specifying quantity and product |
| **Role** | Details individual products or services being ordered |
| **Behavior** | System-managed. Created as part of Purchase Order |
| **Key Relationships** | Purchase Order (parent), Product, Asset (received) |

### 4.5 Vendor

| Aspect | Description |
|--------|-------------|
| **Definition** | An external organization that supplies products or services |
| **Role** | Maintains supplier information for procurement and contract management |
| **Behavior** | Static reference data. Linked to contracts, purchases, and products |
| **Key Relationships** | Contract, Purchase Order, Product, Software License, Person (contact) |

---

## 5. Inventory Management Objects

Objects for managing physical inventory, stock, and equipment lending.

### 5.1 Stock Room

| Aspect | Description |
|--------|-------------|
| **Definition** | A physical or logical location where Assets and Consumables are stored |
| **Role** | Maintains inventory levels for a specific Organization/Location with stock management |
| **Behavior** | Static configuration with workflow-governed stock movements |
| **Key Relationships** | Location, Organization, Asset, Consumable, Stock Rule, Person (stock manager) |

### 5.2 Stock Rule

| Aspect | Description |
|--------|-------------|
| **Definition** | A threshold-based rule for automatic inventory replenishment |
| **Role** | Triggers transfer from another Stock Room or order from Vendor when inventory reaches threshold |
| **Behavior** | System-managed automation. Executes when thresholds are reached |
| **Key Relationships** | Stock Room, Asset, Consumable, Vendor, Purchase Order |

### 5.3 Consumable

| Aspect | Description |
|--------|-------------|
| **Definition** | Expendable supplies tracked by quantity rather than individual identity (toner, cables, batteries) |
| **Role** | Manages inventory levels of non-serialized supplies |
| **Behavior** | Workflow-governed. Reorder thresholds, stock adjustments, and allocation are workflow-configured |
| **Key Relationships** | Stock Room, Location, Vendor, Purchase Order, Product, Asset (optional financial overlay) |

### 5.4 Product

| Aspect | Description |
|--------|-------------|
| **Definition** | A product or service available for purchase, defined in the Product Catalog |
| **Role** | Provides a reference catalog for creating Purchase Orders and managing procurement |
| **Behavior** | Static reference data. Defines products (physical items) and services (maintenance, warranty) |
| **Key Relationships** | Vendor, Purchase Order, PO Item, Consumable |

### 5.5 Equipment (Library Item)

| Aspect | Description |
|--------|-------------|
| **Definition** | An item in the Equipment Lending Library available for temporary user checkout |
| **Role** | Enables check-out/check-in of loanable equipment (monitors, keyboards, laptops, manuals) |
| **Behavior** | Workflow-governed. Checkout, return, and overdue handling are workflow-configured |
| **Key Relationships** | Person (borrower), Equipment Reservation, Location |

**Note:** Equipment is distinct from Hardware. Hardware is a CI for inventory tracking; Equipment is for lending library items that users can borrow temporarily.

### 5.6 Equipment Reservation

| Aspect | Description |
|--------|-------------|
| **Definition** | A booking record for equipment checkout |
| **Role** | Schedules and tracks equipment loans with check-out/check-in dates |
| **Behavior** | Workflow-governed. Reservation approval and reminder notifications are workflow-configured |
| **Key Relationships** | Equipment (Library Item), Person (borrower) |

---

## 6. Software Management Objects

Objects for tracking software products, licenses, and installations.

### 6.1 Software Catalog Item (Software Product)

| Aspect | Description |
|--------|-------------|
| **Definition** | A recognized software application or suite in the software catalog |
| **Role** | Provides master product data imported from Discovery; foundation for license compliance |
| **Behavior** | System-managed (read-only from Discovery import). Reference data for compliance tracking |
| **Key Relationships** | Tracked Software, Software Installation, Vendor |

**Note:** Software Catalog Items are typically imported from Alloy Discovery and are read-only. They represent discovered software products.

### 6.2 Software License

| Aspect | Description |
|--------|-------------|
| **Definition** | A legal entitlement to use a specific software product under defined terms |
| **Role** | Tracks software entitlements and enables compliance comparison against installations |
| **Behavior** | Workflow-governed. Allocation, expiration handling, and renewal processes are workflow-configured |
| **Key Relationships** | Tracked Software, Software Installation, Computer, Person, Organization, Contract, Vendor, Asset (optional financial overlay) |

### 6.3 Tracked Software

| Aspect | Description |
|--------|-------------|
| **Definition** | A link between Software Products and Software Licenses for compliance tracking |
| **Role** | Bridges discovered software to license entitlements; enables compliance calculations |
| **Behavior** | Workflow-governed. Compliance status and tracking rules are workflow-defined |
| **Key Relationships** | Software Catalog Item, Software License, Software Installation |

**Hierarchy:** A single Tracked Software record can join multiple Software Products (e.g., different versions) and include several Software Licenses.

**Note:** Tracked Software is also classified as a Configuration Item (CI) type.

### 6.4 Software Installation

| Aspect | Description |
|--------|-------------|
| **Definition** | A record of software installed on a specific Computer or Hardware |
| **Role** | Links Computer/Hardware to Software Product and Software License for compliance |
| **Behavior** | System-managed. Created from discovery data or manual allocation |
| **Key Relationships** | Computer, Hardware, Software Catalog Item, Software License, Tracked Software |

**Fields:** Includes Allocation Date and "Deallocate automatically" option.

---

## 7. Organizational Objects

Objects that represent people, groups, and organizational structures.

### 7.1 Person

| Aspect | Description |
|--------|-------------|
| **Definition** | An individual who interacts with the platform as a user, technician, or contact |
| **Role** | Identifies individuals for assignment, notification, and accountability |
| **Behavior** | Workflow-governed. Person types and synchronization handling are workflow-configured |
| **Key Relationships** | Organization, Group, Location, Security Role, Computer (assigned), Incident (requester/assignee) |

### 7.2 Organization

| Aspect | Description |
|--------|-------------|
| **Definition** | A business entity (company, division, external customer) |
| **Role** | Groups persons and assets by organizational affiliation; supports multi-tenancy |
| **Behavior** | Static reference data. Hierarchy and relationship rules may be workflow-extended |
| **Key Relationships** | Person, Computer, Hardware, Asset, Contract, Vendor, Location, Company Address |

**Note:** Organization records support hierarchical structure (parent-child relationships) to represent companies, divisions, departments, and teams within a single object class.

### 7.3 Group

| Aspect | Description |
|--------|-------------|
| **Definition** | A collection of persons with a shared function or responsibility |
| **Role** | Enables collective assignment, notification, and authorization |
| **Behavior** | Static configuration. Membership rules and assignment logic may be automated |
| **Key Relationships** | Person (members), Incident (assignee), Service Request (assignee), Approval Group |

### 7.4 Location

| Aspect | Description |
|--------|-------------|
| **Definition** | A physical place where assets or persons are situated |
| **Role** | Tracks geographical distribution of IT resources |
| **Behavior** | Static reference data. Hierarchy (country/city/building/room) is user-defined |
| **Key Relationships** | Computer, Hardware, Person, Organization, Stock Room, Equipment |

### 7.5 Company Address

| Aspect | Description |
|--------|-------------|
| **Definition** | A mailing or physical address associated with an Organization |
| **Role** | Stores address details for organizations (billing, shipping, headquarters) |
| **Behavior** | Static reference data |
| **Key Relationships** | Organization |

### 7.6 Manufacturer

| Aspect | Description |
|--------|-------------|
| **Definition** | A manufacturer or brand name for hardware and software products |
| **Role** | Provides reference data for categorizing assets by manufacturer |
| **Behavior** | Static reference data. Automatically created from discovery audit snapshots |
| **Key Relationships** | Hardware, Computer, Product, Software Catalog Item |

**Note:** The UI displays "Manufacturers" while the database table is named "Brands". These refer to the same object.

---

## 8. Platform Configuration Objects

Objects that define platform behavior and structure. These are meta-objects that configure how other objects behave.

### 8.1 Workflow

| Aspect | Description |
|--------|-------------|
| **Definition** | The complete configuration governing a business object class's behavior |
| **Role** | Defines available actions, triggers, states, and automation for an object class |
| **Behavior** | System-managed configuration. Changes affect object behavior platform-wide |
| **Key Relationships** | Object Class, Action, Trigger, Business Policy, Function, Template |

### 8.2 Object Class

| Aspect | Description |
|--------|-------------|
| **Definition** | A definition of a business object type with its fields, forms, and workflow |
| **Role** | Establishes the schema and behavior framework for a category of objects |
| **Behavior** | System-managed configuration. Extensible through custom classes |
| **Key Relationships** | Workflow, Field, Form, Template, Security Role (permissions) |

### 8.3 Form

| Aspect | Description |
|--------|-------------|
| **Definition** | A layout definition specifying how fields are presented for input or display |
| **Role** | Controls user interaction with object data |
| **Behavior** | System-managed configuration. Multiple forms per object class for different contexts |
| **Key Relationships** | Object Class, Field, Action, Template |

### 8.4 Field

| Aspect | Description |
|--------|-------------|
| **Definition** | An attribute definition for storing data on a business object |
| **Role** | Defines data structure including type, validation, and default values |
| **Behavior** | System-managed configuration. Includes both system fields and custom fields |
| **Key Relationships** | Object Class, Form, Trigger (conditions), Business Policy (conditions) |

### 8.5 Action

| Aspect | Description |
|--------|-------------|
| **Definition** | A defined operation that users or services can perform on business objects |
| **Role** | Provides the mechanism for state changes, updates, and process execution |
| **Behavior** | System-managed configuration. Can invoke functions, send notifications, update fields |
| **Key Relationships** | Workflow, Object Class, Form (input), Function, Notification Rule |

### 8.6 Trigger

| Aspect | Description |
|--------|-------------|
| **Definition** | An automation rule that executes operations based on events or conditions |
| **Role** | Enables automatic responses to object changes, schedules, or condition matches |
| **Behavior** | System-managed configuration. Executes in background via Automation Server |
| **Key Relationships** | Workflow, Object Class, Function, Notification Rule, Field (conditions) |

### 8.7 Business Policy

| Aspect | Description |
|--------|-------------|
| **Definition** | Specialized automation rules for ticket-class objects |
| **Role** | Provides routing, prioritization, escalation, and general automation for tickets |
| **Behavior** | System-managed configuration. Evaluated by policy engine based on object state |
| **Key Relationships** | Workflow, Incident, Service Request, Change Request, SLA, Group |

### 8.8 Function

| Aspect | Description |
|--------|-------------|
| **Definition** | A reusable block of operations callable from actions, triggers, or other functions |
| **Role** | Enables modular, maintainable automation logic |
| **Behavior** | System-managed configuration. Invoked by workflow components |
| **Key Relationships** | Action, Trigger, Business Policy, Workflow |

### 8.9 Template

| Aspect | Description |
|--------|-------------|
| **Definition** | A configuration defining initial values and input collection for new objects |
| **Role** | Standardizes object creation with predefined defaults and required inputs |
| **Behavior** | System-managed configuration. Linked to object creation actions |
| **Key Relationships** | Object Class, Form, Action, Service Catalog Item, Workflow |

### 8.10 Notification Rule

| Aspect | Description |
|--------|-------------|
| **Definition** | A configuration defining when and how notifications are sent |
| **Role** | Controls email, in-app, and other notifications based on object events |
| **Behavior** | System-managed configuration. Executed by actions and triggers |
| **Key Relationships** | Action, Trigger, Person, Group, Object Class |

### 8.11 Security Role

| Aspect | Description |
|--------|-------------|
| **Definition** | A named set of permissions defining what users can access and do |
| **Role** | Implements role-based access control across platform objects and operations |
| **Behavior** | System-managed configuration. Assigned to persons; controls UI and API access |
| **Key Relationships** | Person, Object Class, Action, Field (visibility) |

### 8.12 Approval Group

| Aspect | Description |
|--------|-------------|
| **Definition** | A defined set of persons authorized to approve specific types of requests |
| **Role** | Establishes approval authority for governance workflows |
| **Behavior** | Static configuration. Referenced by approval workflow stages |
| **Key Relationships** | Person (members), Change Request, Service Request, Approval, Service Catalog Item |

---

## 9. Support Objects

Objects that provide operational aids, documentation, and system records.

### 9.1 Knowledge Base Article

| Aspect | Description |
|--------|-------------|
| **Definition** | A documented solution, procedure, or reference information |
| **Role** | Captures and shares knowledge for self-service and technician support |
| **Behavior** | Workflow-governed. Publication states and categorization are workflow-defined |
| **Key Relationships** | Incident, Problem, Service, Person (author), Attachment |

### 9.2 Attachment

| Aspect | Description |
|--------|-------------|
| **Definition** | A file directly attached to a specific business object instance |
| **Role** | Stores supporting files within the context of their parent object |
| **Behavior** | System-managed. Created when files are added to objects |
| **Key Relationships** | Any business object (parent), Person (uploader) |

### 9.3 Note

| Aspect | Description |
|--------|-------------|
| **Definition** | A text comment or annotation added to a business object |
| **Role** | Captures communications, observations, and updates within object context |
| **Behavior** | System-managed. Appended to object history |
| **Key Relationships** | Any business object (parent), Person (author) |

### 9.4 Email

| Aspect | Description |
|--------|-------------|
| **Definition** | An email message sent or received in relation to a business object |
| **Role** | Maintains email correspondence history within object context |
| **Behavior** | System-managed. Created by email integration or notification system |
| **Key Relationships** | Incident, Service Request, Person (sender/recipient) |

### 9.5 Audit Trail Entry

| Aspect | Description |
|--------|-------------|
| **Definition** | A system record of a change made to a business object |
| **Role** | Provides complete change history for compliance and troubleshooting |
| **Behavior** | System-managed. Automatically created on object modifications |
| **Key Relationships** | Any business object (subject), Person (actor), Field (changed) |

### 9.6 Report

| Aspect | Description |
|--------|-------------|
| **Definition** | A saved report definition with query criteria and output format |
| **Role** | Provides reusable data analysis and presentation |
| **Behavior** | System-managed configuration. Executed on demand or schedule |
| **Key Relationships** | Object Class (data source), Security Role (access), Person (owner) |

### 9.7 Dashboard

| Aspect | Description |
|--------|-------------|
| **Definition** | A visual display combining multiple data views and metrics |
| **Role** | Provides at-a-glance operational visibility |
| **Behavior** | System-managed configuration. Composed of widgets and report elements |
| **Key Relationships** | Report, Security Role (access), Person (owner) |

### 9.8 Saved Search

| Aspect | Description |
|--------|-------------|
| **Definition** | A stored query with filter criteria for finding objects |
| **Role** | Enables quick access to commonly needed object sets |
| **Behavior** | System-managed configuration. Can be personal or shared |
| **Key Relationships** | Object Class (target), Security Role (sharing), Person (owner) |

### 9.9 Calendar Entry

| Aspect | Description |
|--------|-------------|
| **Definition** | A scheduled event or time-based record |
| **Role** | Tracks scheduled activities, maintenance windows, and deadlines |
| **Behavior** | Workflow-governed. May trigger notifications or actions at scheduled times |
| **Key Relationships** | Change Request (implementation window), Person, Group |

---

## Appendix A: Object Category Summary

| Category | Count | Primary Purpose |
|----------|-------|-----------------|
| Service Management | 11 | Handle service activities, requests, and communication |
| Project Management | 2 | Manage IT projects and tasks |
| Configuration Items | 5 | Track managed IT infrastructure components |
| Asset & Financial | 5 | Financial tracking and procurement |
| Inventory Management | 6 | Stock, consumables, and equipment lending |
| Software Management | 4 | Software products, licenses, and compliance |
| Organizational | 7 | People, groups, and structures |
| Platform Configuration | 12 | Define platform behavior |
| Support | 9 | Operational aids and system records |
| **Total** | **61** | |

---

## Appendix B: Key Conceptual Distinctions

### Asset vs Configuration Item

| Aspect | Asset | Configuration Item (CI) |
|--------|-------|-------------------------|
| **Purpose** | Financial tracking | Technical configuration |
| **Data** | Cost, depreciation, ownership | Specifications, relationships, status |
| **Relationship** | Optional overlay on CI | Core managed component |

Asset is a **separate object**, not a base class. It stores financial data ABOUT a CI, while the CI record tracks technical information. They are linked via the "Associated CI" field but managed independently.

### Equipment vs Hardware

| Aspect | Equipment (Library Item) | Hardware |
|--------|--------------------------|----------|
| **Purpose** | Lending/borrowing | Inventory tracking |
| **Key Feature** | Check-out/Check-in | Technical specifications |
| **Use Case** | Temporary loans to users | Permanent infrastructure |

### Configuration vs Configuration Item

| Aspect | Configuration | Configuration Item |
|--------|---------------|-------------------|
| **Definition** | A described configuration/assembly | Any managed IT component |
| **Example** | "Standard Email Setup" | A specific server |
| **Role** | Documents how things are configured | Tracks what exists |

### Software Object Hierarchy

```
Software Catalog Item (Software Product)
         │
         │ imported from Discovery (read-only)
         │
         ▼
   Tracked Software
         │
         │ links products to licenses
         │
         ▼
  Software License ◄────► Software Installation
         │                       │
         │ entitlement           │ actual install on device
         │                       │
         └───────────────────────┘
                    ▼
              Computer/Hardware
```

---

*Document Version: 2.2*
*Reference Scope: Platform Business Objects*
*Total Objects: 61*
