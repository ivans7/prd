# Alloy Navigator AI: краткий roadmap

**Дата:** 14 марта 2026  
**Цель:** зафиксировать реалистичный roadmap развития AI в Alloy Navigator на ближайшие 12 месяцев с учетом текущего baseline и технических ограничений платформы.

## Короткий вывод

У Alloy уже есть рабочий AI foundation, поэтому roadmap должен строиться не вокруг базовых demo-функций, а вокруг расширения полезности AI для end users, technicians и service operations.

Ключевое ограничение:
- AI для генерации, редактирования и безопасного применения workflow нельзя считать ближним приоритетом.
- Этот блок нужно ставить только после переписывания соответствующего слоя на `C#`.

Это меняет приоритеты: в ближайший год Alloy должен усиливаться не в `workflow editing by AI`, а в `operational intelligence`, `asset-aware grounding`, `manager insights` и `controlled action drafts`.

Отдельно важно:
- полноценный `Technician Copilot` внутри рабочего интерфейса еще не является частью текущего релиза;
- это должен быть один из первых крупных AI milestones следующей версии.

## Принцип roadmap

Приоритеты должны идти в таком порядке:
- сначала `control`, `observability`, `quality`
- затем `technician copilot`, `triage`, `portal`, `technician productivity`
- затем `service operations intelligence`
- затем `asset-aware AI` и `controlled actions`
- и только после переписывания workflow-слоя на `C#` - `workflow generation / editing`

## Q2 2026: укрепить foundation

- Додокументировать object AI actions и текущие AI scenarios.
- Добавить AI observability: usage, latency, cost, failure rate, feedback.
- Ввести prompt/version control для object AI actions.
- Усилить explainability: показывать, какие источники использованы для ответа.
- Подготовить evaluation harness для ключевых use cases: summarize, category suggestion, next steps, change risk analysis, portal assistant.

**Результат:** прозрачный и управляемый AI слой, который можно безопасно масштабировать.

## Q3 2026: выйти на parity по core service desk AI

- Выпустить `Technician Copilot / Agent Assist` внутри рабочего интерфейса.
- Assignment suggestion и priority suggestion.
- Similar tickets / duplicate detection.
- Улучшение SSP assistant: follow-up questions, KB-first guidance, draft ticket creation.
- Генерация draft KB article из resolution.
- Similar case recommendations для technicians.

**Результат:** Alloy получает полноценный technician-facing AI layer и закрывает основные пробелы относительно Freshservice, SysAid и InvGate по core service desk AI.

## Q4 2026: перейти к service operations intelligence

- Incident clustering и problem candidate detection.
- SLA breach risk prediction.
- Расширенный risk analysis для Change Requests.
- Manager copilot для аналитики по backlog, SLA, categories, KB gaps и trends.
- Recommendations по escalation и reassignment для tickets at risk.

**Результат:** сокращение отрыва от Jira, ManageEngine и Halo по operational AI.

## Q1 2027: создать differentiators без зависимости от workflow rewrite

- Расширить grounding за пределы incidents и KB:
  - assets / CI
  - software
  - contracts
  - vendors
  - locations
  - change history
- Добавить asset-aware AI suggestions для technicians и change managers.
- Добавить controlled action layer в формате draft-only:
  - draft field updates
  - draft child work orders
  - draft change tasks
  - draft requester responses
  - только с human approval
- Добавить AI explanation для существующей automation logic:
  - explain current rule behavior
  - explain why record was routed this way
  - identify possible overlap/conflict between existing rules

**Результат:** Alloy получает зоны возможного превосходства без зависимости от переписывания workflow слоя.

## Post C# Rewrite: workflow-aware AI

Этот блок должен идти отдельной фазой после переписывания workflow-слоя на `C#`.

Только после этого имеет смысл делать:
- draft business rule creation from natural language
- AI-assisted workflow editing
- AI-assisted automation generation
- simulation and validation of generated workflow changes
- safe application of workflow changes with approval and audit trail

**Результат:** workflow-aware AI будет строиться на поддерживаемом и безопасном техническом основании, а не на legacy-слое.

## Рекомендуемый стратегический фокус

Наиболее реалистичные и сильные differentiators Alloy на ближайший период:
- **Secure AI by design**: permission-aware retrieval и enterprise control.
- **Technician productivity AI**: technician copilot, object-specific AI actions, similar cases, better triage, better summaries.
- **Operational intelligence**: clustering, SLA risk, manager insights.
- **Asset-aware AI**: рекомендации с учетом operational context, а не только текста тикета.
- **Explainable AI**: понятные источники и логика рекомендаций.

Рекомендация: не строить ближайший roadmap вокруг workflow authoring by AI. До переписывания слоя на `C#` это будет дорогой и рискованный путь. В ближайший горизонт выгоднее усиливать те AI-возможности, которые уже опираются на существующую data model, RAG, permissions и object actions.
