# Предложение: Переход к модульной продуктовой модели

**Дата:** Январь 2026
**Статус:** Architectural Proposal
**Scope:** Unified Platform Architecture — Incident-Based Core + Modular Features

---

## Executive Summary

**Текущая проблема:**
- Жёсткое разделение Express vs Enterprise зашито в код
- Только 2 плана (Express, Enterprise) — нет гибкости
- Терминологический разрыв при upgrade (Ticket → Work Order)
- Невозможно создавать промежуточные планы или кастомные комбинации

**Предлагаемое архитектурное решение:**

1. **Базовая архитектура:** Ticket = Incident в базе данных (вместо текущего Ticket = Work Order)
2. **Первый шаг:** Aliasing управляется лицензией/модулями (не hardcoded в код)
3. **Конечная цель:** Полностью модульная модель — любые планы через комбинацию модулей

**Ключевые преимущества:**
- Прозрачный upgrade Express → Enterprise (Incident остается Incident, только убирается alias)
- Гибкость создания любых планов (Service Desk Plus, ITIL Lite, Custom Plans...)
- Parent-Child иерархия на базе Incidents (не Work Orders)
- Возможность включать/выключать ITIL терминологию независимо от функциональности

---

## Текущая ситуация (As-Is)

### Архитектура БД

```
Таблицы БД:
├── Incidents (ITIL service interruptions)
├── WorkOrders (task decomposition)
├── ServiceRequests (fulfillment requests)
├── Problems (root cause analysis)
└── ChangeRequests (controlled changes)
```

### Текущая реализация Express

**Hardcoded в коде продукта:**

```
IF Edition == "Express":
    UI.Alias["WorkOrder"] = "Ticket"
    UI.Hide(Incidents)
    UI.Hide(ServiceRequests)
    UI.Hide(Problems)

IF Edition == "Enterprise":
    UI.Show(WorkOrders)  // as "Work Order"
    UI.Show(Incidents)
    UI.Show(ServiceRequests)
    UI.Show(Problems)
```

**Структура данных Express:**

```
Express UI          →  Database           →  Enterprise UI
──────────────────────────────────────────────────────────
Ticket              →  WorkOrder          →  Work Order
Child Ticket        →  WorkOrder          →  Work Order
                    →  Incident (hidden)  →  Incident
                    →  ServiceRequest (hidden) → Service Request
                    →  Problem (hidden)   →  Problem
Change Request      →  ChangeRequest      →  Change Request
```

**Важно:** В Express уже существует концептуальное разделение на два типа тикетов через Templates:

