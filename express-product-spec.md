# Alloy Navigator Express: Продуктовая спецификация

**Версия:** 1.0  
**Дата:** Декабрь 2024  
**Источник:** ANX 6.2.0 SRS (2011), docs.alloysoftware.com  

---

## 1. Определение продукта

Alloy Navigator Express — ограниченная и готовая к использованию "из коробки" (OUT OF BOX) версия Alloy Navigator.

Единая кодовая база с Navigator Enterprise. Единая схема базы данных. Различия реализованы через ограничение доступа к функциям.

---

## 2. Механизмы формирования Express из Enterprise

### 2.1 Переименование (Aliasing)

- Work Orders → Tickets
- Hardware → Inventory Items
- Triggers → Business Rules
- Workflow and Business Logic → Customization

### 2.2 Скрытие объектов (Hiding)

Скрытые объекты Service Desk:
- Incidents
- Problems  
- Service Requests
- My Tickets / All Tickets (консолидированные виды)

Скрытые модули:
- Projects
- Services / SLA
- CMDB (Documents, Configurations)
- Assets (как отдельный объект)

Примечание: Change Requests доступны в Express, но рассматриваются отдельно от Tickets.

### 2.3 Объединение модулей

Configuration Management + Asset Management → IT Assets

---

## 3. Создание объектов

### 3.1 Enterprise: Create Actions

Объекты создаются через Create Actions, которые включают:
- Template — инициализация полей
- Form — пользовательский интерфейс ввода
- Programming — операции при создании (Operations)

При создании объекта можно выполнить любую логику: обновить связанные объекты, отправить уведомления, запустить внешние команды.

### 3.2 Express: Templates

Объекты создаются через Templates, которые включают только:
- Инициализация полей
- Caption формы

Programming (Operations) недоступен. При создании объекта выполняется только инициализация полей без программируемой логики.

---

## 4. Классы объектов по уровню кастомизации

### Класс 1 — без кастомизации
Объекты: Discovered Installations, Manufacturers

### Класс 2 — только справочники (Lookups)
Объекты: Organizations, Locations, Vendors

### Класс 3 — справочники + уведомления + шаблоны
Объекты: Computers, Inventory Items, Purchase Orders, Contracts, Software Licenses, Knowledge Base Articles, Persons, Library Items

Доступно:
- Lookups (справочники/классификаторы)
- Notifications (уведомления)
- Templates (шаблоны создания)

### Класс 4 — полная кастомизация для Express
Объекты: Ticket, Change Request, Approval Request

Доступно всё из Класса 3, плюс:
- Business Policies (Prioritization, Routing, Escalation)
- Business Rules (триггеры)

Примечание: Change Request и Ticket — разные объекты. Views, widgets, reports для Tickets не включают Change Requests.

---

## 5. Business Policies и Business Rules

### 5.1 Business Policies

Доступны для Tickets и Change Requests. Включают:
- Prioritization — автоматическая установка приоритета
- Routing — автоматическое назначение
- Escalation — эскалация при выполнении условий

Для Approval Requests доступна только Escalation Policy.

### 5.2 Business Rules (триггеры)

Доступны для Tickets и Change Requests. 

Доступные операции:
- Update Field
- Function (вызов предустановленных функций)
- Execute SQL
- Send Notification

Недоступные компоненты:
- Создание Forms
- Создание Functions
- Validation Rules

---

## 6. Actions

Управление Actions недоступно. Администратор может только:
- Enable/Disable предустановленных Actions
- Настраивать доступ к Actions через Roles

Создание новых Actions и редактирование Programming закрыто.

---

## 7. Change Management

### 7.1 Change Request

Change Request — отдельный класс объектов, аналог CR в Enterprise.

Отличия от Enterprise:
- Вместо вкладки WO показывается вкладка Child Tickets
- Отсутствуют атрибуты: Service, Urgency, Impact
- Вместо Related CI показывается Related Asset
- Escalation через Business Policies (не отдельная секция в Workflow Configuration)

Change Request и Ticket — разные объекты. Views, widgets, reports типа "My Tickets" не содержат информации о CR.

### 7.2 Approval

Approval реализован для трёх типов объектов:
- Change Request
- Purchase Order
- Knowledge Base Article

Approval Request — специальный объект (не тикет).

Упрощения по сравнению с Enterprise:
- Только одна Approval Stage (в Enterprise — несколько)
- Объект Approval Stage скрыт от пользователя
- Атрибуты Approval Type и Approval Deadline показываются на вкладке Approvals

### 7.3 Approval Workflow Parameters

Для каждого процесса (CR Approvals, KB Approvals, PO Approvals) настраиваются:
- Approvers by Default — выбор из группы или индивидуально
- Default Approver Group
- Default Approval Type — All Approved или Majority Decision
- Approval Deadline (days)

---

## 8. Компоненты Workflow

### Доступно в Express:
- Templates (без Programming)
- Notifications (Enable/Disable, редактирование текста)
- Business Policies (для Tickets, Change Requests, Approval Requests)
- Business Rules (для Tickets, Change Requests)
- Workflow Parameters
- Lookups/Classifiers
- User Defined Fields

### Недоступно в Express:
- Create Actions (создание/редактирование)
- Step Actions (создание/редактирование)
- Functions (создание/редактирование)
- Forms (создание)
- Validation Rules
- Object-level Macros

---

## 9. Дополнительные ограничения

### 9.1 Self Service Portal
Только одна Create Action для Tickets поддерживается.

Approval Requests доступны в SSP (можно включить/выключить). Доступные Actions: Approve, Reject.

### 9.2 Roles
Встроенная роль Administrator не редактируется. Access Scope не поддерживается (добавлен позже).

### 9.3 Service Account
Только один предустановленный Service Account (ALLOY_SYSTEM).

### 9.4 Support Hours
Только один календарь — Help Desk Support Hours.

### 9.5 Dashboard
Один Dashboard с возможностью кастомизации администратором.

---

## 10. Добавленные компоненты

### Network Inventory
Express включает встроенный модуль Network Inventory (на базе Alloy Discovery). Устанавливается как единый продукт.

---

## 11. Резюме принципов

Express формируется из Enterprise через:

1. **Переименование** — изменение названий объектов и секций UI

2. **Скрытие** — удаление из UI объектов и модулей (ITIL-объекты, CMDB, SLA, Projects)

3. **Упрощение создания объектов** — Templates вместо Create Actions, без Programming

4. **Классификация объектов** — 4 класса с разным уровнем кастомизации

5. **Ограничение workflow** — Business Policies и Business Rules только для Tickets, Change Requests, Approval Requests

6. **Блокировка Actions** — только Enable/Disable, без создания/редактирования

7. **Упрощение Approval** — одна Approval Stage вместо нескольких

---

*Конкретные наборы предустановленных Actions, Templates, Business Rules могут отличаться между версиями продукта. Данная спецификация описывает архитектурные принципы, а не конкретную реализацию.*