# Предложение: Унификация IT Asset Management

**Дата:** Январь 2026  
**Статус:** Decision Made  
**Scope:** IT Asset Management (без CMDB)

**Решения:**
- IT Asset унификация: CI + Asset → единый IT Asset с типами
- Software License: остаётся отдельным классом

---

## Structure of This Document

- **Current Product Baseline (As-Is):** current AN model and competitor patterns used as reference context
- **Proposed Future-State Changes (To-Be):** target model, migration impact, risks, and implementation options

---

## Proposal Summary

### Суть предложения

**Часть 1: IT Asset унификация**

Объединить Configuration Items (CI) и Assets в единый объект **IT Asset** с:
- Динамическими типами (Type-specific fields)
- Встроенными финансовыми полями
- Отдельными Workflows по типам/классам

**Часть 2: Software Asset Management**

Software License остаётся **отдельным классом**, но участвует в CMDB как CI наравне с IT Asset.

---

## Current Product Baseline (As-Is)

### Анализ конкурентов

### ServiceNow ITAM

**Архитектура:**
- Отдельные модули: HAM (Hardware), SAM (Software), CAM (Cloud)
- **Единый Asset Lifecycle** для всех типов (типичная конфигурация: Request → Order → Receive → Deploy → Use → Maintain → Retire)
- Asset и CI — связанные, но разные объекты (Asset = финансы, CI = конфигурация)
- Flow Designer для создания автоматизаций на разных этапах lifecycle

**Workflows:**
- Lifecycle **общий** для всех hardware assets
- Автоматизации (workflows) привязываются к **этапам lifecycle**, не к типам
- Можно создавать разные автоматизации через Flow Designer по условиям (тип, категория)

### Jira Assets (Insight)

**Архитектура:**
- **Object Schema** — полностью гибкая схема
- **Object Types** — пользователь создаёт любые типы с любыми атрибутами
- Наследование атрибутов от parent к child object types
- Нет встроенного понятия "lifecycle" — статусы как обычные атрибуты

**Workflows:**
- Статусы — просто атрибуты объекта, не отдельные workflows
- Automation rules на уровне schema, не на уровне типа
- Переходы статусов через Jira workflows (если связано с issues)

### Freshservice ITAM

**Архитектура:**
- Иерархия Asset Types: Hardware → Computer → Laptop
- Asset States: пользователь определяет (примеры: In Stock, In Use, Missing, Retired)
- CI Types создаются пользователем с custom fields

**Workflows:**
- **Workflow Automator привязан к Asset Type** при создании
- Разные типы — разные automators (нельзя менять тип после создания)
- Event-based automators работают с parent types (Computer срабатывает для Laptop)
- Lifecycle states общие, но автоматизации можно делать по типам

### ManageEngine AssetExplorer

**Архитектура:**
- Visual Workflow Builder — drag-and-drop
- ~50 предустановленных Configuration Types
- IT и Non-IT assets в одной системе

**Workflows:**
- **Custom workflows для разных asset states**
- Workflows привязаны к этапам lifecycle, не к типам напрямую
- Можно настраивать actions на разных состояниях

---

### Выводы из анализа

| Аспект | Как у конкурентов |
|--------|-------------------|
| Единая модель | Да — Asset с типами |
| Гибкие типы | Да — пользователь создаёт типы |
| Lifecycle | **Общий** для всех assets (states настраиваются пользователем) |
| Workflows по типам | **Частично** — автоматизации можно фильтровать по типу, но lifecycle общий |
| Finance встроены | Да — часть объекта Asset |
| Asset ≠ CI | Часто да — ServiceNow разделяет, Jira объединяет |

**Вывод:** Конкуренты используют общий lifecycle + условные автоматизации. Предлагаемое решение с разными workflows по типам — более гибкое, но требует проверки/доработки механизма AN.

---

### Software Asset Management: Анализ конкурентов

### ServiceNow SAM Pro

**Архитектура трёх сущностей:**

