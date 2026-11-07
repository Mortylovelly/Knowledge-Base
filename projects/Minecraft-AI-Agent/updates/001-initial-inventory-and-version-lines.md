# 001 — Инвентаризация Minecraft AI Agent и разделение версий

- **Дата:** 2026-11-07
- **Проект:** Minecraft AI Agent (старый slug: adastra-create-addon)
- **Активная линия:** Minecraft 1.20.1 Fabric / Java 17
- **Активная ветка:** `minecraft-1.20.1-fabric`
- **Текущая версия:** 0.2.2
- **Статус:** фактические исходники и ветки проверены; runtime gameplay test не подтверждён.

## Зачем появилась эта запись

В старой документации проект ошибочно назывался Ad Astra Create Addon. Владелец уточнил, что это самостоятельный мод с искусственным интеллектом внутри Minecraft, не связанный с Create. Кроме того, у репозитория есть две игровые линии — 1.20.1 и 1.21.1. Владелец установил общий рабочий стандарт: дальше используем Minecraft 1.20.1 Fabric / Java 17, другие версии не использовать, пока он сам не разрешит переход. Если версия/ветка старого проекта неясна, сначала спросить.

## Проверенные ветки и версии

Репозиторий: https://github.com/Mortylovelly/adastra-create-addon

| Ветка | Игра | Java | Commit при проверке | Решение |
|---|---|---|---|---|
| `minecraft-1.20.1-fabric` | 1.20.1 | 17 | `85568e18c7eab3dc1055e0ba3e2434e0c8d17d6a` | Активная |
| `main` | 1.21.1 | 21 | `bc3f229fe1999eeaaee4629acf25ae59bbec457c` | Неактивная, не менять без разрешения |
| `ci-test-1201` | тестовая линия 1.20.1 | требует отдельной сверки | `de62dad480873f17bc930c06fdcdd950d0bece38` | Историческая CI ветка, не использовать |

## Конфигурация активной ветки

Проверены `gradle.properties`, `build.gradle`, `gradle/wrapper/gradle-wrapper.properties`, `fabric.mod.json`:
- Minecraft 1.20.1
- Yarn mappings 1.20.1+build.10
- Fabric Loader 0.17.2
- Fabric API 0.92.11+1.20.1
- Loom 1.9.2
- Gradle Wrapper 8.12
- Java 17
- mod_version 0.2.2
- mod id minecraft_ai_agent
- archive minecraft-ai-agent

В актуальном build.gradle нет Create или Ad Astra dependencies. Это подтверждает, что проект теперь самостоятельный Minecraft AI Agent.

## Как работает текущая реализация

Java-клиент показывает экран чата. `AiAgentService` вызывает API выбранного провайдера (Gemini/DeepSeek/Groq/OpenRouter), получает function calls от модели и запускает соответствующие Minecraft-инструменты через локальный integrated server. Результаты инструментов передаются модели для следующего шага или финального ответа.

`runOnServer()` получает сервер через `MinecraftClient.getInstance().getServer()`. При отсутствии локального integrated server возвращает ошибку «Нет запущенного локального Minecraft-сервера». Поэтому на текущем коде это singleplayer integration, а не dedicated-server agent.

## Подтверждённые функции

### Открытие интерфейса и управление
- Кнопка по умолчанию: Right Shift (в коде `GLFW_KEY_RIGHT_SHIFT`).
- Экран: `AiChatScreen`.
- Выбор провайдера: Gemini, DeepSeek, Groq, OpenRouter.
- Сохранение API key в локальный config.
- Проверка соединения, новый чат, очистка истории, отправка запроса, остановка активного запроса.

### Инструменты ИИ
Подтверждены в исходнике `AiAgentService.createTools()` / `executeTool()` и `AiBuildTool`:
- world observation/inspection;
- найти структуры;
- узнать состояние игрока, блок, инвентарь и игроков;
- поставить/сломать блок, заполнить или очистить область;
- процедурно построить структуру, дом или blueprint;
- выдать предмет/опыт, эффекты, изменить health/hunger/saturation;
- телепортировать, менять игровой режим, время и погоду;
- призвать моба;
- устанавливать spawn point;
- отправлять сообщения в чат;
- ограниченно выполнять vanilla Minecraft commands;
- сохранять и удалять per-chat memory.

Полный справочник инструментов находится в `PROJECT.md`.

### Хранение
- `minecraft_ai_agent.json` — provider и provider API keys.
- `minecraft_ai_agent_history.json` — история чатов.
- `minecraft_ai_agent_memory.json` — память по чатам.
- `minecraft_ai_agent.log` — tool logs с ротацией.

## Что в репозитории устарело

README.txt в проверенной 1.20.1 ветке всё ещё описывает 1.21.1, backtick key, локальный Python backend на 8765/8787 и модель gpt-5.6-luna. Но фактические Java-файлы 1.20.1:
- открывают панель Right Shift;
- выполняют прямые HTTP API запросы из Java-клиента;
- хранят provider/key в config/minecraft_ai_agent.json;
- работают с локальным integrated server.

Папки `backend/` и `scripts/` остаются в репозитории, но активный Java flow их не вызывает. До отдельной проверки/перепроектирования не считать запуск backend обязательным для актуальной версии.

## История коммитов 1.20.1 port

12 сентября 2026:
- `e8a1c914` — port build config;
- `4ba9c0ec` — project versions for 1.20.1;
- `6f126809` — metadata port;
- `30cdcdee` — enable 1.20.1 branch builds;
- `dd568c41` — restore AI Agent source from main for 1.20.1 port;
- `3f08551e` — build-only workflow;
- `530c2f63` — remove temporary CI workflow;
- `85568e18` — fix Screen.renderBackground override signature.

GitHub Actions:
- ранний port commit `530c2f63...` получил failure, run [34706949487](https://github.com/Mortylovelly/adastra-create-addon/actions/runs/34706949487);
- последний commit `85568e18...` прошёл, run [34707123137](https://github.com/Mortylovelly/adastra-create-addon/actions/runs/34707123137), artifact `minecraft-ai-agent-1.20.1`.

## GitHub rename

Владелец пожелал переименовать репозиторий в `Minecraft-AI-Agent`, чтобы старое имя Ad Astra/Create не вводило в заблуждение. Переименование не было выполнено этой интеграцией: доступные GitHub actions не предоставляют операцию rename repository. Пока старый URL остаётся рабочим. Владелец может переименовать repo через GitHub → Settings → General → Repository name. После этого нужно обновить ссылку в этом PROJECT.md и при необходимости metadata `fabric.mod.json`.

## Что не подтверждено

- Подробные старые игровые тесты до портирования.
- Проверка текущей реализации в реальном singleplayer после последней сборки.
- Поддержка dedicated server.
- Корректность/доступность указанных provider model IDs на текущую дату.
- Полное назначение всех старых Python scripts, потому что текущая ветка не подключает их к AI runtime.

Следующий этап истории — только после реальных изменений/теста: создать update 002.
