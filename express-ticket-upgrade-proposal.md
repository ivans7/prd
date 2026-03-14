# Предложение: Прозрачный Upgrade Express на Enterprise (Tickets)

**Дата:** Январь 2026
**Статус:** Proposal
**Scope:** Express Edition Ticket Model → Enterprise ITIL Service Management

---

## Executive Summary

**Проблема:** Текущий upgrade с Express на Enterprise создаёт терминологический и концептуальный разрыв:
- Express "Ticket" переименовывается в "Work Order" без объяснения
- ITIL объекты (Incident, Service Request, Problem) внезапно появляются БЕЗ готовых workflows
- Пользователи не понимают разницу между Work Order, Incident, Service Request
- Нет guidance какие типы задач должны быть Incidents, какие Requests

**Решение:**
1. Добавить опциональную Ticket Type classification в Express (не обязательную)
2. Скрыть ITIL-атрибуты в Express, активировать при upgrade
3. Pre-configured ITIL workflows готовые к использованию
4. Upgrade Wizard с guidance и опциональной классификацией старых Work Orders

**Ключевой принцип:** НЕ обязательная миграция данных, а добавление metadata для будущей классификации и guidance при upgrade.

---

## Текущая ситуация (As-Is)

### Express Edition: Ticket Model

**Database Level:**
- Ticket = Work Order таблица (одна и та же сущность)
- Ticket имеет рекурсивную иерархию (Parent Ticket → Child Tickets → Grandchild Tickets...)
- Change Request может иметь Child Tickets

**UI Level:**
- Work Order показывается как "Ticket"
- Child Tickets tab (для Ticket и Change Request)
- Templates для создания Tickets
- Business Policies для автоматизации

**Структура:**
```
Ticket (Work Order в БД)
├── Tabs: General, Activity, Resolution, Custom Data, Child Tickets, Related Objects, Notes, Attachments, History
├── Relationships: Requester, Assignee, Related Assets, Parent Change Request, Parent Ticket, Child Tickets
├── Workflows: Business Policies (Routing, Escalation, Prioritization) + Business Rules
└── Hierarchy: Ticket → Child Ticket → Grandchild Ticket (рекурсивная)

Change Request
├── Tabs: General, Activity, Approvals, Custom Data, Child Tickets, Related Objects, Notes, Attachments, History
└── Relationships: Child Tickets (те же Work Orders в БД)
```

**Два типа тикетов через Templates:**