```
┌─────────────────────┐     ┌─────────────────────┐     ┌─────────────────────┐
│ Software Discovery  │────▶│   Software Model    │◀────│ Software Entitlement│
│      Model          │     │   (Product)         │     │    (License)        │
├─────────────────────┤     ├─────────────────────┤     ├─────────────────────┤
│ • Discovered SW     │     │ • Normalized name   │     │ • License type      │
│ • Publisher         │     │ • Publisher         │     │ • Metric (per user, │
│ • Version           │     │ • Version/Edition   │     │   per device, core) │
│ • Installations     │     │ • Discovery Maps    │     │ • Purchased rights  │
│ • Normalization     │     │                     │     │ • Expiration        │
│   status            │     │                     │     │ • Allocations       │
└─────────────────────┘     └─────────────────────┘     └─────────────────────┘
         ↓                           ↓                           ↓
         └───────────────────────────┴───────────────────────────┘
                                     ↓
                            ┌─────────────────────┐
                            │   Reconciliation    │
                            ├─────────────────────┤
                            │ Installations vs    │
                            │ Entitlements =      │
                            │ Compliance Position │
                            └─────────────────────┘
```

**Ключевые концепции:**
- **Discovery Model** — обнаруженное ПО (из Discovery/SCCM/Agent)
- **Software Model** — нормализованный продукт (связь между discovered и licensed)
- **Entitlement** — лицензия (права на использование)
- **Reconciliation** — автоматический расчёт compliance (weekly job)
- **Allocation** — привязка лицензии к user/device

**Процесс:**
1. Discovery находит установленное ПО → создаёт Discovery Model
2. Normalization сопоставляет с Content Library → Software Model
3. Entitlement содержит purchased rights
4. Reconciliation сравнивает installations vs entitlements → compliance

### Freshservice SAM

**Архитектура двух сущностей:**

```
┌─────────────────────┐     ┌─────────────────────┐
│     Software        │◀───▶│    License/Contract │
│  (Discovered/Manual)│     │                     │
├─────────────────────┤     ├─────────────────────┤
│ • Name/Publisher    │     │ • License type      │
│ • Installations tab │     │ • License count     │
│ • Users tab         │     │ • Expiration        │
│ • Status (Managed,  │     │ • Contract link     │
│   Ignored, etc.)    │     │                     │
└─────────────────────┘     └─────────────────────┘
         ↓                           ↓
         └───────────────────────────┘
                    ↓
         Compliance = Installed vs Licensed
```

**Ключевые концепции:**
- **Software** — единый объект (discovered + manual SaaS)
- **Managed Software** — софт, для которого ведётся tracking
- **Licenses** — привязаны к Software через Contracts
- **Compliance** — purchased vs installed count

**Проще чем ServiceNow:** нет отдельной нормализации, нет Discovery Model как объекта.

### ManageEngine ServiceDesk Plus

**Архитектура:**
- **Software** — обнаруженное ПО (из scan)
- **Software License** — лицензия с purchased count
- **Metering** — usage tracking
- **Compliance** = purchased vs installed

**Особенности:**
- Automatic mapping discovered → licenses при scan
- Software metering для usage tracking
- Alerts на over/under licensing
- Prohibited software detection

### Jira Assets

**Архитектура:** Полностью гибкая, нет встроенного SAM.

- Пользователь создаёт свои Object Types: Software, License, User
- References между объектами для связей
- Нет встроенного reconciliation — только manual tracking или plugins

---

### Текущая модель AN vs конкуренты (Software)

### Текущая модель AN

```
┌─────────────────────┐     ┌─────────────────────┐
│  Tracked Software   │◀────│  Software License   │
├─────────────────────┤     ├─────────────────────┤
│ • OID, Name         │     │ • Tracked_Software_ID│
│ • Type, Category    │     │ • License_Qty       │
│ • Status, Owner     │     │ • Policy (Per Install│
│ • Organization      │     │   Named Computer,   │
│ • Location          │     │   Site License...)  │
│                     │     │ • Tracking (By Device│
│                     │     │   By User, None)    │
│                     │     │ • Compliance rules  │
│                     │     │ • Expiration        │
└─────────────────────┘     └─────────────────────┘
         ↑                           ↑
         │                           │
    ┌────┴────┐                 ┌────┴────┐
    │  Audit  │                 │Allocation│
    │  Data   │                 │to CI/User│
    └─────────┘                 └──────────┘
```

**AN имеет:**
- **Tracked Software** — объект для группировки ПО (аналог Software Model)
- **Software License** — лицензия с policies и tracking
- **Audit Data** — обнаруженные установки (из Discovery)
- **Compliance rules** — restrictions by location, organization, computers, persons

### Сравнительная таблица

