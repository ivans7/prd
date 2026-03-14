# Предложение: Эволюция модели Workspaces

**Дата:** Март 2026
**Статус:** Proposal
**Scope:** Workspace Model — от фильтра видимости к организационному контексту

---

## Structure of This Document

- **Current Product Baseline (As-Is):** current workspace data model, visibility rules, and known limitations
- **Proposed Future-State Changes (To-Be):** target workspace model, security changes, workflow impact, migration, and rollout options

---

## Executive Summary

**Текущая проблема:**
- Workspace — просто поле в объекте (`object.workspace`), работающее как фильтр видимости
- Нет связи workspace с бизнес-логикой, структурой данных или процессами
- Слишком мелкая гранулярность — каждый объект в своём workspace, что усложняет права
- Нет глобального слоя данных (справочники, контакты дублируются по workspaces)

**Предлагаемое решение:**
Перейти от модели **workspace = поле в объекте** к модели **workspace = организационный контекст**, где workspace определяет зону ответственности команды, а не принадлежность отдельной записи.

**Ключевые преимущества:**
- Workspace как логическая единица с собственными очередями, агентами и автоматизациями
- Глобальный слой данных для общих объектов (контакты, организации, справочники)
- Чёткое разделение: workspace-scoped объекты vs global объекты
- Соответствие конкурентным решениям (Freshservice, ServiceNow)

---

## Анализ конкурентов

### Freshservice Workspaces

**Архитектура:**
Workspaces в Freshservice — это что-то вроде soft tenants. Не все классы объектов попадают в workspace; часть остаётся глобальной.

**Workspace-scoped объекты** (у каждого workspace свои):
- Tickets
- Service Catalog Items
- KB Articles
- Assets
- Automations (Workflow Automator, Business Rules)

**Global объекты** (общие для всех workspaces):
- Contacts / Requesters
- Agents (могут быть в нескольких workspaces)
- Departments
- Locations

**Каждый workspace имеет:**
- Свою очередь тикетов/заявок
- Свою группу агентов
- Свои настройки автоматизаций и SLA
- Свои категории услуг и каталог

**Главный принцип:** Workspace — это зона ответственности команды (IT, HR, Facilities, Finance), а не атрибут каждой записи.

### ServiceNow Domains

**Архитектура:**
ServiceNow использует Domain Separation — более жёсткую модель multi-tenancy.

- Domains образуют иерархию (Global → Region → Site)
- Объекты наследуют видимость по иерархии (global виден всем, child — только внутри domain)
- Используется для MSP/крупных организаций

**Отличие от Freshservice:** ServiceNow Domains — полноценная multi-tenancy с иерархией; Freshservice Workspaces — более лёгкая модель зон ответственности.

---

## Текущая ситуация (As-Is)

### Модель данных

```
Workspace — значение из классификатора
Каждый объект имеет поле object.workspace
```

**Классификатор Workspaces:**

| Workspace | Назначение |
|-----------|-----------|
| IT | Дефолтный workspace (назначается при создании) |
| HR | Human Resources |
| Facilities | Управление объектами/помещениями |
| Shared | Общие объекты (персоны, организации, локации — через QS WF) |
| ... | Другие значения, определяемые администратором |

### Контроль видимости

```
Security Role
├── Allowed Workspaces: [IT, HR, ...]
├── Allowed Organizations: [Org A, Org B, ...]
└── Видимость = пересечение обоих ограничений
```

**Правило:** Technician видит объект только если:
- `object.workspace` входит в Allowed Workspaces роли, **И**
- `object.organization` входит в Allowed Organizations роли

### Workflow-зависимости

Минимальны:
- При создании объекта — дефолтный workspace `IT`
- В QS WF для персон, организаций, локаций — workspace `Shared`
- Администраторы могут менять workspace вручную

### Отображение данных

- По умолчанию данные показываются **без фильтров по workspace**
- Фильтрация по workspace — только если специально сконфигурировать

### Визуализация текущей модели

```
┌─────────────────────────────────────────────────────┐
│                    Platform                          │
│                                                     │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐         │
│  │ Ticket   │  │ Computer │  │ Person   │         │
│  │ ws=IT    │  │ ws=IT    │  │ ws=Shared│         │
│  └──────────┘  └──────────┘  └──────────┘         │
│                                                     │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐         │
│  │ Ticket   │  │ Hardware │  │ Org      │         │
│  │ ws=HR    │  │ ws=Fac   │  │ ws=Shared│         │
│  └──────────┘  └──────────┘  └──────────┘         │
│                                                     │
│  Security Role → фильтрует по workspace поля       │
└─────────────────────────────────────────────────────┘

Workspace — атрибут объекта, не организационная единица.
Все объекты живут в одном пространстве, различаясь только значением поля.
```

---

## Проблемы текущей модели

### 1. Workspace как технический фильтр, а не организационная единица

Workspace не несёт организационного смысла. Это просто поле для фильтрации — нет иерархии, нет смысловых объединений объектов. Нельзя ответить на вопрос "что входит в workspace HR?" без запроса к базе данных.

### 2. Мелкая гранулярность

Каждый объект индивидуально помечен workspace. Это ведёт к:
- Ручному управлению workspace на уровне отдельных записей
- Ошибкам при назначении (объект попал не в тот workspace)
- Сложности в настройке прав (нужно учитывать пересечение workspace × organization)

### 3. Нет глобального слоя данных

Объекты, которые по своей природе общие (контакты, организации, локации), вынуждены иметь workspace `Shared`. Это workaround, а не архитектурное решение. Нет чёткого разделения: какие объекты workspace-scoped, а какие global.

### 4. Нет связи workspace с процессами

Workspace не определяет:
- Какие очереди тикетов существуют
- Какие агенты обслуживают workspace
- Какие автоматизации применяются
- Какие сервисы (и, следовательно, SLA) действуют

Всё это конфигурируется отдельно, без привязки к workspace. SLA в текущей модели привязан к Service, а Service не привязан к workspace — связь Workspace → Service → SLA отсутствует.

### 5. Дефолтный workspace при создании

Все объекты получают `IT` при создании — независимо от контекста. Администратор должен вручную переназначить workspace. Нет автоматической маршрутизации по workspace на основе контекста (кто создал, для какого отдела, через какой каталог).

---

## Proposed Future-State Changes (To-Be)

### Предлагаемая модель

### Ключевой принцип

**Workspace — это логическая зона ответственности команды, а не атрибут у каждой записи.**

Workspace определяет:
- Какие объекты в нём живут (scope)
- Кто с ними работает (agents/groups)
- По каким правилам они обрабатываются (automations, SLA)
- Какие услуги доступны (service catalog)

### Разделение объектов: Workspace-scoped vs Global

