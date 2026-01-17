# Platform Conceptual Model

---

## 1. Purpose and Scope

### 1.1 Document Purpose

This document defines the **Platform Conceptual Model**, providing a formalized representation of the domain concepts and architectural principles that underlie the platform's functionality.

The conceptual model serves as a shared vocabulary and reference framework for understanding:

- **What** the platform manages (business objects)
- **How** object behavior is defined (workflows and programmable business logic)
- **Why** behavior is configurable rather than prescribed (separation of platform capabilities from business logic)

### 1.2 Intended Audience

| Audience | Use Case |
|----------|----------|
| **Business Analysts** | Understanding domain requirements and mapping business processes to platform capabilities |
| **Product Managers** | Defining feature requirements with consistent terminology |
| **Solution Architects** | Designing configurations and integrations that align with platform semantics |

### 1.3 Conceptual vs. Operational Distinction

This document describes the platform at a **conceptual level**, focusing on business meaning and logical structure rather than operational details.

**This document IS:**
- A definition of business objects and their responsibilities
- An explanation of workflow as the behavior-definition mechanism
- A description of how the platform separates capabilities from business logic
- A statement of what the platform intentionally does not prescribe

**This document IS NOT:**
- A user interface guide or navigation reference
- A database schema or API specification
- A predefined process or lifecycle specification

---

## 2. Core Platform Concepts

The platform is a **workflow-driven system** that provides capabilities for managing business objects while delegating behavior definition to user-configured workflows and business logic.

### 2.1 Fundamental Principle: Separation of Capabilities from Business Logic

The platform distinguishes between:

| Layer | Responsibility |
|-------|----------------|
| **Platform Capabilities** | Providing mechanisms for object management, automation, relationships, and data handling |
| **Business Logic** | User-defined rules, states, transitions, and processes that govern object behavior |

This separation means the platform does not impose specific business processes, statuses, or lifecycles. Instead, it provides the tools for organizations to configure their own.

### 2.2 Business Objects

A **Business Object** is a discrete entity managed by the platform. Business objects represent things of business significance—whether tangible (a computer) or conceptual (an incident).

Each business object:
- Has a defined set of **attributes** (both system and user-defined)
- Can participate in **relationships** with other business objects
- Has behavior governed by **workflow configuration**
- Can be created, modified, and processed through **actions**

### 2.3 Workflows

A **Workflow** is the complete configuration that governs how a business object behaves within the platform. Workflows are user-defined and encompass:

**Workflow Items** (executable units):
- **Actions**: Operations that users or services perform on objects (Create Actions, Step Actions)
- **Triggers**: Automated responses to events or conditions (including routing, escalation, prioritization)

**Workflow Components** (reusable building blocks used by workflow items):
- **Templates**: Field initialization patterns used by Create Actions
- **Forms**: User input dialogs used by Actions
- **Functions**: Reusable operation blocks
- **Notifications**: Email and push notification definitions

Workflows define the "how" of object management—what actions are available, what happens when conditions are met, and how objects transition through user-defined states.

### 2.4 Programmable Business Logic

**Business Logic** encompasses the rules, conditions, and automations that determine how business objects behave. The platform provides a programmable model where:

- **States and statuses** are user-defined classification values
- **Transitions** between states are controlled by configured actions and triggers
- **Automation rules** execute based on events, conditions, or schedules
- **Validation and constraints** are defined through workflow configuration

The platform does not prescribe what states an object should have or how it should progress. It provides the mechanisms; the organization defines the logic.

### 2.5 Data Sources and Integrations

The platform supports **multiple methods for data population**:

| Method | Description |
|--------|-------------|
| **Manual Input** | Direct creation and modification through user interaction |
| **Import/Export** | Batch loading and extraction of data |
| **Email Integration** | Creation and updates triggered by email processing |
| **Directory Services** | Synchronization with Active Directory and other LDAP-compatible services |
| **Network Discovery** | Automated detection of network nodes and devices |
| **Agent-Based Inventory** | Detailed hardware/software inventory via deployed agents (e.g., AlloyScan) |
| **External Tools** | Integration with third-party inventory, monitoring, or management tools |
| **API Integration** | Programmatic access for external system integration |

Each method is a data source capability, not a platform requirement. Organizations select and configure the methods appropriate to their needs.

### 2.6 Object Relationships

Business objects can be connected through **relationship links** that capture dependencies, associations, and hierarchies.