| Template | Роль | Особенность |
|----------|------|-------------|
| **Quick Request [#840]** | Primary ticket | Standalone или parent, создаётся через New menu |
| **Quick Task (Child) [#841]** | Child ticket | Помечен "This template creates child tickets", создаётся только через Child Tickets tab |

Это разделение показывает, что в Express **уже есть** концепция primary work item (Quick Request) vs supporting task (Quick Task) — аналогично Incident vs Work Order в ITIL.

**Проблемы:**
1. ❌ Ticket = Work Order в БД (неправильная семантика)
2. ❌ Incidents скрыты, но существуют в БД (неиспользуемые таблицы)
3. ❌ При upgrade: Ticket → Work Order (терминологический разрыв)
4. ❌ Work Order семантически не соответствует "Ticket" (ITIL: supporting task vs primary work item)
5. ❌ Невозможно создать промежуточные планы

---

## Проблема: Почему Ticket ≠ Work Order

### Семантическая разница

| Aspect | Ticket (пользовательское понимание) | Work Order (ITIL) |
|--------|-------------------------------------|-------------------|
| **Purpose** | Универсальный work item | Task decomposition (часть большей работы) |
| **Parent** | Может быть standalone | Всегда часть чего-то (CR, SR, Incident) |
| **Examples** | "Принтер сломался", "Доступ к папке", "Установить софт" | "Заменить картридж в рамках CR-123", "Создать AD account в рамках SR-456" |
| **Hierarchy** | Может иметь children (sub-tasks) | В ITIL концептуально leaf node (хотя платформа поддерживает иерархию) |
| **Semantics** | Primary work item | Supporting work item |

**Вывод:** Ticket концептуально = Incident (primary work item), НЕ Work Order (supporting task).

### Текущая архитектура создаёт проблемы

**Express:**
```
Ticket (Work Order в БД)
└── Child Ticket (Work Order)
    └── Grandchild Ticket (Work Order) - рекурсивная иерархия
```

**Enterprise:**
```
Work Order - концептуально supporting task в ITIL (хотя платформа технически поддерживает иерархию)
```

**При upgrade:** Семантический разрыв — Work Order в ITIL терминологии означает "подзадача", а не "основной запрос".

**Если бы Ticket = Incident:**
```
Express:
Ticket (Incident в БД)
└── Child Ticket (Incident или Work Order)
    └── Grandchild Ticket (Incident или Work Order)

Enterprise:
Incident
├── Related Incidents (parent-child)
└── Work Orders (decomposition tasks)
```

**Результат:** Иерархия сохраняется, upgrade прозрачен.

---

## Предлагаемое решение: Incident-Based Architecture

### Концептуальная модель

**Базовый принцип:**
```
Ticket = Incident в базе данных

Express UI      →  Database    →  Enterprise UI
───────────────────────────────────────────────
Ticket          →  Incident    →  Incident (или "Ticket" если модуль включен)
Child Ticket    →  Incident    →  Incident
Child Ticket    →  WorkOrder   →  Work Order (для decomposition)
```

### Архитектурные изменения

#### 1. База данных остается неизменной

```
Таблицы (как сейчас):
├── Incidents (Core Object)
├── WorkOrders (Supporting Object)
├── ServiceRequests
├── Problems
└── ChangeRequests
```

**Изменение:** Incidents становится PRIMARY объектом для "tickets", не скрытым.

#### 2. Aliasing Layer (управляемый модулями)

**Вместо hardcoded:**
```python
# Старый код (hardcoded)
if product_edition == "Express":
    ui_label["WorkOrder"] = "Ticket"
```

**Новый код (module-driven):**
```python
# Новый код (configurable)
if modules.enabled("SimplifiedTerminology"):
    ui_label["Incident"] = "Ticket"
    ui_label["WorkOrder"] = "Child Ticket"

if modules.enabled("ITILTerminology"):
    ui_label["Incident"] = "Incident"
    ui_label["WorkOrder"] = "Work Order"
```

#### 3. Модульная архитектура

**Модули определяют functionality + terminology:**

```
Core Module: Service Management
├── Objects: Incident (primary), WorkOrder (supporting)
├── Features: Create, Assign, Resolve, Close
└── UI: Minimal (Templates, Notifications)

ITIL Terminology Module:
├── UI Labels: Incident, Service Request, Problem, Work Order
└── No functional changes (только терминология)

Simplified Terminology Module:
├── UI Labels: Ticket, Child Ticket
└── Hides ITIL complexity

Work Order Decomposition Module:
├── Enables: Incident → Work Order relationships
├── Enables: Service Request → Work Order relationships
└── UI: Work Orders tab on parent objects

Problem Management Module:
├── Objects: Problem visible
├── Features: Root cause analysis, Related Incidents
└── Workflows: Problem templates, triggers

Service Request Management Module:
├── Objects: Service Request visible
├── Features: Approvals, Catalog
└── Workflows: SR templates, fulfillment

Advanced Customization Module:
├── Features: Triggers (full programming), Custom Forms, Functions
└── Replaces: Business Policies, Business Rules

Incident Hierarchy Module:
├── Enables: Parent Incident → Child Incidents
└── UI: Child Incidents tab (рекурсивная иерархия)
```

---

## Примеры планов через модули

### Plan: Express (текущая функциональность)

```
Enabled Modules:
✓ Core Service Management
✓ Simplified Terminology
✓ Incident Hierarchy (Parent Ticket → Child Tickets)
✗ ITIL Terminology (disabled)
✗ Problem Management (disabled)
✗ Service Request Management (disabled)
✗ Advanced Customization (disabled)

Result:
- User sees: "Ticket", "Child Ticket"
- Database: Incident, WorkOrder (WorkOrder not used)
- Hierarchy: Incident → Incident (parent-child)
- Customization: Templates, Business Policies, Business Rules
```

### Plan: Enterprise (текущая функциональность)

```
Enabled Modules:
✓ Core Service Management
✓ ITIL Terminology
✓ Work Order Decomposition
✓ Problem Management
✓ Service Request Management
✓ Advanced Customization
✓ Incident Hierarchy (опционально)

Result:
- User sees: "Incident", "Service Request", "Problem", "Work Order"
- Database: Incident, WorkOrder, ServiceRequest, Problem
- Hierarchy: Incident → Incident (parent-child) + Incident → Work Order (decomposition)
- Customization: Full Triggers, Custom Forms, Functions
```

### Plan: Service Desk Plus (НОВЫЙ, промежуточный)

```
Enabled Modules:
✓ Core Service Management
✓ ITIL Terminology (Incident, Work Order)
✓ Work Order Decomposition
✓ Service Request Management
✗ Problem Management (disabled - дороже чем Express, дешевле Enterprise)
✗ Advanced Customization (disabled - только Business Policies)

Result:
- User sees: "Incident", "Service Request", "Work Order"
- Database: Incident, WorkOrder, ServiceRequest
- NO Problem Management
- Customization: Business Policies (не full Triggers)
```

### Plan: ITIL Lite (НОВЫЙ)

```
Enabled Modules:
✓ Core Service Management
✓ ITIL Terminology
✓ Simplified Terminology (dual labels: "Incident (Ticket)")
✗ Problem Management (disabled)
✗ Advanced Customization (disabled)

Result:
- User sees: "Incident (Ticket)" — оба термина одновременно для плавного перехода
- Database: Incident
- Customization: Business Policies
```

### Plan: Custom (гибкая комбинация)

```
Customer chooses modules:
✓ Core Service Management
✓ ITIL Terminology for Incidents only
✓ Simplified Terminology for Service Requests ("Request Ticket")
✓ Problem Management
✗ Advanced Customization

Result:
- Incident = "Incident"
- Service Request = "Request Ticket"
- Problem = "Problem"
- Customization: Business Policies
```

---

## Upgrade Path: Express → Enterprise

### Текущая архитектура (проблемная)

```
Express:
Ticket (WorkOrder в БД) → Enterprise: Work Order
                           + Incident появляется "из ниоткуда"
                           + Service Request появляется "из ниоткуда"

Проблемы:
❌ Терминологический разрыв (Ticket → Work Order)
❌ Новые объекты без data migration
❌ Семантический разрыв (Work Order = supporting task в ITIL терминологии)
```

### Предлагаемая архитектура (элегантная)

```
Express:
Ticket (Incident в БД)

Upgrade (модули меняют конфиг):
Disable: Simplified Terminology Module
Enable:  ITIL Terminology Module
Enable:  Work Order Decomposition Module
Enable:  Service Request Management Module
Enable:  Problem Management Module

Enterprise:
Ticket (Incident в БД) теперь показывается как "Incident"
+ Work Orders становятся visible (для decomposition)
+ Service Request становится visible
+ Problem становится visible

Результат:
✓ NO терминологического разрыва (Incident → Incident)
✓ NO migration данных (Incident был Incident всегда)
✓ Иерархия сохраняется (Incident → Child Incident)
✓ Прозрачное добавление новых объектов
```

**Опциональный Dual Terminology Mode (переходный период):**

```
Enable: ITIL Terminology Module
Enable: Simplified Terminology Module (dual mode)

UI показывает:
"Incident (Ticket)" — оба термина одновременно
"Work Order (Task)" — оба термина одновременно

Через 3-6 месяцев:
Disable: Simplified Terminology Module
→ Остается только "Incident", "Work Order"
```

---

## Реализация: Phase 1 (Первый шаг)

### Цели Phase 1

1. ✓ Ticket = Incident в БД (архитектурное изменение)
2. ✓ Aliasing управляется модулями (не hardcoded)
3. ✓ Express и Enterprise работают на одной кодовой базе
4. ✗ Полная модульная модель (Phase 2, долгосрочно)

### Изменения в Express

**1. Database Schema (NO changes)**

Incidents уже существует в БД. Начинаем использовать вместо WorkOrder для "Tickets".

**2. Data Migration (Express internal)**

```sql
-- Текущие "Tickets" в Express (WorkOrder таблица)
-- Мигрировать в Incidents таблицу

INSERT INTO Incidents (Object_ID, Title, Requester, Assignee, Status, ...)
SELECT Object_ID, Title, Requester, Assignee, Status, ...
FROM WorkOrders
WHERE [Express Tickets conditions]

-- Preserve Object_ID для backward compatibility
-- Update references (Child Tickets, Change Requests, etc.)
```

**3. UI Aliasing Layer**

```python
# Express Configuration
modules = {
    "SimplifiedTerminology": True,
    "ITILTerminology": False,
    "IncidentHierarchy": True,
    "WorkOrderDecomposition": False,
}

if modules["SimplifiedTerminology"]:
    ui_labels["Incident"] = "Ticket"
    ui_labels["Incident.ChildTab"] = "Child Tickets"

if modules["IncidentHierarchy"]:
    enable_feature("Incident.ParentChild")
```

**4. Parent-Child Incidents (новая функциональность)**

```
Incident
├── Parent Incident (FK: ParentIncident_ID)
└── Child Incidents (reverse relationship)

UI:
- Child Tickets tab (показывает Child Incidents)
- Опционально: Child Work Orders (если модуль включен)
```

### Изменения в Enterprise

**1. Module Configuration**

```python
# Enterprise Configuration
modules = {
    "SimplifiedTerminology": False,  # Disabled
    "ITILTerminology": True,         # Enabled
    "IncidentHierarchy": True,       # Enabled
    "WorkOrderDecomposition": True,  # Enabled
    "ProblemManagement": True,
    "ServiceRequestManagement": True,
    "AdvancedCustomization": True,
}

if modules["ITILTerminology"]:
    ui_labels["Incident"] = "Incident"
    ui_labels["WorkOrder"] = "Work Order"
    ui_labels["ServiceRequest"] = "Service Request"
```

**2. Work Order Decomposition**

```
Incident
├── Child Incidents (hierarchy)
└── Work Orders (decomposition tasks)

Service Request
└── Work Orders (fulfillment tasks)

Change Request
└── Work Orders (implementation tasks)
```

---

## Преимущества решения

### 1. Прозрачный Upgrade

| Aspect | Текущая архитектура | Предлагаемая архитектура |
|--------|---------------------|--------------------------|
| **Database Change** | Ticket (WorkOrder) → WorkOrder + NEW Incident | Ticket (Incident) → Incident (same) |
| **Terminology Change** | Ticket → Work Order (jarring) | Ticket → Incident (semantic match) OR dual mode |
| **Data Migration** | Optional (convert to Incidents) | NO migration (already Incidents) |
| **Hierarchy Preservation** | Preserved (WO supports hierarchy) | Preserved (Incident → Child Incident) |
| **User Confusion** | High ("Where are my Tickets?") | Low (optional dual terminology) |

### 2. Модульная гибкость

**Текущая модель:**
```
Express OR Enterprise (binary choice)
```

**Предлагаемая модель:**
```
Core + Module1 + Module2 + ... = Custom Plan

Примеры:
- Express = Core + SimplifiedTerminology
- Service Desk Plus = Core + ITILTerminology + ServiceRequests
- Enterprise = Core + All Modules
- Custom Plan X = Core + Selected Modules
```

### 3. Семантическая корректность

**Ticket:**
- Основной work item (primary)
- Может быть standalone
- Может иметь sub-tasks (children)

**Incident (ITIL):**
- ✓ Primary work item
- ✓ Может быть standalone
- ✓ Может иметь Related Incidents или Work Orders

**Work Order (ITIL концепция):**
- ❌ Supporting work item (не primary)
- ❌ Всегда часть чего-то большего (parent required)
- ❌ Концептуально leaf node в ITIL (хотя платформа технически поддерживает иерархию WO)

**Вывод:** Ticket = Incident (семантически корректно), Ticket ≠ Work Order.

### 4. Incident Hierarchy (новая возможность)

**Express (сейчас):**
```
Ticket (WorkOrder) → Child Ticket (WorkOrder) - работает, но семантически неправильно
```

**Express (предлагается):**
```
Ticket (Incident) → Child Ticket (Incident) - семантически правильно
                  → Child Ticket (WorkOrder) - для decomposition tasks (опционально)
```

**Enterprise (предлагается):**
```
Incident → Child Incident (related incidents, breakdown)
         → Work Order (implementation tasks)
```

**Use Case:**
```
Incident: "Network outage in Building A"
├── Child Incident: "Router failure on Floor 3"
├── Child Incident: "Switch failure on Floor 5"
└── Work Order: "Replace router on Floor 3"
```

---

## Риски и митигация

### Критические риски

| Риск | Описание | Митигация |
|------|----------|-----------|
| **Data Migration Failure** | Express Tickets (WorkOrder) → Incidents migration fails | Comprehensive testing, rollback plan, preserve Object_ID |
| **Backward Compatibility** | APIs, integrations break | Versioned API, "Ticket" endpoint → Incident backend (transparent) |
| **Performance Degradation** | Incident table grows significantly | Indexes, partitioning, performance testing |

### Высокие риски

| Риск | Описание | Митигация |
|------|----------|-----------|
| **User Confusion (terminology)** | Users don't understand Incident vs Ticket | Dual terminology mode, gradual transition, tooltips |
| **Module Complexity** | Too many modules, configuration complexity | Start with simple modules (Phase 1), expand gradually |
| **Child Incidents UX** | Confusing to have both Child Incidents AND Work Orders | Clear UI separation: "Related Incidents" vs "Work Orders" |

### Средние риски

| Риск | Описание | Митигация |
|------|----------|-----------|
| **Reports Break** | Reports reference "Ticket" or "WorkOrder" | Compatibility views, auto-detect and suggest fixes |
| **Custom Code** | Customer scripts reference WorkOrder for Tickets | Migration guide, compatibility shims for 1-2 releases |

---

## Plan реализации

### Phase 1: Incident-Based Core (6-8 months)

**Goal:** Ticket = Incident в БД, aliasing через модули

**1.1 Database Migration (2 months)**
- [ ] Design migration: WorkOrder (Express Tickets) → Incidents
- [ ] Preserve Object_ID for backward compatibility
- [ ] Update foreign keys: Child Tickets, Change Requests
- [ ] Performance testing (10k, 100k, 1M records)

**1.2 Aliasing Layer (2 months)**
- [ ] Extract hardcoded aliasing from code
- [ ] Create Module Configuration system
- [ ] Implement SimplifiedTerminology module
- [ ] Implement ITILTerminology module

**1.3 Incident Hierarchy (1.5 months)**
- [ ] Add Parent Incident FK to Incidents table
- [ ] UI: Child Incidents tab
- [ ] Business logic: prevent circular references
- [ ] Testing

**1.4 Express Migration (1.5 months)**
- [ ] Migrate Express installations: WorkOrder → Incident
- [ ] Enable SimplifiedTerminology module
- [ ] Enable IncidentHierarchy module
- [ ] Validation, testing

**1.5 Enterprise Configuration (1 month)**
- [ ] Enable ITILTerminology module
- [ ] Enable WorkOrderDecomposition module
- [ ] Dual mode support (Incident + Ticket labels)
- [ ] Testing

### Phase 2: Additional Modules (4-6 months)

**Goal:** Problem Management, Service Request Management как модули

**2.1 Problem Management Module**
- [ ] Extract Problem code into module
- [ ] Module configuration (enable/disable)
- [ ] Testing

**2.2 Service Request Management Module**
- [ ] Extract Service Request code into module
- [ ] Module configuration (enable/disable)
- [ ] Testing

**2.3 Advanced Customization Module**
- [ ] Extract Triggers, Custom Forms into module
- [ ] Backward compatibility with Business Policies
- [ ] Testing

### Phase 3: Flexible Plans (2-3 months)

**Goal:** Возможность создавать любые планы

**3.1 Plan Builder UI**
- [ ] Admin UI для выбора модулей
- [ ] Plan templates (Express, Enterprise, Service Desk Plus, Custom)
- [ ] Validation (required modules, dependencies)

**3.2 New Plans**
- [ ] Service Desk Plus plan (промежуточный)
- [ ] ITIL Lite plan
- [ ] Testing

**Total Duration:** ~12-17 months

---

## Открытые вопросы

### По архитектуре

1. **Child Incident Depth** — ограничить глубину иерархии Incident → Child Incident? (Предложение: 3 уровня max)
2. **Work Order vs Incident** — когда использовать Child Incident vs Work Order? (Предложение: Incident = related issue, Work Order = implementation task)
3. **Circular References** — как предотвратить Incident A → Child Incident B → Child Incident A? (Предложение: Graph validation)

### По модулям

4. **Module Dependencies** — какие модули зависят друг от друга? (Предложение: ITILTerminology requires WorkOrderDecomposition)
5. **Module Versioning** — как версионировать модули? (Предложение: SemVer)
6. **Custom Modules** — можно ли создавать свои модули? (Предложение: Phase 4, долгосрочно)

### По миграции

7. **Rollback Window** — сколько времени доступен rollback после migration? (Предложение: 90 дней)
8. **Partial Migration** — можно ли мигрировать только часть Tickets? (Предложение: Нет, all-or-nothing для Express)
9. **API Compatibility** — поддерживать "Ticket" endpoint сколько времени? (Предложение: 2-3 releases, затем deprecate)

### По планам

10. **Plan Customization** — можно ли изменять модули в рамках плана? (Предложение: Да, но через Support)
11. **Plan Migration** — можно ли переключаться между планами? (Предложение: Upgrade yes, downgrade с ограничениями)
12. **Pricing Model** — как модули влияют на цену? (Предложение: Module-based pricing, не plan-based)

---

## Следующие шаги

### Immediate Actions

1. **Stakeholder Review** — презентовать архитектурное видение Product и Engineering
2. **Technical Feasibility** — proof of concept (WorkOrder → Incident migration на test DB)
3. **Customer Validation** — interviews с Express и Enterprise customers

### Design Phase

4. **Module Architecture** — детальная спецификация модульной системы
5. **Database Schema** — финализировать Incident hierarchy schema
6. **Migration Plan** — пошаговый план миграции для Phase 1

### Proof of Concept

7. **Migration POC** — миграция 1000 WorkOrders → Incidents (test environment)
8. **Module POC** — SimplifiedTerminology vs ITILTerminology switching
9. **Performance POC** — Incident hierarchy с 3 levels, 100k records

---

## Заключение

Предлагаемое архитектурное решение решает три фундаментальные проблемы:

### 1. Семантическая корректность
**Проблема:** Ticket ≠ Work Order семантически
**Решение:** Ticket = Incident (primary work item, корректная семантика)

### 2. Прозрачный Upgrade
**Проблема:** Express Ticket → Enterprise Work Order (терминологический разрыв)
**Решение:** Express Ticket (Incident) → Enterprise Incident (нет разрыва, опциональный dual mode)

### 3. Модульная гибкость
**Проблема:** Только 2 плана (Express OR Enterprise), нет промежуточных
**Решение:** Модульная архитектура → любые планы через комбинацию модулей

**Ключевые преимущества:**
- ✓ Zero data migration при upgrade (Incident → Incident)
- ✓ Сохранение иерархии (Incident → Child Incident)
- ✓ Возможность создания Service Desk Plus, ITIL Lite, Custom Plans
- ✓ Управление терминологией независимо от функциональности
- ✓ Соответствие ITIL (Incident как primary, Work Order как supporting)

**Первый шаг (Phase 1):**
- Ticket = Incident в БД
- Aliasing через модули
- Parent-Child Incidents
- Прозрачный upgrade Express → Enterprise

**Долгосрочная цель:**
- Полностью модульная продуктовая модель
- Гибкие планы под любые потребности
- Module-based pricing

---

*Связанные документы: Express Ticket Upgrade Proposal, Express Product Spec, Platform Conceptual Model*

*Ключевые термины: Incident-Based Architecture, Modular Product Model, Configurable Aliasing, Parent-Child Incidents*