| Категория | Объекты | Принадлежность |
|-----------|---------|----------------|
| **Workspace-scoped** | Tickets (Incidents, Work Orders, Service Requests, etc.) | Принадлежат одному workspace |
| **Workspace-scoped** | Services | Определяются в рамках workspace. SLA привязан к Service (не к workspace напрямую) |
| **Workspace-scoped** | Service Catalog Items | Определяются в рамках workspace |
| **Workspace-scoped** | Knowledge Articles | Могут быть workspace-specific |
| **Workspace-scoped** | Automations (Triggers, Business Policies) | Работают в контексте workspace |
| **Global** | Persons (Contacts, Requesters) | Доступны для ссылки из всех workspaces, доступ ограничен Organization ACL |
| **Global** | Organizations | Доступны для ссылки из всех workspaces, доступ ограничен Organization ACL |
| **Global** | Locations | Доступны для ссылки из всех workspaces, доступ ограничен Organization ACL |
| **Global** | SLA | Определения SLA — global (конфигурация). Привязка к тикету — через workspace-scoped Service |
| **Configurable** | Assets (IT Assets, Computers, etc.) | Могут быть привязаны к workspace или быть global |

### Контракт доступа к Global Objects

**"Global" означает "не принадлежит конкретному workspace", но НЕ означает "доступен без ограничений".**

Global objects по-прежнему защищены Organization ACL из security role агента. Workspace membership не влияет на видимость global objects — влияет только Organization scope роли.

**Проблема:** В предлагаемой модели роли привязаны к workspaces, а агент может быть членом нескольких workspaces с разными ролями. Для global objects (которые не принадлежат workspace) нужно определить: какой набор Organization restrictions применяется?

**Решение: Effective Organization Scope = UNION по всем workspace-ролям агента.**

```
Агент: member of IT (Role A) и HR (Role B)
  Role A: Allowed Organizations = [Org X, Org Y]
  Role B: Allowed Organizations = [Org Y, Org Z]

Effective Org Scope для workspace-scoped objects:
  Объект в IT workspace → применяется Role A → Org X, Org Y
  Объект в HR workspace → применяется Role B → Org Y, Org Z

Effective Org Scope для global objects:
  UNION(Role A, Role B) → Org X, Org Y, Org Z
  Агент видит Person из Org X, Org Y, и Org Z
```

**Обоснование UNION (а не INTERSECTION или per-workspace):**
- INTERSECTION слишком рестриктивно: агент в IT+HR видел бы только Org Y — теряя доступ к Org X (нужен для IT) и Org Z (нужен для HR)
- Per-workspace невозможно для global objects — они не принадлежат workspace, нет контекста для выбора роли
- UNION = "агент видит все организации, к которым имеет доступ хотя бы в одном workspace". Это соответствует текущему поведению (Allowed Organizations из role — единый список)

**Формализованный контракт доступа:**

```
Доступ к объекту:

Workspace-scoped object (Ticket, Catalog Item, etc.):
  Agent видит объект, ЕСЛИ:
    agent.workspace_membership INCLUDES object.workspace
    AND agent.role_in(object.workspace).allowed_organizations INCLUDES object.organization
  Примечание: используется роль агента В КОНКРЕТНОМ workspace объекта.

Global object (Person, Org, Location):
  Agent видит объект, ЕСЛИ:
    UNION(agent.role_in(ws).allowed_organizations FOR ws IN agent.workspaces)
      INCLUDES object.organization
  Примечание: используется ОБЪЕДИНЕНИЕ organization scope по ВСЕМ workspace-ролям агента.
```

**Для чувствительных доменов (HR, Finance):** Если организация требует изоляцию персональных данных по workspace (например, HR-агенты видят только HR-related contacts), это реализуется через:
- Organization-based ACL (уже существует)
- Опциональный Sensitive Data Flag на Person/Organization, ограничивающий доступ до workspace-admin level
- Не через workspace scope — это сломало бы cross-workspace references (тикет в IT на сотрудника из HR)

**Принцип:** Workspace = scope для рабочих объектов (тикеты, каталог, автоматизации). Organization ACL = scope для данных о людях и организациях. Эти два механизма дополняют друг друга, не заменяют.

### Визуализация предлагаемой модели

```
┌─────────────────────────────────────────────────────────────────┐
│                        Platform (Global Layer)                  │
│                                                                 │
│  ┌──────────────────────────────────────────────────────┐      │
│  │ Global Objects: Persons, Organizations, Locations     │      │
│  └──────────────────────────────────────────────────────┘      │
│           ↓                    ↓                   ↓            │
│  ┌────────────────┐  ┌────────────────┐  ┌────────────────┐   │
│  │  Workspace: IT │  │  Workspace: HR │  │ Workspace: Fac │   │
│  │                │  │                │  │                │   │
│  │  Tickets       │  │  Tickets       │  │  Tickets       │   │
│  │  Services      │  │  Services      │  │  Services      │   │
│  │  Catalog       │  │  Catalog       │  │  Catalog       │   │
│  │  KB Articles   │  │  KB Articles   │  │  KB Articles   │   │
│  │  Automations   │  │  Automations   │  │  Automations   │   │
│  │  Agent Groups  │  │  Agent Groups  │  │  Agent Groups  │   │
│  └────────────────┘  └────────────────┘  └────────────────┘   │
│                                                                 │
│  SLA → привязан к Service (не к workspace напрямую)            │
│                                                                 │
│  Assets → привязка к workspace (опционально)                   │
└─────────────────────────────────────────────────────────────────┘
```

### Workspace как первоклассный объект

```
Workspace (новый объект)
├── Name: "IT", "HR", "Facilities", ...
├── Description
├── Agent Groups: [группы, обслуживающие workspace]
├── Default Assignee/Group
├── Services: [сервисы workspace, каждый со своим SLA]
├── Service Catalog: [каталог услуг workspace]
├── Automations: [триггеры и политики workspace]
└── Settings: timezone, business hours, email template, ...
```

**Примечание:** SLA не является прямым свойством workspace. SLA привязан к Service (как в текущей модели). Workspace scope'ит Services, а SLA следует за Service. Подробнее — см. "SLA и Service в контексте workspace".

**Workspace перестаёт быть значением классификатора и становится бизнес-объектом** с собственными свойствами, связями и конфигурацией.

---

## Изменения в модели безопасности

### Текущая модель

```
Security Role
├── Allowed Workspaces: [IT, HR]        ← значения из классификатора
├── Allowed Organizations: [Org A]
└── Видимость = Workspace ∩ Organization
```

### Предлагаемая модель