| Relationship Kind | Description |
|-------------------|-------------|
| **Parent-Child** | Asymmetrical containment or dependency (e.g., "contains" / "is contained in") |
| **Sibling** | Symmetrical association (e.g., "is related to") |

Relationship types are configurable. Organizations define the types of relationships meaningful to their context and specify which object classes can participate in each type.

---

## 3. Business Object Categories

Business objects are the fundamental entities that the platform manages. They are organized into categories based on their conceptual purpose.

| Category | Purpose | Examples |
|----------|---------|----------|
| **Service Management** | Handle service-related activities and requests | Incident, Service Request, Change Request, Problem, Work Order, Approval, SLA, Service, Recurrent Ticket, Announcement |
| **Project Management** | Manage IT projects and tasks | Project, Project Task |
| **Configuration Items** | Track managed IT infrastructure components | Computer, Hardware, Network, Document, Configuration, Tracked Software |
| **Asset & Financial** | Financial tracking, ownership, and procurement | Asset, Contract, Purchase Order, PO Item, Vendor, Cost Center, Warranty |
| **Inventory Management** | Stock, consumables, and equipment lending | Stock Room, Stock Rule, Consumable, Product Catalog Item, Equipment, Equipment Reservation |
| **Software Management** | Software products, licenses, and compliance | Software Catalog Item, Software License, Tracked Software, Software Installation |
| **Organizational** | Represent people and organizational structures | Person, Organization, Department, Group, Location, Company Address, Brand |
| **Platform Configuration** | Define platform behavior and structure | Workflow, Object Class, Form, Field, Action, Trigger, Function, Template, Security Role |
| **Support** | Provide operational aids and system records | Knowledge Article, Attachment, Note, Email, Audit Trail, Report, Dashboard, Saved Search |

### Key Characteristics

All business objects share common characteristics within the platform:

- **Attributes**: Each object has system-defined and user-defined fields
- **Relationships**: Objects can be linked to other objects through configurable relationship types
- **Behavior**: Object behavior is governed by workflow configuration, not predefined lifecycles
- **Actions**: Operations available on objects are defined by workflow, not hard-coded

### Behavior Classification

Objects exhibit different behavior patterns:

| Behavior Type | Description | Examples |
|---------------|-------------|----------|
| **Workflow-governed** | State, transitions, and automation defined by user-configured workflows | Incident, Change Request, Computer |
| **Static reference** | Serves as reference data with minimal state changes | Vendor, Location, Cost Center |
| **System-managed** | Created and maintained by platform mechanisms | Audit Trail Entry, Attachment |

For detailed definitions of business objects, see [Business Objects Reference](business-objects-reference.md).

### Important Conceptual Distinctions

- **Asset vs Configuration Item**: Asset is a financial overlay (cost, depreciation, ownership) that can be associated with a CI. The CI tracks technical configuration; the Asset tracks financial data.
- **Equipment vs Hardware**: Hardware is a CI for inventory tracking. Equipment (Library Item) is for temporary lending to users with check-out/check-in.
- **Configuration vs Configuration Item**: Configuration is an object describing a standard setup (e.g., "Email Configuration"). CI is the ITIL term for any managed component.

---

## 4. Workflow-Centric Model

### 4.1 Workflows as the Primary Mechanism

The platform positions **workflows as the primary mechanism** for defining business object behavior. Rather than providing fixed lifecycles with predetermined statuses, the platform provides:

- A **classification system** where users define the values for Status, Type, Category, and other classifiers
- A **lifecycle stage mechanism** where users define stages, map them to statuses, and control their sequence
- An **action system** where users define what operations are available and what those operations do
- A **trigger system** where users define automated responses to events and conditions

### 4.2 User-Defined States and Transitions

| Concept | Platform Provides | User Defines |
|---------|-------------------|--------------|
| **Status** | Field mechanism and classification structure | Actual status values and their meanings |
| **Lifecycle Stage** | Stage grouping mechanism and visualization | Stages, their sequence, and status mapping |
| **Transition** | Action and trigger execution framework | Which transitions exist and their conditions |
| **Rules** | Condition evaluation and operation execution engine | Specific conditions and resulting operations |

### 4.3 Workflow Building Blocks

#### Actions

Actions are operations that modify business objects. Conceptually, actions are either initiated by a person (interactive actions) or initiated by a service/integration channel (service actions).

##### Create Actions

Create Actions define how new objects are created and initialized. Multiple Create Actions can exist for the same object class, each employing different initialization logic for specific scenarios.

