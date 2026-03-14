# Alloy Navigator AI: текущее состояние и конкуренты

**Дата:** 14 марта 2026  
**Цель:** кратко зафиксировать текущий AI baseline Alloy Navigator и положение относительно ключевых конкурентов.

## 1. Текущее состояние Alloy Navigator

По документации Alloy Navigator и уже реализованным изменениям Alloy находится не на стартовой точке, а на уровне первого зрелого AI foundation.

Уже реализовано:
- Интеграция с `OpenAI` и `Azure OpenAI`.
- Настройка моделей для completion, embeddings, speech-to-text и text-to-speech.
- Векторный поиск (`RAG`) по данным Alloy.
- `AI Assistant` в Self-Service Portal.
- `AI Writing Assistance` в HTML-полях: summarize, improve, translate.
- Object AI actions на формах объектов:
  - `Suggest Category`
  - `Summarize Incident / Problem / Request / Work Order / Ticket`
  - `Suggest Solution / Suggest Next Steps / Suggest Action Plan`
  - `Analyze Risks` для Change Requests
- Permission-aware access control: AI actions должны учитывать права пользователя; записи, недоступные пользователю, не должны попадать в prompt.
- Возможность override prompts через настройки.

Вывод: Alloy уже имеет базовый enterprise-grade AI слой, включающий copilot-функции для агентов, портал для конечных пользователей, RAG и управляемость на уровне конфигурации.

## 2. Положение относительно конкурентов

Рынок ITSM уже воспринимает AI как обязательную часть продукта. У основных конкурентов AI закрывает три слоя: self-service, agent copilot и operational/manager intelligence.

Краткая оценка:
- **Jira Service Management**: самый широкий AI layer; силён в virtual agent, triage, incident management и analytics.
- **ManageEngine**: один из лидеров по practical AI для ITSM admins; силён в генерации workflow/script, predictive use cases и гибкости модели.
- **Freshservice**: очень сильный mid-market AI пакет; хорошо закрывает assistant, triage, routing и employee experience.
- **SysAid**: сильный акцент на governance, control, monitoring и AI administration.
- **HaloITSM**: быстро усиливается, особенно в AI for service ops и agentic direction.
- **InvGate**: прагматичный AI набор для service desk, но обычно уже по breadth, чем лидеры.

Позиция Alloy:
- Alloy уже не аутсайдер и не “AI-light”.
- По текущему уровню Alloy ближе к strong mid-tier AI platform.
- Alloy особенно силён в enterprise-oriented подходе: configurable providers, object-specific actions, RAG, permission-aware retrieval.
- Основной gap относительно лидеров не в отсутствии AI, а в недостаточной широте второго уровня:
  - predictive intelligence,
  - major incident / problem clustering,
  - manager analytics copilot,
  - workflow generation,
  - asset-aware grounding,
  - controlled action execution.

Главный вывод: Alloy не нужно “добавить AI с нуля”; Alloy нужно расширить уже существующий foundation до platform-level AI.