```
Agent ←→ Workspace (many-to-many)
├── Agent может быть членом нескольких workspaces
├── Membership определяет:
│   ├── Видимость workspace-scoped объектов
│   ├── Возможность назначения на тикеты
│   └── Доступ к конфигурации workspace
│
Security Role (внутри workspace)
├── Определяет уровень доступа: read, create, update, admin
└── Allowed Organizations: [Org A]  ← остаётся как есть
```

**Ключевое изменение:** Workspace membership заменяет Allowed Workspaces в роли. Пересечение с Organization остаётся.

### Уровни администрирования

| Роль | Scope | Полномочия |
|------|-------|-----------|
| **Global Admin** | Все workspaces + global objects | Создание/удаление workspaces, управление membership, global automations, system settings |
| **Workspace Admin** | Один или несколько workspaces | Настройка workspace: services, automations, catalog, agent groups. НЕ может создавать workspaces или управлять global settings |
| **Workspace Agent** | Один или несколько workspaces | Работа с объектами в workspace: создание, обработка, закрытие тикетов |
| **Workspace Viewer** | Один или несколько workspaces | Read-only доступ к объектам workspace |

**Workspace Admin НЕ может:**
- Создавать/удалять workspaces
- Менять membership других агентов в чужих workspaces
- Редактировать global automations
- Менять system-level settings

**Workspace Admin МОЖЕТ:**
- Управлять workspace-scoped services, automations, catalog
- Назначать/убирать агентов в своём workspace (при наличии права)
- Настраивать workspace settings (business hours, email templates)

---

## Изменения в Workflow

### Маршрутизация по workspace

**Текущее поведение:**
- Все объекты получают workspace `IT`
- Администратор перемещает вручную

**Предлагаемое поведение:**

Workspace определяется при создании детерминированно. Источники workspace (в порядке приоритета):

| Приоритет | Источник | Пример | Когда применяется |
|-----------|----------|--------|-------------------|
| 1 (высший) | **Явное указание** | API: `workspace_id=3`, Agent выбирает в UI | Всегда, если указано |
| 2 | **Service Catalog Item** | Каталог "Отпуск" → HR workspace | Если тикет создан через каталог |
| 3 | **Email alias** | hr@company.com → HR | Если тикет создан через email |
| 4 | **Текущий workspace агента** | Agent работает в IT → тикет в IT | Если агент создаёт тикет вручную без явного выбора |
| 5 (низший) | **Default workspace** | Системный default = IT | Fallback, если ни один источник не сработал |

**Правило:** Применяется первый сработавший источник сверху вниз. Конфликтов нет — приоритет строгий.

**Валидация (fail-closed для explicit, fail-open для implicit):**

Поведение при невалидном workspace зависит от источника:

| Источник | Ошибка | Поведение | Обоснование |
|----------|--------|-----------|-------------|
| **Приоритет 1: Явное указание (API/UI)** | workspace не существует | **Reject: HTTP 400 / UI error.** Объект НЕ создаётся. | Caller явно указал workspace — ошибка в вызове, не в системе. Fail-open здесь = silent misroute. |
| **Приоритет 1: Явное указание (API/UI)** | агент не имеет membership | **Reject: HTTP 403 / UI error.** Объект НЕ создаётся. | Агент пытается создать объект в чужом workspace — нарушение access control. |
| **Приоритет 2: Service Catalog Item** | каталог ссылается на несуществующий workspace | **Skip source, переход к приоритету 3.** Warning в лог + admin notification. | Ошибка конфигурации каталога — не повод блокировать создание тикета от end-user. |
| **Приоритет 3: Email alias** | alias не маппится на workspace | **Skip source, переход к приоритету 4.** | Нормальная ситуация: не все email aliases имеют workspace mapping. |
| **Приоритет 4: Текущий workspace агента** | агент не в workspace (невозможно, если UI корректен) | **Skip source, переход к приоритету 5.** | Defensive fallback. |
| **Приоритет 5: Default workspace** | default workspace не настроен | **Reject: объект НЕ создаётся.** Error в лог + admin notification. | Системная ошибка конфигурации — создавать объект без workspace нельзя. |