A Create Action is typically composed of:

| Component | Purpose |
|-----------|---------|
| **Template** | Assigns initial values to object fields |
| **Form** | Collects required inputs (when the action is interactive) |
| **Logic** | Executes configured operations upon object creation |

The logic component enables workflow-governed automation during object creation, such as initializing derived fields, creating related objects, establishing relationships, invoking reusable functions, sending notifications, and calling configured integrations. Operations execute in a defined order and can be conditional.

##### Step Actions

Step Actions perform operations on existing objects during their lifecycle:
- State transitions between user-defined classification values
- Field modifications (e.g., adding annotations, updating categorization or prioritization attributes)
- Relationship changes (e.g., linking related objects, creating related tasks)

Step Actions operate on already-created objects and can collect inputs (via forms) and execute configured logic/operations. Templates are primarily used to initialize newly created objects (via Create Actions) and can also be applied when workflow logic creates related objects.

##### Service Actions

Service Actions are initiated by integration channels rather than users. Conceptually, these channels can include email processing, directory synchronization, optional inventory/discovery tools (including agent-based inventory), portals, and other third-party integrations. Service actions can map incoming attributes to object fields according to user-configured rules.

#### Triggers

Triggers are automated operations that execute based on conditions or events:

| Trigger Type | Fires When |
|--------------|------------|
| **On Condition** | A specified condition becomes true (evaluated on schedule or on change) |
| **On Created/Modified** | An object is created or modified |
| **On Delete** | An object is deleted |

Triggers use the same Programming operations as Actions but execute automatically without user initiation.

**Common trigger use cases:**
- **Automatic routing**: Assign objects to technicians based on category, location, or other criteria
- **Escalation**: Increase priority or reassign when objects approach due dates or remain unresolved
- **Prioritization**: Calculate due dates based on urgency, impact, or requester
- **Field synchronization**: Update related objects when source object changes
- **Notifications**: Send alerts when conditions are met

Scheduled triggers (On Condition with time intervals) enable time-based automation such as escalating aging service desk objects.

#### Workflow Components

Workflow Components are reusable building blocks used by Actions and Triggers. They are configured separately and can be shared across multiple workflow items.

| Component | Purpose | Used By |
|-----------|---------|---------|
| **Template** | Assigns initial field values to objects | Create Actions, Create Object operations |
| **Form** | Collects user input through dialogs | Actions (Interactive) |
| **Function** | Reusable block of operations | Actions, Triggers, other Functions |
| **Notification** | Email or push message definition | Actions, Triggers |

Templates, Forms, and Functions can be:
- Created within an Action (custom, single-use)
- Created independently and reused across multiple Actions (reusable)

Functions enable modular logic definition and consistent operation execution across workflow items.

#### Workflow Parameters

Workflow Parameters are configuration values that can be referenced across multiple workflow items (Actions, Triggers, Functions). They enable:

- **Centralized configuration**: Change a value once, update all references automatically
- **Reduced redundancy**: Avoid duplicating the same value in multiple places
- **Simplified customization**: Modify workflow behavior without editing individual items

Example uses:
- Default assignee for a category
- Escalation timeout threshold
- Notification recipient email

Parameters are referenced using placeholders (e.g., `%[WFP:ParameterName]%`) in field values, conditions, and notifications.

#### Terminal Status
Terminal status is a user-configured characteristic of a classification value that indicates an object is considered complete for automation and governance purposes. Importantly, terminal statuses (and their semantics) are not predefined by the platform; they are part of user-configured business logic.

### 4.4 Separation of Platform Capabilities from Business Logic

The platform provides the **machinery**:
- Field definitions and customization
- Classification lists and hierarchies
- Action and trigger execution
- Condition evaluation
- Notification delivery
- Relationship management

The organization provides the **logic**:
- What statuses exist and what they mean
- What stages objects progress through
- What actions are available and when
- What conditions trigger automation
- What notifications are sent and to whom

---

## 5. Data Population Model

### 5.1 Multiple Supported Methods

The platform supports data population through multiple channels, each serving different use cases:

| Method | Conceptual Purpose |
|--------|-------------------|
| **Manual Input** | Direct data entry through configured forms and templates |
| **Import/Export** | Batch data movement for initial population or periodic synchronization |
| **Email Integration** | Object creation and updates triggered by email content |
| **Directory Synchronization** | Person and organizational data from directory services |
| **Network Discovery** | IT infrastructure detection via network scanning |
| **Agent-Based Inventory** | Detailed hardware/software inventory via deployed agents (e.g., AlloyScan) |
| **External Tools** | Data from third-party inventory, monitoring, or management systems |
| **API Access** | Programmatic data exchange with external systems |