| Template | Роль | Особенность |
|----------|------|-------------|
| **Quick Request [#840]** | Primary ticket | Standalone или parent, доступен в Desktop/Web/Mobile |
| **Quick Task (Child) [#841]** | Child ticket | Помечен "This template creates child tickets", создаётся только через Child Tickets tab |

**Note:** Это разделение уже существует в Express и концептуально соответствует Incident (primary) vs Work Order (supporting task) в ITIL. Однако оба типа используют одну таблицу WorkOrder в БД.

### Enterprise Edition: ITIL Service Management

**Database Level:**
- Work Order (отдельный объект для task decomposition)
- Incident (service interruptions)
- Service Request (fulfillment requests)
- Problem (root cause analysis)
- Change Request (controlled changes)

**UI Level:**
- Work Order показывается как "Work Order" (не "Ticket")
- Incident, Service Request, Problem visible и доступны
- Full Triggers с programming
- Multi-stage approvals, SLA, Service Management

**Структура:**
```
Work Order
├── Parent: Change Request, Service Request, Incident, or another Work Order
└── Can have child Work Orders (рекурсивная иерархия поддерживается)

Incident
├── Related: Problem, Change Request
└── Can have Work Orders (decomposition)

Service Request
├── Approval stages
└── Can have Work Orders (fulfillment tasks)

Problem
├── Related Incidents
└── Related Change Request (permanent fix)

Change Request
└── Has Work Orders (implementation tasks)
```

---

## Проблемы при текущем Upgrade

### 1. Терминологический разрыв

| Aspect | Express | После Upgrade | User Confusion |
|--------|---------|---------------|----------------|
| **Main Object** | "Ticket" | "Work Order" | Where did my Tickets go? |
| **ITIL Objects** | Hidden (не видны) | Visible (Incident, Request, Problem) | What are these? When to use? |
| **Child Tickets** | Child Tickets tab | Work Orders tab (Change Request) | What's the difference? |
| **Terminology** | User-friendly ("Ticket") | ITIL-formal ("Work Order") | Confusing rename |

### 2. Концептуальный разрыв

**Пользователи Express привыкли:**
- Создавать "Ticket" для любой задачи (что-то сломалось, запрос на доступ, задача обслуживания)
- "Ticket" = универсальный work item

**В Enterprise нужно понимать:**
- Incident = something broke (reactive)
- Service Request = request for something (proactive)
- Problem = root cause analysis
- Work Order = implementation task (part of larger work)

**Gap:** Нет guidance какие старые "Tickets" должны быть Incidents, какие Requests, какие Work Orders.

### 3. Workflow разрыв

| Aspect | Express | Enterprise | Impact |
|--------|---------|------------|--------|
| **Automation** | Business Policies | Triggers (full programming) | Need reconfiguration |
| **Incident Workflows** | N/A (hidden) | Нет pre-configured workflows | User must configure from scratch |
| **Request Workflows** | N/A (hidden) | Нет pre-configured workflows | User must configure from scratch |
| **Work Order Workflows** | Ticket workflows exist | Must map from Ticket workflows | Need manual mapping |

### 4. Иерархия и Child Tickets

**Express:**
```
Ticket
└── Child Ticket
    └── Grandchild Ticket (рекурсивная иерархия)

Change Request
└── Child Ticket (Work Order в БД)
```

**Enterprise:**
```
Work Order (supporting task в ITIL терминологии)

Change Request
└── Work Order

Service Request
└── Work Order

Incident
└── Work Order (редко)
```

**Проблема:** Express Ticket семантически = primary work item, Enterprise Work Order = supporting task (разная ITIL семантика, хотя платформа технически поддерживает иерархию WO).

---

## Анализ конкурентов

### ServiceNow Essentials (Express) → Enterprise

**Essentials:**
- Simplified Incident, Problem, Change modules available
- Pre-configured ITIL workflows из коробки
- Ограниченная кастомизация (no scripting)

**Enterprise:**
- Same objects (Incident, Problem, Change)
- Workflows расширяются, добавляется scripting
- NO renaming (Incident остается Incident)

**Upgrade:** Smooth - workflows expand, terminology consistent.

### Freshservice Sprout (Express) → Growth/Enterprise

**Sprout:**
- Unified "Ticket" object с Type field (Incident, Service Request, Problem)
- Workflow Automator с templates
- Limited scripting

**Growth/Enterprise:**
- Same "Ticket" object, same Type field
- Workflow Automator unlocks scripting
- Advanced features (SLA, Service Catalog)

**Upgrade:** Seamless - Type field persists, no terminology change.

### Jira Service Management Standard → Premium

**Standard:**
- Request Types определяют behavior
- Simplified workflows
- Limited automation

**Premium:**
- Same Request Types
- Advanced workflows, SLA, Assets

**Upgrade:** Continuous - Request Types unchanged.

---

## Выводы из анализа

| Aspect | ServiceNow | Freshservice | Jira | AN Current |
|--------|------------|--------------|------|-----------|
| **Terminology Change** | No (Incident → Incident) | No (Ticket → Ticket) | No (Request → Request) | **YES (Ticket → Work Order)** |
| **Pre-configured ITIL** | Yes (Essentials has workflows) | Yes (Templates) | Yes (Request Types) | **NO (ITIL hidden)** |
| **Type Field** | Objects separate from start | Unified Ticket + Type | Request Types | **NO (generic Category only)** |
| **Upgrade Experience** | Smooth (expand) | Seamless (unlock) | Continuous (same model) | **Jarring (rename + new objects)** |

**Вывод:** AN — единственная платформа где upgrade приводит к терминологическому разрыву и появлению новых объектов без workflows.

---

## Предлагаемое решение

### Концепция

**Принцип:** Добавить metadata в Express для облегчения upgrade, НЕ требуя обязательной миграции данных.

### Изменения в Express Edition

#### 1. Опциональное поле Ticket Type (Classification)

**Новое поле:** `Ticket_Type` (опциональное, не обязательное)

**Значения:**
- **Incident-like** — Something is broken (reactive)
- **Request-like** — Request for something (proactive)
- **Task** — General work task (default если не выбрано)
- *Not Set* — User didn't classify (большинство старых Tickets)

**UI Placement:**
- Optional field на Ticket form (не обязательное к заполнению)
- Can be set at creation или updated later
- Filter/Group by Type в views

**Purpose:**
- Helps users understand ITIL concepts gradually
- Provides metadata for optional conversion при upgrade
- NOT enforced (user can leave blank)

#### 2. Скрытые ITIL-атрибуты (Hidden Fields)

**Добавить в Ticket БД, скрыть в Express UI:**

| Поле | Тип | Express UI | Enterprise UI | Purpose |
|------|-----|------------|---------------|---------|
| `Service_ID` | Link | ❌ Hidden | ✓ Visible | For future ITIL classification |
| `Urgency` | Classification | ❌ Hidden | ✓ Visible | Priority calculation (Enterprise) |
| `Impact` | Classification | ❌ Hidden | ✓ Visible | Priority calculation (Enterprise) |

**Логика:**
- Поля существуют в БД, NULL по умолчанию
- Express Priority calculation uses current logic (не меняется)
- При upgrade поля становятся visible для новых Incidents/Requests

#### 3. Pre-configured ITIL Workflows (Hidden в Express)

**Создать и скрыть:**

**Incident Workflow:**
```
Create Action: Create Incident
├── Template: Requester, Service, Urgency, Impact
├── Form: User input
└── Programming:
    ├── Calculate Priority (Urgency + Impact matrix)
    ├── Auto-route based on Category + Location
    └── Send notification to Assignee

Step Actions:
├── Assign, Investigate, Resolve, Close, Reopen
└── Triggers:
    ├── On Created: Auto-route
    ├── On Modified: Update Priority if Urgency/Impact changed
    ├── On Condition (age > 4 hours): Escalate
    └── On Resolved: Notify Requester
```

**Service Request Workflow:**
```
Create Action: Create Service Request
├── Template: Requester, Service, Request Type
├── Form: User input
└── Programming:
    ├── Check if Approval required (based on Category)
    ├── Create Approval if needed
    └── Send confirmation to Requester

Step Actions:
├── Submit for Approval, Approve/Reject, Fulfill, Complete, Close
└── Triggers:
    ├── On Created: Create Approval if required
    ├── On Approved: Create Work Orders for fulfillment
    ├── On All Work Orders Complete: Transition to Complete
    └── On Complete: Notify Requester
```

**Status:** Hidden в Express, activated при upgrade.

---

## Upgrade Process (Express → Enterprise)

### Phase 1: Pre-Upgrade Analysis

**Upgrade Wizard анализирует:**
```
Found 1,250 Tickets:
├── Ticket_Type = "Incident-like": 320 tickets (25.6%)
├── Ticket_Type = "Request-like": 180 tickets (14.4%)
├── Ticket_Type = "Task": 450 tickets (36%)
├── Ticket_Type = Not Set: 300 tickets (24%)

Found 85 Change Requests:
└── With Child Tickets: 85 (all have Work Orders in DB already)
```

### Phase 2: User Decision

**Upgrade Wizard presents options:**

**Option A: Keep All as Work Orders (Recommended for most)**
```
Express "Ticket" → Enterprise "Work Order"
- All existing Tickets remain Work Orders
- UI terminology changes: "Ticket" → "Work Order"
- Child Tickets tab → Work Orders tab (Change Request)
- Incident, Service Request, Problem available for NEW records
- No data migration, only UI change
```

**Advantages:**
- No data migration (zero risk)
- Immediate upgrade (no downtime)
- Users create new Incidents/Requests going forward
- Historical data preserved as Work Orders

**Option B: Classify and Convert (Advanced)**
```
Optionally convert SOME Work Orders → Incidents/Service Requests based on Ticket_Type:
- Ticket_Type = "Incident-like" → Can convert to Incident
- Ticket_Type = "Request-like" → Can convert to Service Request
- Ticket_Type = "Task" or Not Set → Remain Work Orders
```

**User selects which to convert:**
- Review list of Ticket_Type = "Incident-like" (320 tickets)
- Select which to convert (can select all, some, or none)
- Same for "Request-like"
- Wizard performs conversion

**Advantages:**
- Historical Incidents properly classified
- Better reporting (Incidents vs Requests vs Work Orders)
- ITIL-aligned from day 1

**Disadvantages:**
- Requires review and decision (time investment)
- Risk of incorrect classification
- More complex rollback

### Phase 3: Conversion Logic (if Option B chosen)

**For each selected Work Order:**

```
IF converting to Incident:
    ├── Create Incident record (copy all fields)
    ├── Set Service_ID (from hidden field or NULL)
    ├── Set Urgency/Impact (default: Medium/Medium)
    ├── Calculate Priority (Urgency + Impact matrix)
    ├── Preserve Object_ID (for backward compatibility)
    ├── Copy all relationships (Requester, Assignee, Related Assets)
    ├── Copy history, attachments, notes
    └── Mark original Work Order as "Migrated to Incident-XXX"

IF converting to Service Request:
    ├── Create Service Request record (copy all fields)
    ├── Set Service_ID (from hidden field or NULL)
    ├── Check if historical Approval exists → link
    ├── Preserve Object_ID
    ├── Copy all relationships
    └── Mark original Work Order as "Migrated to Request-XXX"

IF remaining as Work Order:
    └── No changes (UI shows "Work Order" instead of "Ticket")
```

### Phase 4: Child Tickets Handling

**Child Tickets под Change Request:**

Express БД уже содержит Work Orders как children Change Request. При upgrade:
```
Change Request → Child Tickets (UI Express)
Change Request → Work Orders (UI Enterprise)

Database: Same relationship, only UI terminology changes
```

**No migration needed** — просто UI переименование.

**Рекурсивная иерархия Tickets:**

Express Ticket может иметь Child → Grandchild hierarchy. Enterprise Work Order также поддерживает иерархию (платформа это позволяет).

**Note:** Иерархия Work Orders сохраняется при upgrade. Основная проблема — семантическая: Work Order в ITIL терминологии означает "supporting task", а не "primary work item" как Ticket.

### Phase 5: Workflow Activation

```
Activate Pre-configured ITIL Workflows:
    ├── Enable Incident Workflow
    ├── Enable Service Request Workflow
    ├── Problem Workflow available (empty template for user)
    └── Change Request Workflow (already exists, unchanged)

Convert Business Policies → Triggers:
    ├── Routing Policy → Trigger (On Created: Assign based on Category)
    ├── Prioritization Policy → Trigger (On Created: Calculate Priority)
    ├── Escalation Policy → Trigger (On Condition: Escalate on aging)
    └── User reviews before activation
```

### Phase 6: Post-Upgrade Guidance

**Wizard shows:**
```
Upgrade Complete!

Your Work Orders (formerly "Tickets"): 1,250
├── Converted to Incidents: 280 (Option B selected)
├── Converted to Service Requests: 150 (Option B selected)
└── Remain as Work Orders: 820

Available Now:
✓ Incident management (pre-configured workflow)
✓ Service Request management (pre-configured workflow)
✓ Problem management (configure as needed)
✓ Work Order decomposition (unchanged)

Next Steps:
1. Review Incident workflow (customize if needed)
2. Review Service Request workflow (customize if needed)
3. Train staff on ITIL concepts (Incident vs Request vs Work Order)
4. Create new Incidents/Requests going forward
```

---

## Преимущества решения

### 1. Минимальный риск

| Aspect | Advantage |
|--------|-----------|
| **No Forced Migration** | Option A requires zero data migration |
| **Optional Conversion** | Option B only for users who want ITIL classification |
| **Rollback Simple** | Option A easily reversible (UI change only) |
| **Data Preserved** | All historical data intact |

### 2. Прозрачный upgrade

| Aspect | Before | After |
|--------|--------|-------|
| **Terminology** | Ticket (Express-specific) | Work Order (proper ITIL term) |
| **ITIL Objects** | Hidden (not visible) | Visible with pre-configured workflows |
| **Guidance** | No classification | Optional Ticket Type helps understand |
| **Workflows** | Business Policies | Triggers (mapped automatically) |

### 3. Соответствие конкурентам

| Aspect | ServiceNow | Freshservice | AN Proposed |
|--------|------------|--------------|-------------|
| **Pre-configured ITIL** | ✓ Yes | ✓ Yes | ✓ Yes (after upgrade) |
| **Smooth Upgrade** | ✓ Expand | ✓ Unlock | ✓ Guidance + Optional conversion |
| **Type Classification** | ✓ Objects separate | ✓ Unified + Type | ✓ Optional Ticket Type |

### 4. Гибкость

- Users who want simple: Option A (no conversion)
- Users who want ITIL: Option B (selective conversion)
- Users can add Ticket Type gradually in Express (not forced)
- Historical data remains intact regardless of choice

---

## Риски и митигация

### Критические риски

| Риск | Описание | Митигация |
|------|----------|-----------|
| **Incorrect Classification** | User converts wrong Work Orders → Incidents | Wizard shows preview, allows review before commit |
| **Child Relationships** | Hierarchy preserved (WO supports parent-child) | Wizard preserves full hierarchy during upgrade |
| **Rollback Complexity** | Option B harder to rollback | Backup before conversion, snapshot at each step |

### Высокие риски

| Риск | Описание | Митигация |
|------|----------|-----------|
| **User Confusion** | Don't understand Incident vs Request vs Work Order | Guidance wizard, tooltips, training materials, Option A (no conversion) |
| **Reports Break** | Old reports reference "Ticket" | Compatibility view (Ticket = Work Order alias for queries) |
| **API Compatibility** | External integrations use "Ticket" endpoint | Versioned API, "Ticket" endpoint → Work Order backend (transparent) |

### Средние риски

| Риск | Описание | Митигация |
|------|----------|-----------|
| **Partial Adoption** | Some users never classify Ticket Type | Optional field, not enforced, works without it |
| **Performance** | Conversion of 10k+ Work Orders | Batch processing, progress indicator, pausable wizard |

---

## Варианты реализации

### Вариант A: Minimal (Recommended)

**Описание:**
- Add optional Ticket Type field (not enforced)
- Hide ITIL attributes (Service, Urgency, Impact)
- Pre-configure ITIL workflows (hidden в Express)
- Upgrade Wizard with Option A (keep as Work Orders) as default

**Преимущества:**
- Minimal changes to Express
- Low risk
- Fast implementation (~2-3 months)

**Недостатки:**
- No automatic conversion (manual work if users want ITIL classification)

---

### Вариант B: Guided Conversion (Proposed)

**Описание:**
- Add optional Ticket Type field (with guidance/hints)
- Hide ITIL attributes
- Pre-configure ITIL workflows
- Upgrade Wizard with both Option A and Option B
- Conversion logic for selected Work Orders → Incidents/Requests

**Преимущества:**
- Best of both worlds (simple upgrade OR ITIL classification)
- Users choose based on needs
- Pre-configured workflows ready
- Smooth guidance

**Недостатки:**
- More complex Wizard
- Conversion logic requires testing
- ~4-5 months implementation

---

### Вариант C: Forced Conversion

**Описание:**
- Ticket Type required (not optional)
- Automatic conversion ALL Work Orders → Incidents/Requests based on Type
- No Option A (keep as Work Orders)

**Преимущества:**
- Forces ITIL adoption
- Clean ITIL structure from day 1

**Недостатки:**
- HIGH RISK (forced migration)
- Potential incorrect classifications
- Complex rollback
- User resistance
- NOT RECOMMENDED

---

## Рекомендация: Вариант B (Guided Conversion)

**Обоснование:**

1. **Flexibility:** Users choose upgrade path (simple or ITIL)
2. **Low Risk:** Default Option A (no conversion) for cautious users
3. **ITIL-Ready:** Pre-configured workflows for users who want ITIL
4. **Guidance:** Ticket Type helps understand concepts gradually
5. **Industry Standard:** Matches ServiceNow, Freshservice approach

**Success Criteria:**
- 80%+ users choose Option A (simple upgrade)
- 20% users choose Option B (ITIL conversion)
- Zero data loss
- <1% support tickets about upgrade confusion
- Rollback available for 30 days

---

## План реализации

### Phase 1: Express Enhancements (2 months)

**1.1 Database Changes**
- [ ] Add `Ticket_Type` classification field (optional)
- [ ] Add hidden fields: `Service_ID`, `Urgency`, `Impact` (NULL default)
- [ ] Add indexes for performance

**1.2 Express UI Changes**
- [ ] Add Ticket Type selector (optional field, not required)
- [ ] Add hints/tooltips ("Incident-like = something broke")
- [ ] Add Type filter/grouping in Ticket views
- [ ] NO changes to workflows (works without Type)

**1.3 Documentation**
- [ ] Express Admin Guide: Ticket Type classification (optional)
- [ ] Express User Guide: When to use each Type (guidance)

### Phase 2: Pre-configured ITIL Workflows (1.5 months)

**2.1 Incident Workflow**
- [ ] Create Action (Template + Form + Programming)
- [ ] Step Actions (Assign, Investigate, Resolve, Close, Reopen)
- [ ] Triggers (Auto-routing, Priority calc, Escalation, Notifications)
- [ ] Testing

**2.2 Service Request Workflow**
- [ ] Create Action (with Approval check)
- [ ] Step Actions (Submit, Approve/Reject, Fulfill, Complete, Close)
- [ ] Triggers (Approval creation, Work Order decomposition, Notifications)
- [ ] Testing

**2.3 Visibility Control**
- [ ] Hide ITIL workflows в Express (feature flag)
- [ ] Activate в Enterprise (feature flag)

### Phase 3: Upgrade Wizard (2 months)

**3.1 Pre-Upgrade Analysis**
- [ ] Scan Tickets, count by Type
- [ ] Identify relationships (Child Tickets, Change Requests)
- [ ] Generate report (PDF/HTML)

**3.2 Wizard UI**
- [ ] Step 1: Review Analysis Report
- [ ] Step 2: Choose Option A or Option B
- [ ] Step 3: (If Option B) Select Work Orders to convert
- [ ] Step 4: Configure defaults (Service, Urgency, Impact)
- [ ] Step 5: Review changes (preview)
- [ ] Step 6: Execute upgrade (progress bar)
- [ ] Step 7: Post-upgrade summary

**3.3 Conversion Engine (if Option B)**
- [ ] Work Order → Incident conversion logic
- [ ] Work Order → Service Request conversion logic
- [ ] Child Tickets hierarchy preservation
- [ ] Object_ID preservation
- [ ] Relationship copying

**3.4 Rollback Mechanism**
- [ ] Snapshot before upgrade
- [ ] Rollback UI (restore to pre-upgrade state)
- [ ] 30-day retention

### Phase 4: Business Policies → Triggers (1 month)

**4.1 Policy Analysis**
- [ ] Scan existing Business Policies
- [ ] Generate equivalent Triggers

**4.2 Conversion Wizard**
- [ ] Present generated Triggers to user
- [ ] Allow customization
- [ ] Activate Triggers, disable Policies

### Phase 5: Testing & Documentation (1.5 months)

**5.1 Testing**
- [ ] Unit tests (Wizard logic, conversion)
- [ ] Integration tests (end-to-end upgrade)
- [ ] Performance tests (10k+ Work Orders)
- [ ] Beta testing (pilot customers)

**5.2 Documentation**
- [ ] Upgrade Guide (step-by-step)
- [ ] ITIL Concepts Guide (Incident vs Request vs Work Order)
- [ ] Workflow Documentation (Incident, Service Request)
- [ ] Admin Guide updates
- [ ] Release Notes

**5.3 Training Materials**
- [ ] Video: Ticket Type in Express
- [ ] Video: Upgrade Wizard walkthrough
- [ ] Webinar: Express to Enterprise upgrade best practices

**Total Duration:** ~8 months

---

## Открытые вопросы

### По Ticket Type

1. **Default Value** — если не выбрано, какое значение? (Предложение: NULL / "Not Set")
2. **Enforce Later** — можно ли сделать обязательным в будущих версиях Express? (Предложение: Нет, всегда optional)
3. **Custom Types** — можно ли добавлять свои типы? (Предложение: Нет, только 3 предустановленных)

### По Upgrade Wizard

4. **Dry Run Mode** — нужен ли test mode перед реальным upgrade? (Предложение: Да, обязательно)
5. **Partial Conversion** — можно ли конвертировать по частям? (Предложение: Нет, all-or-nothing для Option B)
6. **Rollback Window** — сколько времени доступен rollback? (Предложение: 30 дней)

### По Иерархии

7. **Hierarchy Depth** — ограничивать ли глубину иерархии при upgrade? (Предложение: Нет, Work Order поддерживает полную иерархию)
8. **Hierarchy Validation** — валидировать ли циклические ссылки? (Предложение: Да, prevent circular references)

### По Workflows

9. **Customization Before Activation** — можно ли изменить workflows перед активацией? (Предложение: Да, review step)
10. **Problem Workflow** — нужен ли pre-configured Problem workflow? (Предложение: Нет, пустой template для user)

### По Compatibility

11. **API Versioning** — как поддерживать "Ticket" endpoint? (Предложение: Alias to Work Order, transparent)
12. **Reports Migration** — auto-convert reports? (Предложение: Compatibility view, no auto-conversion)

---

## Следующие шаги

### Immediate Actions

1. **Stakeholder Review** — презентовать Product Management и Engineering
2. **Customer Feedback** — interviews с Express customers (understand Ticket usage patterns)
3. **Competitor Deep Dive** — детальный анализ ServiceNow Essentials upgrade UX

### Design Phase

4. **UI Mockups** — Ticket Type selector, Upgrade Wizard screens
5. **Database Schema** — финализировать hidden fields structure
6. **Workflow Specification** — детализировать Incident и Request workflows

### Proof of Concept

7. **Upgrade Wizard POC** — prototype с test data (100 Work Orders)
8. **Performance Testing** — upgrade 10k Work Orders (measure time)
9. **Rollback Testing** — validate rollback mechanism works

---

## Заключение

Предлагаемое решение устраняет терминологический и концептуальный разрыв между Express и Enterprise путём:

1. **Опционального Ticket Type** — helps users understand ITIL concepts gradually (not enforced)
2. **Скрытых ITIL attributes** — ready for activation при upgrade (no impact on Express)
3. **Pre-configured ITIL workflows** — operational immediately после upgrade
4. **Guided Upgrade Wizard** — users choose simple (Option A) or ITIL (Option B)
5. **NO forced migration** — data preserved, minimal risk

Это обеспечивает:
- **Smooth upgrade experience** (Option A = UI change only, zero risk)
- **ITIL-ready for advanced users** (Option B = selective conversion with guidance)
- **Industry alignment** (matches ServiceNow, Freshservice approach)
- **Data integrity** (all historical data preserved)
- **Flexibility** (users choose upgrade complexity)

**Ключевое отличие от текущей ситуации:**
- Current: Ticket → Work Order (jarring rename, no guidance, no workflows)
- Proposed: Ticket → Work Order WITH guidance, optional classification, pre-configured ITIL workflows

---

*Связанные документы: Express Edition Conceptual Model, Express Business Objects Reference, Platform Conceptual Model, Business Objects Reference*

*Источники исследования: [Alloy Navigator Express Documentation](https://docs.alloysoftware.com/alloynavigatorexpress/), ServiceNow Essentials, Freshservice, Jira Service Management*