**Принцип:** Explicit input = fail-closed (caller's error). Implicit/derived input = skip to next source (system resolves gracefully). No workspace at all = fail-closed (system misconfiguration).

### Автоматизации в контексте workspace

**Два уровня scope для автоматизаций:**

```
Trigger/Business Policy
├── Scope: workspace
│   ├── Привязан к конкретному workspace
│   ├── Выполняется ТОЛЬКО для объектов этого workspace
│   └── Пример: Escalation rule в IT workspace ≠ Escalation rule в HR workspace
│
└── Scope: global
    ├── НЕ привязан к workspace
    ├── Выполняется для объектов ВСЕХ workspaces
    └── Пример: "Все тикеты P1 → уведомление CTO" (cross-workspace)
```

**Правило выполнения:** Для workspace-scoped объекта (например, Ticket) триггеры выполняются в таком порядке:
1. Workspace-specific triggers (если объект в этом workspace и условие совпадает)
2. Global triggers (если условие совпадает)

**Обоснование порядка "workspace first":** Workspace triggers более специфичны. Выполняя их первыми, мы позволяем workspace-specific логике определить состояние объекта до того, как global triggers его обработают. Это предотвращает проблему irreversible side effects (уведомления, webhooks, escalations), которые global trigger мог бы отправить на основе состояния, которое workspace trigger потом изменит.

**Разрешение конфликтов по типу действия:**

| Тип действия | Конфликт | Поведение |
|-------------|----------|-----------|
| **Field update** (Status, Priority, Assignee) | Global и workspace trigger меняют одно поле | Workspace trigger побеждает (выполнен первым, global trigger видит уже изменённое значение и может не сработать по условию) |
| **Notification** | Оба шлют email | Оба выполняются — notifications не конфликтуют, они аддитивны. Дедупликация: если один и тот же recipient+template — отправляется один раз |
| **Webhook** | Оба вызывают endpoint | Оба выполняются — webhooks аддитивны. Если один endpoint — дедупликация по URL+payload hash |
| **Escalation** | Оба эскалируют | Workspace trigger задаёт escalation path; global trigger НЕ переопределяет уже заданную escalation (idempotent: если escalation уже в процессе — skip) |

**Opt-out:** Global trigger может иметь флаг `skip_if_workspace_handled = true`. Если workspace trigger сработал на том же объекте в том же event cycle — global trigger с этим флагом пропускается. Это позволяет админу сказать: "используй global logic только если workspace не определил свою".

**Стратегия миграции автоматизаций:**

| Phase | Что происходит | Эффект |
|-------|---------------|--------|
| **При upgrade** | Все существующие триггеры получают scope = `global` | Поведение идентично текущему: триггер работает на всех объектах как и раньше |
| **После upgrade** | Админ может привязать триггер к workspace | Триггер сужает scope, перестаёт работать на объектах других workspaces |
| **Новые триггеры** | Создаются с явным scope: global или workspace | Админ выбирает при создании |

**Важно:** Миграция НЕ расширяет выполнение. Текущие триггеры уже работают на всех объектах (workspace поле не участвует в их логике). Маркировка `global` при миграции сохраняет это поведение. Сужение scope до конкретного workspace — это осознанное действие админа после миграции.

### SLA и Service в контексте workspace

**Текущая модель SLA (as-is):**

```
Ticket (Incident / Service Request / Change Request)
  ↓ поле "Service"
Service (IT capability, определяет что ИТ предоставляет)
  ↓ key relationship
SLA (response/resolution time targets)
  ↓ referenced by
Business Policy: Escalation (проверяет SLA targets для эскалации)
```

SLA привязан к **Service**, не к workspace и не к тикету напрямую. Цепочка: Ticket → Service → SLA → Escalation.

В Express: Service, SLA, Service Catalog Item — все hidden. Due Date управляется через Business Policy: Prioritization (по приоритету).

**Влияние workspace на SLA — три варианта:**

#### Вариант SLA-1: Service = workspace-scoped → SLA следует автоматически (рекомендуется)

```
Workspace: IT
  └── Service: "Email Service"
        └── SLA: "IT Email SLA — 1h response, 8h resolution"

Workspace: HR
  └── Service: "Onboarding"
        └── SLA: "HR Onboarding SLA — 4h response, 24h resolution"
```

Service становится workspace-scoped. SLA остаётся привязанным к Service как сейчас. Ничего не ломается в модели. Тикет в IT workspace → Service из IT workspace → SLA этого Service.

| Плюсы | Минусы |
|-------|--------|
| Не ломает текущую модель Service → SLA | Один Service не может обслуживать несколько workspaces с разными SLA |
| Workspace scope Service = scope его SLA автоматически | Shared services (например, "Hardware Repair" в IT и Facilities) требуют дублирования Service per workspace |
| Простая реализация | |

#### Вариант SLA-2: SLA напрямую к workspace (ломает текущую модель)

```
Workspace: IT → Default SLA: "IT SLA — 1h/8h"
Workspace: HR → Default SLA: "HR SLA — 4h/24h"

Ticket → Service → SLA (по Service, как сейчас)
Ticket → Workspace → SLA (новая связь, по workspace)

Конфликт: если Service SLA = 1h, а Workspace SLA = 4h — какой применять?
```

| Плюсы | Минусы |
|-------|--------|
| Workspace определяет SLA baseline | Конфликт двух источников SLA |
| Не требует дублирования Services | Нужна логика приоритетов (Service SLA > Workspace SLA? или наоборот?) |
| | Ломает чистую модель Service → SLA |

#### Вариант SLA-3: Service + Workspace = SLA (промежуточная таблица)

```
Service: "Hardware Repair" (global или shared service)
  ├── В IT workspace: SLA = 4h response, 8h resolution
  └── В Facilities workspace: SLA = 24h response, 48h resolution

Связь: Service × Workspace → SLA
```

| Плюсы | Минусы |
|-------|--------|
| Один Service, разные SLA по workspace | Сложнее модель данных (промежуточная таблица) |
| Не нужно дублировать Services | Нужна fallback-логика (что если для пары Service+Workspace нет SLA?) |
| Максимальная гибкость | Сложнее конфигурация |

**Рекомендация:** Вариант SLA-1 (Service = workspace-scoped). Причины:
- Не ломает текущую модель Service → SLA
- В Freshservice Service Catalog Items — workspace-scoped, что эквивалентно
- Дублирование shared services (Hardware Repair в IT и Facilities) — приемлемо, потому что у них разные SLA, разные agent groups, разные workflows. Это не техническое дублирование — это разные сервисы в разных контекстах
- Вариант SLA-3 можно добавить позже как оптимизацию, если дублирование станет проблемой

---

## Что происходит с Assets

Assets — отдельный вопрос, требующий гибкости:

### Вариант A: Assets = Global

- Все assets доступны из всех workspaces
- Workspace не влияет на видимость assets
- Тикет в workspace HR может ссылаться на любой asset

### Вариант B: Assets = Workspace-scoped (опционально)

- Asset может быть привязан к workspace
- Пример: принтеры в Facilities, компьютеры в IT
- Тикет может ссылаться на asset из другого workspace (cross-reference)

### Вариант C: Гибридный (рекомендуется)

- По умолчанию assets — global
- Опционально можно привязать asset к workspace
- Привязка влияет на дефолтную маршрутизацию (тикет по asset в IT workspace → IT queue)
- Привязка не ограничивает видимость (asset виден из всех workspaces)

---

## Миграция

### Принципы миграции

1. **Backward compatibility:** Существующие инсталляции продолжают работать без изменений до явного opt-in
2. **Dual-write период:** Поле `object.workspace` и FK на Workspace object сосуществуют в течение переходного периода
3. **Маппинг данных:** Существующие значения `object.workspace` конвертируются в workspace objects при opt-in

### Compatibility Mode vs New Mode

```
Version N (текущая):
  object.workspace = "IT"                    ← строка из классификатора
  Security Role → Allowed Workspaces         ← список строк

Version N+1 (переходная, dual-write):
  object.workspace = "IT"                    ← deprecated, read-only computed field
  object.workspace_id = FK → Workspace(3)    ← новый FK
  Security Role → Allowed Workspaces         ← продолжает работать (compatibility)
  Agent → Workspace Membership               ← новый механизм (opt-in)

  Dual-write: запись в workspace_id автоматически обновляет workspace строку
  Dual-read: код, читающий object.workspace, получает значение из FK

Version N+2 (new mode only):
  object.workspace_id = FK → Workspace(3)    ← единственный механизм
  object.workspace                           ← удалён
  Agent → Workspace Membership               ← единственный механизм
  Security Role → Allowed Workspaces         ← удалён
```

**Переходный период (Version N+1):** 1 major release (примерно 12 месяцев). В этот период:
- **Dual-read:** Reports, API, интеграции, читающие `object.workspace`, получают computed значение из FK
- **Dual-write (write-shim):** API/интеграции, пишущие `object.workspace = "HR"`, прозрачно конвертируются в `workspace_id = FK(HR)`
- Новый код использует `workspace_id` FK напрямую
- Deprecation warnings в логах при обращении к старому полю (и read, и write)

**Write-shim поведение (Version N+1):**

```
API/Integration пишет object.workspace = "HR":
  1. Система ищет Workspace object с name = "HR"
  2. Если найден → устанавливает workspace_id = FK(HR). Success + deprecation warning.
  3. Если НЕ найден → HTTP 400 "Workspace 'HR' not found. Use workspace_id parameter."
     (fail-closed: не создаём объект с невалидным workspace)

API/Integration пишет workspace_id = 5:
  1. Валидация FK → Success (стандартное поведение). Без deprecation warning.
```

**Важно:** Write-shim работает только для строковых значений, существующих как Workspace objects. Если клиент использовал кастомное значение workspace, не мигрированное в Workspace object — write-shim вернёт ошибку. Это выявляется на этапе dry-run (acceptance check: все уникальные значения `object.workspace` должны иметь соответствующий Workspace object).

### Шаги миграции

**Phase 0: Подготовка (Version N+1)**
- Создать Workspace как бизнес-объект
- Мигрировать значения классификатора в workspace objects
- Добавить `workspace_id` FK ко всем объектам (dual-write с `object.workspace`)
- Compatibility mode: оба механизма работают параллельно

**Phase 1: Workspace membership (Version N+1, opt-in)**
- Workspace membership для агентов — новый механизм (opt-in per installation)
- При opt-in: Allowed Workspaces из роли автоматически конвертируются в membership
- Без opt-in: Allowed Workspaces продолжает работать как раньше

**Phase 2: Workspace-scoped объекты (Version N+1, opt-in)**
- Tickets и automations получают workspace scope (для installations с opt-in)
- Global objects (Persons, Organizations): поле workspace_id = NULL (не привязаны к workspace)
- `object.workspace` для global objects возвращает "Shared" через dual-read (backward compat)

**Phase 3: Workspace configuration (Version N+2)**
- Services (→ SLA), Service Catalog, KB — привязка к workspace
- Workspace settings (business hours, email templates)
- Удаление deprecated `object.workspace` поля и Allowed Workspaces из роли

### Доступность routing sources по phases

Routing priority table ссылается на источники, которые становятся доступны в разных phases. Поведение routing зависит от текущей phase:

| Routing Source | Phase 0-1 (N+1) | Phase 2 (N+1) | Phase 3 (N+2) |
|----------------|-----------------|----------------|----------------|
| **1. Явное указание (API/UI)** | Доступен | Доступен | Доступен |
| **2. Service Catalog Item** | **Недоступен** — каталог ещё не привязан к workspace | **Недоступен** | Доступен |
| **3. Email alias** | Доступен (маппинг alias → workspace настраивается в Phase 0) | Доступен | Доступен |
| **4. Текущий workspace агента** | Доступен (workspace selector в UI появляется в Phase 1) | Доступен | Доступен |
| **5. Default workspace** | Доступен | Доступен | Доступен |

**Следствие:** В Phase 0-2 routing по каталогу (приоритет 2) пропускается — система переходит к приоритету 3 (email) или 4 (agent workspace). Это означает, что тикеты, созданные через каталог, попадут в workspace агента или default. После Phase 3 (каталог привязан к workspace) — routing автоматически начнёт использовать приоритет 2.

**Это не regression:** В текущей модели каталог не определяет workspace вообще. Phase 0-2 сохраняет это поведение. Phase 3 улучшает его.

### Маппинг текущих данных (при opt-in)

| Текущее состояние | Phase 0 (dual-write) | Phase 3 (new mode) |
|-------------------|----------------------|---------------------|
| `object.workspace = "IT"` | `workspace_id = FK(IT)`, `workspace = "IT"` (computed) | `workspace_id = FK(IT)` |
| `object.workspace = "Shared"` (Person) | `workspace_id = NULL`, `workspace = "Shared"` (computed) | `workspace_id = NULL` (global object) |
| Role: Allowed Workspaces = [IT, HR] | Role работает + Agent Membership = [IT, HR] | Agent Membership = [IT, HR] |
| Дефолтный workspace `IT` | Без изменений | Workspace по контексту создания |

---

## Риски

### Критические

| Риск | Описание | Митигация |
|------|----------|-----------|
| **Ломаются security roles** | Переход от Allowed Workspaces к membership | Dual-write период (Version N+1): оба механизма работают; opt-in переключение; acceptance checks (Permission Matrix Parity) |
| **Потеря видимости объектов** | Неправильный маппинг workspace → workspace membership | Dry-run на копии production DB + Permission Matrix Parity check до production миграции |
| **Data leak через global objects** | Global objects (Persons, Organizations) видны без workspace scope | Organization ACL продолжает действовать; global ≠ unrestricted; Sensitive Data Flag для regulated domains |

### Высокие

| Риск | Описание | Митигация |
|------|----------|-----------|
| **Cross-workspace data exposure** | Тикет в IT ссылается на asset в HR, раскрывая контекст | Cross-reference разрешён, но содержимое связанного объекта показывается с учётом Organization ACL agent'а |
| **Automations scope confusion** | Админ не понимает, global trigger или workspace trigger сработал | Audit log: каждое срабатывание показывает trigger scope + workspace объекта. Миграция: все текущие → global (без изменения поведения) |
| **Reports ломаются** | Reports с фильтром `workspace = "IT"` | Compatibility views + dual-read в Version N+1; Report Parity acceptance check |
| **Backward compat breakage** | API/интеграции используют `object.workspace` строку | Dual-write: computed field `object.workspace` возвращает Workspace.name по FK; deprecation warnings; удаление в Version N+2 |

### Средние

| Риск | Описание | Митигация |
|------|----------|-----------|
| **Сложность конфигурации** | Workspace добавляет ещё один уровень настройки | Хорошие defaults, wizard для настройки |
| **Performance** | Дополнительный JOIN при проверке workspace scope | Indexing, caching membership |
| **User confusion** | Агенты не понимают в каком workspace работают | UI: workspace selector, индикатор текущего workspace |
| **Admin scope ambiguity** | Неясно кто может что менять (Global Admin vs Workspace Admin) | Чёткое разделение полномочий (см. "Уровни администрирования"), enforce при создании роли |

---

## Критерии приёмки миграции (Acceptance Checks)

Миграция считается успешной, если ВСЕ следующие проверки пройдены:

### Permission Matrix Parity

| Проверка | Метод | Критерий |
|----------|-------|----------|
| **Видимость объектов** | Для каждого агента: список видимых объектов до и после миграции | 100% совпадение |
| **Создание объектов** | Для каждой роли: список классов объектов, которые можно создать | 100% совпадение |
| **Редактирование** | Для каждой роли: какие поля каких объектов можно редактировать | 100% совпадение |
| **Cross-reference** | Тикет может ссылаться на те же объекты, что и до миграции | Нет regression |

### Automation Parity

| Проверка | Метод | Критерий |
|----------|-------|----------|
| **Trigger execution set** | Для каждого триггера: на каких объектах он выполняется до и после | 100% совпадение (все мигрированы как global) |
| **Business Policy results** | Routing, escalation, prioritization для test dataset | Идентичные результаты |
| **Notification delivery** | Те же уведомления тем же получателям | 100% совпадение |

### Report Parity

| Проверка | Метод | Критерий |
|----------|-------|----------|
| **Существующие reports** | Все saved reports возвращают те же данные | 100% совпадение (через compatibility views) |
| **Workspace filter** | Reports с фильтром `workspace = "IT"` | Работают через dual-read |

### Data Integrity

| Проверка | Метод | Критерий |
|----------|-------|----------|
| **Record count** | Количество объектов по классам до и после | 100% совпадение |
| **FK integrity** | Все workspace_id FK ссылаются на существующие Workspace objects | 0 orphaned references |
| **Dual-write consistency** | `object.workspace` (computed) совпадает с `Workspace.name` по FK | 100% совпадение |

**Процедура:** Dry-run миграция на копии production DB → прогон acceptance checks → отчёт о расхождениях → fix → повтор до 100% parity → production миграция.

---

## Сравнение: As-Is vs To-Be

| Аспект | As-Is | To-Be |
|--------|-------|-------|
| **Workspace сущность** | Значение классификатора | Бизнес-объект первого класса |
| **Гранулярность** | Поле в каждом объекте | Организационный контекст |
| **Видимость** | Фильтр по полю в security role | Workspace membership |
| **Общие данные** | Workaround: `workspace = Shared` | Архитектурный Global layer |
| **Автоматизации** | Не связаны с workspace | Workspace-scoped |
| **SLA** | Привязан к Service (не к workspace) | Service = workspace-scoped → SLA следует за Service |
| **Service Catalog** | Единый для всех | Workspace-specific |
| **Создание объектов** | Дефолт `IT`, ручное переназначение | Контекстная маршрутизация |
| **Assets** | Workspace как поле | Global или опционально workspace-scoped |

---

## Открытые вопросы

### По архитектуре

1. **Workspace hierarchy** — нужна ли вложенность workspaces (IT → IT Infrastructure, IT Applications)? Или плоский список достаточен?
2. **Cross-workspace tickets** — может ли тикет принадлежать нескольким workspaces одновременно? Или только одному с возможностью transfer?
3. **Workspace transfer** — как обрабатывать перемещение тикета между workspaces? Reset assignee? Сохранение истории?

### По данным

4. **Assets scope** — какой вариант выбрать: Global, Workspace-scoped, или Гибридный?
5. **Knowledge Base** — workspace-scoped KB или global с workspace tags?
6. **Custom Fields** — общие для всех workspaces или workspace-specific?

### По конфигурации

7. **Workspace defaults** — какие workspaces создавать out-of-box? Только IT? IT + HR + Facilities?
8. **Workflow per workspace** — разные workflows для одного класса объектов в разных workspaces? Или единый workflow с workspace-specific branches?

### По миграции

9. **Express Edition** — как workspace model работает в Express? Один workspace по умолчанию?
10. **Online vs offline migration** — миграция Phase 0 (dual-write FK) требует maintenance window или может быть online? Объём данных у типичного клиента влияет на решение.

### Resolved (зафиксировано в документе)

| # | Вопрос | Решение |
|---|--------|---------|
| — | Admin scope (Global vs Workspace Admin) | Определено в секции "Уровни администрирования": 4 уровня (Global Admin, Workspace Admin, Agent, Viewer) |
| — | Обратная совместимость `object.workspace` (read) | Dual-read в Version N+1: computed field из FK. Удаление в Version N+2 |
| — | Обратная совместимость `object.workspace` (write) | Write-shim в Version N+1: запись строки конвертируется в FK lookup. Fail-closed если workspace не найден |
| — | Feature flag (opt-in или для всех) | Opt-in per installation в Version N+1. Обязательно в Version N+2 |
| — | Routing determinism | Строгий приоритет источников + fail-closed для explicit, skip-to-next для implicit |
| — | Routing fail-open vs fail-closed | Explicit workspace (API/UI) = fail-closed (400/403). Implicit = skip to next source. No workspace at all = fail-closed |
| — | Global objects access control | Organization ACL действует; UNION семантика по всем workspace-ролям агента |
| — | Effective Org Scope для multi-workspace agents | UNION: агент видит организации из всех своих workspace-ролей (см. "Контракт доступа") |
| — | Automation migration scope | Все текущие → global scope, без изменения поведения |
| — | Automation conflict resolution | Workspace triggers first, global second. Notifications/webhooks дедуплицируются. `skip_if_workspace_handled` opt-out |
| — | Catalog routing в Phase 1/2 | Недоступен — каталог привязывается к workspace в Phase 3. Phase 1/2 fallback на agent workspace / default |

---

## Следующие шаги

### Immediate Actions

1. **Stakeholder Review** — представить предложение Product и Engineering
2. **Competitor Deep Dive** — детальный анализ Freshservice Workspaces (demo, documentation)
3. **Customer Research** — как клиенты используют текущую модель workspaces (patterns, pain points)

### Design Phase

4. **Data Model** — детальная спецификация Workspace как бизнес-объекта (поля, связи, constraints)
5. **Security Model** — детальная спецификация workspace membership и пересечения с organization
6. **Migration Plan** — пошаговый план миграции для каждой phase

### Proof of Concept

7. **Workspace Object POC** — создать Workspace как объект, проверить основные операции
8. **Security POC** — workspace membership вместо Allowed Workspaces в security role
9. **Routing POC** — контекстная маршрутизация при создании тикета

---

## Варианты решения

Три архитектурно различных подхода к эволюции workspace model. Отличаются глубиной изменений, сложностью миграции и потолком возможностей.

---

### Вариант A: Workspace Configuration Layer (лёгкий)

**Суть:** Не трогаем `object.workspace` как поле. Добавляем Workspace Settings — справочный объект, который привязывает конфигурацию (default service/agent group, routing rules) к значению workspace из классификатора.

```
Текущее:
  object.workspace = "IT"       ← строка из классификатора, фильтр видимости
  Security Role → Allowed Workspaces

Добавляем:
  Workspace Settings (новый справочник)
  ├── Name: "IT"                ← совпадает со значением классификатора
  ├── Default Agent Group: "IT Support L1"
  ├── Default Service: "IT General Support" (→ Service → SLA)
  ├── Email Alias: "it@company.com"
  └── Business Hours: "24/5"

Как работает:
  1. Объект создаётся, получает workspace = "IT" (как сейчас)
  2. Система смотрит Workspace Settings["IT"] → определяет SLA, default group, и т.д.
  3. Security model — без изменений (Allowed Workspaces в роли)
  4. Routing: новый тип Business Policy "Workspace Router" — определяет workspace при создании
```

**Что меняется:**

| Аспект | Изменение |
|--------|-----------|
| Модель данных | `object.workspace` остаётся строкой. Добавляется Workspace Settings (справочник) |
| Security | Без изменений. Allowed Workspaces в роли работает как раньше |
| Automations | Без изменений в scope. Админ вручную добавляет condition `workspace = "IT"` в триггеры |
| SLA | Без изменений (SLA привязан к Service). Workspace Settings может хранить ссылку на default Service → его SLA применяется |
| Routing | Новый тип Business Policy: определяет workspace по email alias, каталогу, agent context |
| Global objects | Без изменений. `workspace = "Shared"` остаётся как workaround |
| Миграция | Нулевая. Workspace Settings создаётся пустым, заполняется админом |

**Архитектура:**

```
┌─────────────────────────────────────────────┐
│                 Platform                     │
│                                             │
│  object.workspace = "IT" (строка, как есть) │
│        ↓                                    │
│  Workspace Settings["IT"]                   │
│  ├── Default Service → IT Service (→ IT SLA) │
│  ├── Group → IT Support L1                  │
│  └── Email → it@company.com                 │
│                                             │
│  Security Role → Allowed Workspaces (как есть)│
└─────────────────────────────────────────────┘
```

**Преимущества:**

- Zero migration risk — ничего не ломается
- Быстрая реализация (3-4 месяца)
- Workspace Settings — опциональный: если не настроен, всё работает как раньше
- Не требует dual-write, write-shim, compatibility mode
- Инкрементально: можно постепенно добавлять конфигурацию в Workspace Settings

**Недостатки:**

- Workspace остаётся строкой в объекте — нет FK integrity
- Security model не меняется — нет workspace membership, нет per-workspace ролей
- Automations не workspace-scoped — админ вручную добавляет условия
- Global/workspace-scoped разделение — только convention, не enforce
- `workspace = "Shared"` — workaround остаётся
- Потолок: не решает фундаментальную проблему "workspace = поле, а не контекст"

**Кому подходит:** Если основная потребность — привязать SLA и default groups к workspace, без переделки security model. Quick win для клиентов, использующих 2-3 workspace.

---

### Вариант B: Workspace as Context (средний) — текущее предложение

**Суть:** Workspace становится первоклассным бизнес-объектом. `object.workspace` (строка) → `workspace_id` (FK). Появляется чёткое разделение workspace-scoped vs global objects. Security model переходит на workspace membership. Двухфазная миграция с dual-write.

Это вариант, детально описанный в основной части документа.

```
Workspace (бизнес-объект)
├── Agent Groups, Services (→ SLA), Catalog, Automations, Settings
│
object.workspace_id = FK → Workspace    ← вместо строки
│
Security: Agent ←→ Workspace (membership) + Role per workspace
│
Global objects (Person, Org) — без workspace, Organization ACL
Workspace-scoped objects (Ticket, Automation) — принадлежат workspace
```

**Что меняется:** (резюме основного предложения)

| Аспект | Изменение |
|--------|-----------|
| Модель данных | `workspace_id` FK вместо строки. Workspace — бизнес-объект |
| Security | Workspace membership + per-workspace roles. UNION семантика для global objects |
| Automations | Workspace-scoped и global scope. Workspace triggers first |
| SLA | Через workspace-scoped Services (Service → SLA, как в текущей модели) |
| Routing | Детерминированный приоритет 5 уровней, fail-closed для explicit |
| Global objects | Архитектурное разделение. Person/Org/Location — global с Organization ACL |
| Миграция | Dual-write (Version N+1) → new mode (Version N+2). Write-shim для backward compat |

**Преимущества:**

- Чистая архитектура — workspace = организационный контекст
- Конкурентный паритет с Freshservice
- FK integrity, формализованный access control contract
- Workspace-scoped automations, Services (→ SLA), catalog
- Устраняет workaround `workspace = "Shared"`

**Недостатки:**

- Сложная миграция (dual-write, write-shim, 2-version transition)
- ~12-18 месяцев до полной реализации
- Требует opt-in per installation — клиенты мигрируют в разном темпе
- Не решает hard isolation для regulated industries (HR data, finance data)

**Кому подходит:** Основной вариант для multi-department ESM. Баланс между architectural correctness и реализуемостью.

---

### Вариант C: Workspace as Tenant (тяжёлый)

**Суть:** Workspace — это soft tenant с hard data boundaries. Каждый workspace имеет собственную конфигурацию custom fields, workflows, и даже object schemas. Global objects (Person, Org) доступны через контролируемую проекцию, а не напрямую.

```
┌─────────────────────────────────────────────────────────────────┐
│                     Platform (System Layer)                      │
│                                                                 │
│  ┌──────────────────────────────────────────────────────┐      │
│  │ Global Registry: Persons, Organizations, Locations    │      │
│  │ (master data, не доступен агентам напрямую)           │      │
│  └──────────────────────────────────────────────────────┘      │
│       ↓ projection          ↓ projection         ↓ projection  │
│  ┌────────────────┐  ┌────────────────┐  ┌────────────────┐   │
│  │ Tenant: IT     │  │ Tenant: HR     │  │ Tenant: Fac    │   │
│  │                │  │                │  │                │   │
│  │ Tickets        │  │ Tickets        │  │ Tickets        │   │
│  │ Catalog        │  │ Catalog        │  │ Catalog        │   │
│  │ KB Articles    │  │ KB Articles    │  │ KB Articles    │   │
│  │ Automations    │  │ Automations    │  │ Automations    │   │
│  │ Custom Fields  │  │ Custom Fields  │  │ Custom Fields  │   │
│  │ Workflows      │  │ Workflows      │  │ Workflows      │   │
│  │ Services → SLA │  │ Services → SLA │  │ Services → SLA │   │
│  │                │  │                │  │                │   │
│  │ Person View    │  │ Person View    │  │ Person View    │   │
│  │ (projection)   │  │ (projection)   │  │ (projection)   │   │
│  └────────────────┘  └────────────────┘  └────────────────┘   │
│                                                                 │
│  Cross-tenant: запрещён по умолчанию, разрешается explicit      │
│  sharing rules                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Ключевые отличия от Варианта B:**

| Аспект | Вариант B (Context) | Вариант C (Tenant) |
|--------|--------------------|--------------------|
| **Global objects** | Доступны напрямую, Organization ACL | Доступны через проекцию; workspace видит только "своих" people |
| **Custom Fields** | Общие для всех workspaces | Workspace-specific: IT Ticket имеет поля X, HR Ticket — поля Y |
| **Workflows** | Один workflow на класс объекта | Workspace-specific workflows: IT Incident workflow ≠ HR Incident workflow |
| **Cross-reference** | Разрешён по умолчанию | Запрещён по умолчанию; разрешается через sharing rules |
| **Data isolation** | Soft (membership + Organization ACL) | Hard (tenant boundary + explicit sharing) |
| **Admin model** | Global Admin + Workspace Admin | Workspace Admin = почти полный контроль внутри tenant; System Admin = cross-tenant |

**Проекция Global Objects:**

```
Global Registry: Person "John Smith" (Org: Acme Corp)

IT Workspace projection:
  Видит: John Smith — имя, email, отдел, телефон
  (полный профиль — IT управляет инфраструктурой для всех)

HR Workspace projection:
  Видит: John Smith — имя, email, отдел, телефон, salary grade, employment date
  (HR-специфичные поля видны только HR workspace)

Facilities Workspace projection:
  Видит: John Smith — имя, отдел, локация, office number
  (Facilities видит только location-relevant данные)

Projection rules:
  Каждый workspace определяет какие поля Person видны внутри tenant.
  Global Admin настраивает projection rules.
```

**Что меняется:**

| Аспект | Изменение |
|--------|-----------|
| Модель данных | Workspace = tenant с собственной schema. `workspace_id` FK + tenant isolation layer |
| Security | Tenant boundary — hard. Agent видит только объекты своего tenant. Cross-tenant через sharing rules |
| Automations | Полностью изолированы per tenant. Нет "global triggers" — только system-level jobs |
| Custom Fields | Per-workspace: IT Ticket может иметь поле "Affected CI", HR Ticket — поле "Employee ID" |
| Workflows | Per-workspace: разные workflows для Ticket в IT vs HR |
| Global objects | Projection model: master data в Global Registry, tenant видит проекцию |
| Миграция | Тяжёлая: reorganization данных по tenants, projection rules, sharing rules |

**Преимущества:**

- Hard data isolation — подходит для regulated industries (GDPR, HIPAA)
- Workspace-specific custom fields и workflows — максимальная гибкость
- HR workspace не видит IT-специфичные данные и наоборот
- Cross-tenant access — explicit и auditable
- Масштабируется для MSP (managed service providers) — каждый клиент = workspace
- Projection model решает проблему "HR agent видит salary data через global Person"

**Недостатки:**

- Наивысшая сложность реализации (~24-30 месяцев)
- Тяжёлая миграция — нужно определить tenant boundaries для всех существующих данных
- Per-workspace custom fields и workflows = explosion конфигурации
- Cross-tenant references сложны (sharing rules, projection sync)
- Может быть overkill для 80% клиентов, которым достаточно soft isolation
- Reporting across tenants — отдельная сложная задача
- Не соответствует модели Freshservice (Freshservice проще)

**Кому подходит:** Regulated industries, MSP, крупные организации с жёстким разделением departmental data. ServiceNow Domain Separation аналог.

---

### Сравнение вариантов

| Критерий | A: Configuration Layer | B: Context (рекомендуется) | C: Tenant |
|----------|----------------------|---------------------------|-----------|
| **Архитектурное изменение** | Минимальное | Среднее | Глубокое |
| **Сроки реализации** | 3-4 мес | 12-18 мес | 24-30 мес |
| **Migration risk** | Нулевой | Средний (dual-write) | Высокий (data reorganization) |
| **Data isolation** | Нет (только visibility filter) | Soft (membership + Org ACL) | Hard (tenant boundary) |
| **Custom fields per workspace** | Нет | Нет | Да |
| **Workflows per workspace** | Нет | Нет (один workflow на класс) | Да |
| **Workspace-scoped automations** | Нет (manual conditions) | Да | Да (полностью изолированы) |
| **Global object model** | `workspace = "Shared"` workaround | Global layer + Organization ACL | Projection model |
| **Cross-workspace access** | Не контролируется | Разрешён, контролируется ACL | Запрещён, разрешается sharing rules |
| **Конкурентный паритет** | Ниже Freshservice | Freshservice-level | ServiceNow Domain Separation level |
| **Подходит для ESM** | Базовый | Полноценный | Enterprise-grade |
| **Regulated industries** | Нет | Частично (Org ACL) | Да |
| **Express Edition** | Совместим (один Workspace Settings) | Совместим (один workspace) | Избыточен |

### Рекомендация

**Вариант B (Workspace as Context)** — оптимальный баланс. Закрывает основной use case (multi-department ESM, конкурентный паритет с Freshservice) без избыточной сложности tenant model.

**Возможная стратегия:** B сейчас, с архитектурным заделом для C.

Вариант B не исключает эволюцию в C в будущем:
- Workspace object (B) → Tenant object (C) — расширение, не замена
- Workspace membership (B) → Tenant boundary (C) — ужесточение, не переделка
- Global objects (B) → Projection model (C) — добавление слоя, не замена

Если реализовать B так, чтобы workspace object был extensible (custom fields per workspace — "заглушка" для будущего), переход B→C будет добавлением функциональности, а не рефакторингом.

**Вариант A** имеет смысл как **Phase 0.5** — промежуточный quick win, который можно выпустить за 3-4 месяца, пока идёт разработка Варианта B. Workspace Settings не конфликтует с будущим Workspace object — Settings можно мигрировать в свойства Workspace object при переходе к B.

---

## Заключение

Текущая модель workspaces решает базовую задачу фильтрации видимости, но не масштабируется для multi-department service management. Workspace как поле в объекте — это реализация "снизу вверх" (каждый объект помечен), тогда как конкуренты используют подход "сверху вниз" (workspace как контекст, определяющий поведение и видимость).

**Предлагаемая эволюция:**

| Шаг | Суть |
|-----|------|
| 1. Workspace = бизнес-объект | Вместо значения классификатора |
| 2. Global vs Workspace-scoped | Чёткое разделение объектов |
| 3. Workspace membership | Вместо Allowed Workspaces в security role |
| 4. Workspace-scoped конфигурация | Services (→ SLA), automations, catalog — по workspace |
| 5. Контекстная маршрутизация | Workspace определяется при создании, а не назначается вручную |

Это не замена текущей модели, а её эволюция: существующее поле `object.workspace` трансформируется в FK на workspace object через dual-write переходный период (Version N+1), а logic вокруг workspace усложняется постепенно.

**Контракт безопасности (ключевой принцип):**
- Workspace scope управляет видимостью рабочих объектов (тикеты, каталог, автоматизации)
- Organization ACL управляет видимостью данных о людях и организациях (global objects)
- Оба механизма работают в связке: workspace membership + Organization ACL = полная модель доступа
- Миграция гарантирует parity: ни один агент не получит доступ к объектам, которые не видел раньше

---

*Связанные документы: Platform Conceptual Model, Business Logic and Workflow Model, Modular Product Model Proposal*

*Ключевые термины: Workspace, Organizational Context, Workspace Membership, Global Layer, Workspace-scoped Objects*