### 5.2 Inventory Tools as Data Sources

Inventory and discovery tools (network discovery, agent-based scanners like AlloyScan, and third-party integrations) are **optional data sources** for populating asset information. They are:

- **Capabilities**, not platform requirements
- **Integration options** among many for asset data
- **Configurable** in terms of what data is synchronized and how
- **Conceptually equal** to other data population methods

These tools do not define platform behavior or object lifecycles. They provide data that enters the platform through configured synchronization rules and is then governed by the same workflow mechanisms as any other data.

### 5.3 Data Source Independence

Business objects exist independently of how they were populated. An object created manually, imported from a file, synchronized from a directory service, or populated via optional inventory tools or external integrations is the same conceptual entity with the same workflow-governed behavior.

---

## 6. Conceptual Boundaries

### 6.1 What the Platform Provides

| Capability | Description |
|------------|-------------|
| **Object Management** | Creation, modification, retrieval, and deletion of business objects |
| **Relationship Management** | Configurable links between objects with typed relationships |
| **Workflow Engine** | Execution of actions, triggers, and business policies |
| **Classification System** | User-defined lists for statuses, types, categories, and other classifiers |
| **Automation Server** | Background execution for scheduled and triggered operations |
| **Integration Framework** | Connectors for email, directory services, optional inventory tools (e.g., network discovery and agent-based inventory), and external systems |
| **Security Model** | Role-based access control with configurable permissions |
| **Extensibility** | Custom fields, custom object types, and custom workflow components |

### 6.2 What the Platform Does NOT Prescribe

The platform intentionally **does not define**:

| Aspect | Platform Position |
|--------|-------------------|
| **Fixed Lifecycles** | The platform provides lifecycle mechanisms; organizations define actual lifecycles |
| **Predefined Statuses** | Status values are classification entries configured by the organization |
| **Mandated Processes** | The platform supports process automation; it does not require specific processes |
| **Required Transitions** | Transition paths are defined by workflow configuration, not platform rules |
| **Specific Business Rules** | Validation, routing, escalation, and other rules are user-configured |
| **Single Data Source** | Multiple data population methods are supported; none is required |

### 6.3 Customization vs. Configuration

The platform operates on a **configuration-first** model:

- **Configuration** adjusts behavior within the platform's capability framework
- Business objects, workflows, classifications, and integrations are configured, not coded
- The platform provides the structure; the organization populates it with their specific logic

---

## Appendix A: Glossary

| Term | Definition |
|------|------------|
| **Action** | An operation that modifies a business object; includes Interactive Actions (user-initiated) and Service Actions (system-initiated) |
| **Business Logic** | The user-defined rules, conditions, and automations governing object behavior |
| **Business Object** | A discrete entity managed by the platform representing something of business significance |
| **Classification** | User-defined values for categorization fields (Status, Type, Category, etc.) |
| **Create Action** | An action that creates and initializes a new business object; consists of Template, Form, and Programming components |
| **Form** | A customizable dialog or wizard that collects user input during action execution |
| **Function** | A reusable block of operations callable from actions, triggers, or other functions |
| **Lifecycle Stage** | A user-defined grouping of related statuses representing a phase in object handling |
| **Operation** | A single executable unit within Programming (e.g., Update Field, Execute SQL, Create Object) |
| **Programming** | The component of an action containing operations that execute during action processing |
| **Relationship** | A typed connection between two business objects |
| **Service Action** | An action initiated by platform services (Mail Connector, Discovery, Active Directory) rather than users |
| **Step Action** | An action that performs operations on an existing object during its lifecycle |
| **Template** | A workflow component that assigns initial values to object fields; used by Create Actions |
| **Terminal Status** | A final status state where an object is complete and protected from automated updates |
| **Trigger** | An automation rule executing based on events, conditions, or schedules |
| **Workflow** | The complete configuration governing a business object class's behavior |
| **Workflow Component** | A reusable building block (Template, Form, Function, Notification) used by workflow items |
| **Workflow Item** | An executable workflow unit (Action or Trigger) |
| **Workflow Parameter** | A reusable configuration value referenced across workflow items via placeholders |

---

*Document Version: 2.4*
*Model Scope: Platform Conceptual Architecture*