| Аспект | ServiceNow | Freshservice | ManageEngine | AN |
|--------|------------|--------------|--------------|-----|
| Discovered SW object | Discovery Model | Software | Software | Audit Data |
| Normalized product | Software Model | — | — | Tracked Software |
| License object | Entitlement | License/Contract | Software License | Software License |
| Reconciliation | Automatic (weekly) | Manual + basic | Automatic | Automatic |
| License metrics | Per core, device, user, named | Per device | Per device, user | Per install, device, user |
| Compliance rules | Model-level | Contract-level | License-level | License-level (rich) |
| Usage tracking | Optional (SCCM) | Basic | Metering | Audit-based |

### Выводы по Software

**AN уже имеет зрелую модель SAM:**
- Tracked Software + Software License = аналог Software Model + Entitlement
- Богатые compliance rules (by location, organization, computers, persons)
- Flexible tracking policies (by device, device owner, user, none)

**Отличия от конкурентов:**
1. **Нет отдельного Discovery Model** — discovered SW в Audit Data, не отдельный объект
2. **Tracked Software = grouping object** — не нормализованный продукт
3. **License policies встроены** — не нужен отдельный reconciliation engine

### Вопрос: Нужно ли менять Software модель?

**Аргументы ЗА текущую модель:**
- Работает, проверена клиентами
- Compliance rules богаче чем у конкурентов
- Tracked Software ≈ Software Model

**Аргументы ЗА изменения:**
- Нет явной нормализации (Content Library)
- Audit Data — не объект первого класса
- Нет автоматического маппинга discovered → tracked

### Software License как CI

**Важно:** Software License в AN функционирует как CI:
- Может быть **Related CI** в Incidents, Changes, Problems
- Участвует в CMDB relationships
- На него ссылаются другие объекты

Цитата из Admin Guide:
> "the Related CI field of a Ticket can contain a link to a Computer, Person, Software License, etc."

### Решение: Software License остаётся отдельным классом

```
IT Asset (класс)           Software License (класс)
├── Type=Computer          ├── Связь с Tracked Software
├── Type=Hardware          ├── License-specific поля
└── Type=Furniture         └── Compliance Rules
         ↓                          ↓
         └──────────────────────────┘
                    ↓
         Оба могут быть Related CI в Tickets
         Оба участвуют в CMDB
```

**Преимущества:**
- Сохраняем зрелую SAM логику без изменений
- Compliance engine не трогаем
- Меньше Type-specific complexity в IT Asset
- Соответствует подходу ServiceNow (Software Entitlement — отдельная сущность от Hardware Asset)

**Software License не меняется:**
- Tracked_Software_ID → связь с Tracked Software
- License_Qty, Policy, Tracking
- Compliance Rules (by location, organization, computers, persons)
- Expiration_Date, Install_Key

### Возможные улучшения SAM (отдельный scope)

1. Создать **Discovered Software** как объект первого класса (аналог Discovery Model)
2. Добавить **Normalization** для автоматического маппинга
3. Улучшить **Usage Tracking** интеграцию

---

## Proposed Future-State Changes (To-Be)

### Предлагаемое решение

### Модель данных

```
IT Asset (класс)                    Software License (класс)
├── Category: Physical | Logical    ├── Tracked_Software_ID
├── Type: Computer | Hardware |     ├── License_Qty, Policy
│         Furniture | Vehicle       ├── Tracking, Compliance Rules
├── Common fields: Name, Owner,     └── Expiration, Install_Key
│   Location, Finance fields...
└── Type-specific fields
         ↓                                   ↓
         └───────────────────────────────────┘
                         ↓
              Оба = CI в CMDB
              Оба могут быть Related CI в Tickets
```

### Разные Workflows по типам

Type определяет Workflow (используем механизм AN — workflows по классам).

**Принцип:** Платформа не предписывает фиксированных статусов или переходов. Пользователь конфигурирует:
- Classification values (статусы)
- Workflow transitions (переходы)
- Automation rules (автоматизации)

**Примеры возможных конфигураций:**

```
IT Asset
├── Type=Computer → Workflow (пример: In Stock → Deployed → In Repair → Retired)
├── Type=Hardware → Workflow (пример: In Stock → Deployed → Maintenance → Retired)
├── Type=Furniture → Workflow (пример: Available → In Use → Disposed)
└── Type=Vehicle → Workflow (пример: In Fleet → Maintenance → Decommissioned)

Software License (отдельный класс)
└── Workflow (пример: Active → Expiring → Renewed | Terminated)
```

*Конкретные статусы и переходы — user-defined, примеры выше — для иллюстрации.*

**Требуется:** Поддержка привязки workflow к Type внутри класса IT Asset (доработка если не поддерживается).

### Standalone Assets

Assets без связи с CI мигрируются как отдельные типы:

| Текущий Asset | Новый IT Asset Type |
|---------------|---------------------|
| Asset (Category=Furniture) | IT Asset (Type=Furniture) |
| Asset (Category=Vehicle) | IT Asset (Type=Vehicle) |
| Asset (без категории) | IT Asset (Type=General Asset) |

---

### Изменения при миграции

| Было | Стало |
|------|-------|
| Computer + связанный Asset | IT Asset (Type=Computer) |
| Hardware + связанный Asset | IT Asset (Type=Hardware) |
| Standalone Asset (Furniture) | IT Asset (Type=Furniture) |
| Standalone Asset (Vehicle) | IT Asset (Type=Vehicle) |
| Связь CI ↔ Asset | Не нужна |
| Asset как отдельный объект | Удаляется |
| Два lifecycle (CI + Asset) | Один workflow по Type (user-configured) |
| **Software License** | **Без изменений** (отдельный класс) |
| **Tracked Software** | **Без изменений** |

---

### Риски

### Критические

| Риск | Описание | Митигация |
|------|----------|-----------|
| Потеря данных при merge | Конфликты при объединении CI + Asset | Backup, validation, test migration |
| Ломаются Asset workflows | Клиенты с отдельным Asset lifecycle | Маппинг на Type-specific workflows |
| API breaking changes | Внешние интеграции перестают работать | Versioned API, deprecation period |

### Высокие

| Риск | Описание | Митигация |
|------|----------|-----------|
| Reports ломаются | Reports по старым объектам | Compatibility views, migration guide |
| Object ID изменяются | Ссылки в tickets, emails | Сохранение старых ID как alias |
| Standalone Assets | Нужно определить новые типы | Анализ данных до миграции |
| Конфликт статусов | CI=Retired, Asset=In Use | Правила маппинга |

### Средние

| Риск | Описание | Митигация |
|------|----------|-----------|
| User confusion | Изменение терминологии | Documentation, training |
| Performance | Единая большая таблица | Indexing, partitioning |
| Type-specific fields | Как реализовать динамику | UDF или встроенный механизм |

---

### Варианты реализации

**A. Hard Migration**
- Автоматическая миграция при upgrade
- CI + Asset → IT Asset сразу
- Workflows маппятся на Type-specific
- Breaking changes

**B. Soft Migration (рекомендуется)**
- Новые объекты создаются как IT Asset
- Старые CI/Asset работают, но deprecated
- Migration wizard для переноса
- 2-3 версии переходный период

**C. Compatibility Layer**
- IT Asset — новая модель
- CI/Asset — views для backward compatibility
- Долгосрочная поддержка двух моделей

---

### Преимущества решения

| Преимущество | Описание |
|--------------|----------|
| Упрощение модели | Один объект вместо двух связанных (CI + Asset) |
| Гибкость | Каждый тип — свой workflow с user-defined статусами и переходами |
| Используем механизм AN | Workflows по классам/типам уже реализованы |
| Чистый upgrade path | Express → Enterprise без миграции данных |
| Non-IT assets | Furniture, Vehicles с собственными workflows |
| SAM без изменений | Software License остаётся отдельным классом, зрелая логика сохраняется |
| Оба как CI | IT Asset и Software License участвуют в CMDB |

---

### Открытые вопросы

### По IT Asset унификации
1. **Workflow по Type** — поддерживает ли AN привязку workflow к Type внутри класса? Если нет — требуется доработка.
2. **Type-specific fields** — UDF или встроенная динамическая схема?
3. **Object ID** — сохранять старые (Computer-001) или генерировать новые (ITAsset-001)?
4. **Finance Status** — отдельное поле или часть основного Status?

### По Software Asset Management (отдельный scope)
5. **Discovered Software object** — нужен ли объект первого класса для обнаруженного ПО?
6. **Normalization** — нужна ли автоматическая нормализация (Content Library)?

---

### Следующие шаги

### IT Asset унификация
1. **Prototype** — создать IT Asset с Type-specific workflows
2. **Инвентаризация** — собрать все существующие Asset categories/types у клиентов
3. **Mapping rules** — определить правила миграции Asset → IT Asset Type
4. **API analysis** — оценить breaking changes для интеграций

### Software Asset Management (отдельный scope, опционально)
5. **Discovered Software object** — оценить необходимость создания объекта первого класса
6. **Normalization research** — изучить Content Library подход ServiceNow

---

*Связанные документы: Express Product Specification, WF2 Business Requirements*
