# Alloy Navigator AI: краткий roadmap

**Дата:** 14 марта 2026  
**Цель:** зафиксировать приоритетный roadmap развития AI в Alloy Navigator на ближайшие 12 месяцев.

## Принцип roadmap

У Alloy уже есть AI foundation. Значит roadmap должен строиться не от базовых demo-функций, а от масштабирования в platform-level AI с упором на differentiators Alloy: security, workflow, operational context и explainability.

## Q2 2026: закрепить foundation

- Додокументировать object AI actions в official docs и product messaging.
- Добавить AI observability: usage, latency, cost, failure rate, feedback.
- Ввести prompt/version control для object AI actions.
- Усилить explainability: показывать, какие источники использованы для ответа.

**Результат:** прозрачный и управляемый AI слой, который можно масштабировать.

## Q3 2026: выйти на parity по core service desk AI

- Assignment suggestion и priority suggestion.
- Similar tickets / duplicate detection.
- Улучшение SSP assistant: follow-up questions, KB-first guidance, draft ticket creation.
- Генерация draft KB article из resolution.

**Результат:** закрытие основных пробелов относительно Freshservice, SysAid и InvGate.

## Q4 2026: перейти к service operations intelligence

- Incident clustering и problem candidate detection.
- SLA breach risk prediction.
- Расширенный risk analysis для Change Requests.
- Manager copilot для естественно-языковой аналитики по backlog, SLA, categories, KB gaps.

**Результат:** сокращение отрыва от Jira, ManageEngine и Halo по operational AI.

## Q1 2027: создать differentiators Alloy

- Расширить grounding за пределы incidents и KB:
  - assets / CI
  - software
  - contracts
  - vendors
  - locations
  - change history
- Запустить workflow-aware AI:
  - draft business rule
  - explanation of existing automation
  - detection of overlapping/conflicting rules
- Добавить controlled action layer:
  - draft field updates
  - draft child work orders
  - draft change tasks
  - draft requester responses
  - только с human approval

**Результат:** Alloy получает зоны возможного превосходства, а не только parity.

## Рекомендуемый стратегический фокус

Наиболее перспективные differentiators Alloy:
- **Secure AI by design**: permission-aware retrieval и enterprise control.
- **Workflow-aware AI**: AI, встроенный в business rules и service operations.
- **Asset-aware AI**: рекомендации с учётом operational context, а не только текста тикета.
- **Explainable AI**: понятные источники и логика рекомендаций.

Рекомендация: не распыляться на generic chatbot scenarios. Основной упор делать на AI, который использует сильные стороны Alloy Navigator: workflow, data model, service management objects и enterprise governance.
