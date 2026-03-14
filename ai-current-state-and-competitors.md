# Alloy Navigator AI: текущее состояние и конкуренты

**Дата:** 14 марта 2026  
**Цель:** кратко и понятно зафиксировать, где Alloy Navigator находится по AI сейчас и как выглядит на фоне ключевых конкурентов.

## Короткий вывод

Alloy уже не находится на стартовой точке. У продукта есть рабочий AI foundation:
- AI для end users в `Self-Service Portal`
- AI-помощь для technicians
- `RAG` по внутренним данным
- object-level AI actions
- enterprise-oriented control через permissions и настройки

Главный вывод: Alloy не нужно "добавлять AI с нуля". Основная задача теперь не в появлении базовых AI-функций, а в расширении breadth и depth, где лидеры рынка уже ушли дальше.

## 1. Что уже есть в Alloy

По доступной документации Alloy и по описанию уже реализованных изменений в продукте сейчас доступны:

- Интеграция с `OpenAI` и `Azure OpenAI`.
- Настройка моделей для completion, embeddings, speech-to-text и text-to-speech.
- `RAG` по внутренним данным Alloy.
- `AI Assistant` для end users в `Self-Service Portal`.
- `AI Writing Assistance` в HTML-полях: summarize, improve, translate.
- Object AI actions на формах объектов:
  - `Suggest Category`
  - `Summarize Incident / Problem / Request / Work Order / Ticket`
  - `Suggest Solution / Suggest Next Steps / Suggest Action Plan`
  - `Analyze Risks` для `Change Requests`
- Permission-aware retrieval: AI должен учитывать права пользователя и не должен подтягивать недоступные записи в prompt.
- Возможность переопределять prompts через настройки.

Что это означает на практике:
- Alloy уже покрывает базовые AI use cases для service desk.
- Alloy особенно силен там, где важны `control`, `security` и `object-specific behavior`.
- Это уже enterprise-grade foundation, а не просто "чат с моделью".

## 2. Как выглядит рынок

Рынок ITSM уже воспринимает AI как обязательную часть продукта. В сильных решениях AI обычно покрывает 3 слоя:

- `End-user AI`: virtual agent или conversational assistant для портала и других каналов.
- `Technician AI`: copilot для agents/technicians внутри рабочего интерфейса.
- `Operational AI`: аналитика, clustering, prediction, workflow assistance, иногда action execution.

## 3. Краткая картина по конкурентам

- **Atlassian Jira Service Management**: самый широкий AI-слой по breadth. Есть virtual agent, AI triage, suggestions for request types and fields, alert grouping, PIR generation, incident summaries in Slack, automation rule generation, sentiment и natural-language search. Это очень сильный platform AI игрок. Источник: Atlassian Support.

- **ManageEngine ServiceDesk Plus**: один из самых сильных practical AI игроков для ITSM admins. Есть summarization, knowledge assistance, workflow и script generation, predictive triage, problem/change analysis и гибкая модель подключения LLM. Сильная сторона: AI не только для агентов, но и для process owners. Источник: ManageEngine AI pages.

- **Freshservice**: сильный mid-market AI пакет с хорошим time-to-value. Есть AI agent для end users и Freddy AI Copilot для agents/technicians: summarization, categorization, response assistance, routing и recommendations. Сильная сторона: сбалансированный end-user и technician experience. Источник: Freshservice AI pages.

- **SysAid**: сильный акцент на governance и controllability. Есть Copilot, AI Chatbot for Agents, case summarization, categorization, sentiment, monitoring, usage dashboards и элементы тонкой настройки AI. Сильная сторона: enterprise control plane вокруг AI и отдельный AI слой для technicians. Источник: SysAid AI docs.

- **HaloITSM**: быстро усиливает AI в service operations. Есть summaries, suggested replies, routing, knowledge drafting, sentiment и virtual agent. Сильная сторона: AI и для end users, и для technicians внутри рабочего процесса. Источник: Halo AI pages.

- **InvGate**: прагматичный AI набор для service desk. Есть virtual agent для end users и agent-facing AI: ticket summary, improved responses, solution recommendation, KB generation и incident pattern support. Сильная сторона: компактный, но полезный набор AI use cases для service desk agents. Источник: InvGate AI pages.

## 4. Позиция Alloy на фоне конкурентов

Позицию Alloy сейчас разумно описывать так:

- Alloy уже не `AI-light`.
- Alloy ближе к сильному `mid-tier` уровню.
- У Alloy хороший enterprise baseline: `RAG`, object-aware actions, configurable providers, permission-aware retrieval.
- Основной gap относительно лидеров не в том, что "AI нет", а в том, что пока меньше breadth на следующем уровне зрелости.

Ключевые пробелы относительно лидеров:
- similar ticket / duplicate detection
- problem and incident clustering
- SLA risk prediction
- manager analytics copilot
- workflow / automation generation
- asset-aware grounding
- controlled action execution

## 5. Сравнительная таблица

| Capability | Alloy | Jira | ManageEngine | Freshservice | SysAid | Halo | InvGate |
|---|---|---|---|---|---|---|---|
| End-user portal assistant / virtual agent | • | • | • | • | • | • | • |
| Technician copilot / agent assist | • | • | • | • | • | • | • |
| Ticket / case summarization | • | • | • | • | • | • | • |
| Category / triage suggestion | • | • | • | • | • | • | Partial |
| RAG on KB | • | • | • | • | • | • | • |
| RAG on historical tickets / incidents | • | • | • | Partial | • | • | Partial |
| Permission-aware retrieval | • | Partial | Partial | Partial | Partial | Partial | Partial |
| Change risk analysis | • | Partial | • | Partial | Partial | Partial | No |
| Similar ticket / duplicate detection | No | • | • | • | Partial | • | Partial |
| Problem / incident clustering | No | • | • | Partial | Partial | Partial | Partial |
| SLA risk prediction | No | Partial | • | Partial | Partial | Partial | Partial |
| Manager analytics copilot | No | • | Partial | Partial | Partial | Partial | No |
| Workflow / automation generation | No | • | • | Partial | No | Partial | No |
| Asset-aware grounding | No | Partial | Partial | Partial | Partial | Partial | No |
| Controlled action execution | No | Partial | Partial | Partial | Partial | Partial | No |

Примечания:
- `•` означает, что capability явно присутствует в продукте или официально заявлен.
- `Partial` означает ограниченную, менее зрелую или косвенно подтвержденную реализацию.
- Для Alloy строка `End-user portal assistant / virtual agent` сейчас означает AI assistant в `Self-Service Portal`, а не multi-channel coverage.
- Для Alloy таблица основана на доступной документации и на описанных реализованных CR.

## 6. Что означают capabilities

- **End-user portal assistant / virtual agent**: AI для конечных пользователей, который отвечает на вопросы, предлагает статьи, собирает контекст и помогает создать тикет.
- **Technician copilot / agent assist**: AI-помощник для technicians внутри рабочего интерфейса. Обычно помогает с summary, next steps, suggested replies и triage.
- **Ticket / case summarization**: краткий AI digest по тикету, инциденту, change или переписке.
- **Category / triage suggestion**: предложение категории, priority, assignment group или других triage-полей.
- **RAG on KB**: AI использует internal knowledge base как grounding source.
- **RAG on historical tickets / incidents**: AI использует прошлые тикеты или инциденты для поиска похожих случаев и рекомендаций.
- **Permission-aware retrieval**: AI retrieval учитывает права пользователя и не раскрывает недоступные данные.
- **Change risk analysis**: AI анализирует change request и предлагает потенциальные риски.
- **Similar ticket / duplicate detection**: поиск похожих или дублирующих обращений.
- **Problem / incident clustering**: grouping похожих инцидентов для поиска общей причины или кандидатов в problem records.
- **SLA risk prediction**: оценка риска нарушения SLA заранее.
- **Manager analytics copilot**: AI для руководителей и process owners, который помогает анализировать backlog, SLA, trends и bottlenecks.
- **Workflow / automation generation**: AI помогает создавать workflows, scripts, business rules или automation rules.
- **Asset-aware grounding**: AI использует не только текст тикета и KB, но и контекст из assets, CI, software, contracts, vendors и history.
- **Controlled action execution**: AI не только рекомендует, но и готовит или запускает действия с audit trail и обычно с human approval.

## Итог

Alloy уже имеет сильную базу для AI в ITSM. Его сильные стороны сегодня:
- security-aware AI
- object-specific AI actions
- technician-facing assistance
- RAG и configurability

Чтобы приблизиться к лидерам и местами их обойти, Alloy нужно усиливаться не в "ещё одном чатботе", а в `operational intelligence`, `workflow-aware AI` и `asset-aware AI`.
