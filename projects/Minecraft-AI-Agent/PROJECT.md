# Minecraft AI Agent

## Кратко

- **Актуальное название проекта:** Minecraft AI Agent.
- **Старое имя/ошибочный исторический label:** Ad Astra Create Addon; больше не отражает назначение проекта.
- **Текущий GitHub URL:** [Mortylovelly/adastra-create-addon](https://github.com/Mortylovelly/adastra-create-addon).
- **Желаемое новое имя GitHub-репозитория:** `Minecraft-AI-Agent`. Сам GitHub rename ещё не выполнен: доступные инструменты интеграции не имеют операции переименования репозитория. Код и документация пока остаются в старом slug.
- **Mod ID:** `minecraft_ai_agent`.
- **Название в Fabric metadata:** `Minecraft AI Agent`.
- **Maven group:** `com.mortylovelly.minecraftai`.
- **Archive name:** `minecraft-ai-agent`.
- **Активная версия для нашей работы:** Minecraft 1.20.1 Fabric, Java 17.
- **Активная ветка:** `minecraft-1.20.1-fabric`.
- **mod_version:** `0.2.2`.
- **Последний известный commit ветки:** `85568e18c7eab3dc1055e0ba3e2434e0c8d17d6a`.
- **Последний известный успешный build:** GitHub Actions run [34707123137](https://github.com/Mortylovelly/adastra-create-addon/actions/runs/34707123137), success для того же SHA.
- **Проверка в игре после последнего билда:** в доступном контексте не подтверждена.

## Что это за мод

Это самостоятельный Fabric-мод с ИИ-агентом, встроенным в интерфейс Minecraft. Игрок открывает панель чата прямо в игре, пишет запрос обычным русским языком, а модель может не только отвечать текстом, но и вызывать зарегистрированные инструменты для действий в мире.

Мод **не является аддоном для Create или Ad Astra**. В активном build.gradle нет зависимостей Create/Ad Astra; используются только Minecraft, Yarn mappings, Fabric Loader и Fabric API. Старое имя репозитория осталось от предыдущего назначения, поэтому проект нужно переименовать в GitHub, когда владелец сможет выполнить это действие в Settings.

## Текущая целевая версия и ветки

| Ветка | Minecraft | Java | Статус |
|---|---|---|---|
| `minecraft-1.20.1-fabric` | 1.20.1 | 17 | **Активная и единственная рабочая ветка по текущему стандарту** |
| `main` | 1.21.1 | 21 | Неактивная; не трогать без отдельного разрешения владельца |
| `ci-test-1201` | тестовая 1.20.1 | требует сверить | Историческая тестовая ветка; CI завершался failure, не использовать |

Наличие `main` с Minecraft 1.21.1 не означает, что её следует использовать. Владелец установил долгосрочное правило: сейчас вся разработка ведётся под Minecraft 1.20.1 Fabric / Java 17. Переход на другую версию возможен только по прямому указанию владельца.

## Техническая конфигурация активной ветки

Проверено в `minecraft-1.20.1-fabric`:
- Minecraft: `1.20.1`
- Yarn mappings: `1.20.1+build.10`
- Fabric Loader: `0.17.2`
- Fabric API: `0.92.11+1.20.1`
- Fabric Loom: `1.9.2`
- Gradle Wrapper: `8.12`
- Java compile/source/target: `17`
- mod_version: `0.2.2`
- mod id: `minecraft_ai_agent`
- root project: `minecraft-ai-agent`

Fabric metadata:
- `main`: `com.mortylovelly.minecraftai.MinecraftAiAgent`
- `client`: `com.mortylovelly.minecraftai.client.MinecraftAiAgentClient`
- environment: `*`
- requires Fabric Loader >=0.17.2, Minecraft 1.20.1, Java >=17, Fabric API >=0.92.11+1.20.1.

## Как игрок открывает мод

- Клавиша по умолчанию: **Right Shift**.
- Client initializer регистрирует keybinding `key.minecraft_ai_agent.open_panel`.
- Открывается экран `AiChatScreen`.
- В панели можно писать запросы на русском языке.

**Несоответствие README:** README.txt в ветке до сих пор утверждает, что экран открывается клавишей backtick (`), но фактический `MinecraftAiAgentClient.java` регистрирует `GLFW_KEY_RIGHT_SHIFT`. Следовать коду, пока README отдельно не обновлён.

## Команды Minecraft

В текущем `MinecraftAiAgent.java` зарегистрирована одна команда:

```text
/aiagent status
```

Она отвечает текстом `Minecraft AI Agent: direct in-game AI service`.

Других команд мода в проверенном entrypoint нет. Инструмент `run_minecraft_command` — это отдельный AI tool, а не отдельная команда мода.

## Интерфейс панели

В активном `AiChatScreen` доступны:
- поле API key выбранного провайдера;
- кнопка сохранения API key;
- выбор Gemini / DeepSeek / Groq / OpenRouter;
- проверка подключения;
- Новый чат;
- Очистить историю текущего чата;
- поле сообщения и Отправить;
- Стоп для отмены активного запроса;
- статус выполнения;
- показ предыдущих сообщений текущего чата.

Очистка истории чата не удаляет долговременную память: экран явно сообщает, что история очищена, а память чата сохранена.

## Поставщики ИИ и API keys

Текущий Java-код выполняет HTTP-запросы к провайдерам непосредственно из Minecraft-клиента через `java.net.http.HttpClient`. Настраиваемые providers:
- Gemini (по умолчанию)
- DeepSeek
- Groq
- OpenRouter

Модели, заданные константами в текущем коде:
- Gemini: `gemini-3.7-flash`
- DeepSeek: `deepseek-v4-flash`
- Groq: `openai/gpt-oss-20b`
- OpenRouter: `google/gemma-4-26b-a4b-it:free`

Это значения текущего исходника, а не гарантия, что каждый провайдер или alias остаётся доступен у поставщика API. Если подключение ломается, сначала проверять актуальный код, модель/provider и конкретную ошибку API.

Путь настроек:
`config/minecraft_ai_agent.json`

Ключи:
- `provider`
- `deepseek_api_key`
- `groq_api_key`
- `openrouter_api_key`
- `gemini_api_key`

API keys хранятся локально в конфигурации пользователя. Не публиковать этот файл, не вставлять реальные ключи в GitHub и не отправлять их в чат/логи.

## Память, история, логи

| Файл | Назначение | Ограничения в коде |
|---|---|---|
| `config/minecraft_ai_agent_history.json` | История отдельных чатов и Gemini interaction IDs | До 120 сообщений и 60 000 символов на чат |
| `config/minecraft_ai_agent_memory.json` | Долговременные заметки AI по чатам | До 80 заметок, до 500 символов каждая; в prompt до 5 000 символов |
| `config/minecraft_ai_agent.log` | TOOL CALL и другие записи AI Agent | При размере >1 000 000 байт переименовывается в `minecraft_ai_agent.log.old` |
| `config/minecraft_ai_agent.json` | provider и API keys | Содержит секреты — не публиковать |

Новый чат получает отдельный chat ID. Очистка истории удаляет сообщения выбранного чата, но не очищает автоматически память.

## Возможности AI tools

Текущий `AiAgentService.createTools()` и `executeTool()` регистрируют следующие инструменты:

### Осмотр мира
- `get_player_state` — координаты, здоровье, голод, режим, измерение, направление.
- `observe_world` — позиция/поворот игрока, время, погода, близкие блоки/поверхность/сущности.
- `inspect_region` — точный список блоков в локальном объёме.
- `get_block` — блок по координатам.
- `find_entities` — поиск сущностей рядом.
- `list_players` — список игроков.

### Строительство и изменение блоков
- `build_structure` — процедурная постройка из primitive частей через `AiBuildTool`.
- `build_house` — компактный дом с фундаментом, стенами, окнами, входом, крышей и дымоходом.
- `build_blueprint` — чертёж из fill/hollow/clear/set/line/roof_gable.
- `set_block` — поставить один блок.
- `fill_area` — fill/hollow/clear в кубоиде.
- `break_block` — сломать один блок.
- `clear_area` — очистить сферу/куб около точки.
- `locate_structure` — найти деревню, крепость и другие структуры.

### Игрок и инвентарь
- `give_item` — выдать предмет; поддерживает count, enchantments, custom_name и unbreakable.
- `get_inventory` — прочитать инвентарь.
- `clear_inventory` — очистить инвентарь.
- `apply_effect` — применить или снять эффект.
- `set_player_stats` — здоровье, голод, насыщение.
- `give_experience` — выдать уровни или очки опыта.
- `teleport_player` — телепортация по координатам, при желании в другое измерение.
- `set_gamemode` — survival/creative/adventure/spectator.
- `set_spawnpoint` — установить точку возрождения.
- `spawn_entity` — призвать зарегистрированную сущность.

### Мир и общение
- `set_time` — день/ночь/полдень/полночь.
- `set_weather` — clear/rain/thunder.
- `send_chat` — отправить `[AI]` сообщение всем онлайн-игрокам.
- `run_minecraft_command` — выполнить обычную команду из allowlist.
- `remember_memory` / `forget_memory` — сохранить/удалить долговременную заметку.

Команда `run_minecraft_command` ограничена корневыми командами из списка в исходнике. В коде разрешены: advancement, attribute, clear, clone, damage, data, difficulty, effect, enchant, execute, experience, fill, function, gamemode, give, item, kill, locate, loot, particle, place, playsound, recipe, ride, say, schedule, setblock, spawnpoint, spreadplayers, summon, tag, team, teleport, tellraw, time, title, trigger, weather, xp, spectate. Команды `op`, `ban`, `stop`, `whitelist` не находятся в allowlist.

## Ограничения и безопасность исполнения

- В текущем коде `runOnServer()` получает сервер через `MinecraftClient.getInstance().getServer()`. Значит AI tools работают через локальный integrated server одиночного мира. Это не реализация для dedicated server: если локального сервера нет, tool вернёт ошибку.
- Один запрос ограничен 4 раундами tool calls и 8 вызовами инструментов.
- `MAX_PLACE_BLOCKS=512`, `MAX_BLUEPRINT_OPS=96`, `MAX_BUILD_VOLUME=20000`, `MAX_CLEAR_VOLUME=32768`, `MAX_OBSERVE_RADIUS=10`, `MAX_ENTITY_RESULTS=20`.
- `AiBuildTool`: максимум 40 частей, 60 000 изменённых блоков, горизонтальный предел 96, вертикальный 64; начало постройки обычно 4 блока впереди игрока; локальные координаты: +X вправо, +Y вверх, +Z вперёд.
- Для строительства текущая system instruction просит использовать `build_structure`, а если не указаны стиль/материалы, сначала предложить план и дождаться подтверждения пользователя.
- Результат инструмента должен быть подтверждён tool result; агенту запрещено заявлять об успехе, если tool не подтвердил действие.

## Реальная архитектура активной ветки

`Minecraft Client GUI (AiChatScreen)`
→ `AiAgentService`
→ HTTP request к выбранному provider API
→ function/tool call модели
→ Minecraft integrated server через `server.execute(...)`
→ результат инструмента модели
→ ответ пользователю.

Это подтверждено прямым чтением Java-кода:
- `AiClientConfig` читает API keys из `config/minecraft_ai_agent.json`.
- `AiAgentService` содержит HTTP endpoints, провайдеры, tool schema и диспетчер действий.
- `runOnServer` вызывает сервер клиента (singleplayer integrated server).

## Устаревшие/неподключённые файлы

В репозитории также присутствуют `backend/` и `scripts/` с Python/FastAPI/uvicorn и старой схемой локального backend на портах 8765/8787. Но текущий Java flow активной ветки непосредственно использует HTTP API провайдеров и не вызывает этот локальный backend.

README.txt ошибочно описывает:
- Minecraft 1.21.1 вместо активной ветки 1.20.1;
- запуск Python backend через `backend/start.bat`;
- открытие панели по backtick;
- модель `gpt-5.6-luna`.

Эти сведения не совпадают с реальными файлами активной ветки 1.20.1. Для текущей версии следовать коду: Right Shift, API provider/key в экранной панели, Java direct HTTP. Сам README пока не исправляли; это отдельная документационная задача в репозитории мода.

## Установка и использование (1.20.1)

1. Установить Minecraft 1.20.1 Fabric с Java 17.
2. Собрать мод из правильной ветки или скачать JAR из успешного artifact.
3. Поместить `minecraft-ai-agent-0.2.2.jar` в клиентскую папку `mods`.
4. Запустить singleplayer мир.
5. Нажать Right Shift.
6. Выбрать provider, вставить соответствующий API key, нажать «Сохранить».
7. Нажать «Проверить» для теста подключения.
8. Написать запрос обычным языком.
9. Для прерывания активного задания нажать «Стоп».

Не запускать `backend/start.bat` для текущей Java-реализации, пока архитектура не будет явно изменена.

## Сборка и Actions

Команда локальной сборки:
```bash
./gradlew build --no-daemon
```

Workflow:
`.github/workflows/build.yml`

- Push и pull_request проверяют ветки `main` и `minecraft-1.20.1-fabric`.
- Для активной 1.20.1 ветки workflow использует JDK 17, `chmod +x gradlew`, `./gradlew build --no-daemon`.
- Artifact: `minecraft-ai-agent-1.20.1`.

Последний известный 1.20.1 build:
- commit: `85568e18c7eab3dc1055e0ba3e2434e0c8d17d6a`
- Actions run: [34707123137](https://github.com/Mortylovelly/adastra-create-addon/actions/runs/34707123137)
- result: completed / success
- artifact: `minecraft-ai-agent-1.20.1` (не истёк на момент проверки).

Более ранний port build на SHA `530c2f638c4316f3a57507034698e9dab91dbfe7` завершился failure; затем ошибка renderBackground была исправлена commit `85568e1`, который прошёл успешно. Успешный build не означает полный runtime-тест в игре.

## Известная история разработки по commits

1. На линии 1.21.1 в сентябре 2026 были добавлены процедурный AI builder и high-level build tool; улучшены tool routing и token economy.
2. 12 сентября 2026 AI Agent был перенесён в отдельную ветку Minecraft 1.20.1 Fabric: build config, project versions, metadata, workflow и Java source.
3. CI проверка 1.20.1 сначала падала, затем после исправления сигнатуры `AiChatScreen.renderBackground` последний build прошёл.
4. Точные детали старых разговоров/тестов, не отражённые в GitHub commits, пока не восстановлены.

## Текущее состояние

- **Активная платформа:** Minecraft 1.20.1 Fabric / Java 17.
- **Активная ветка:** `minecraft-1.20.1-fabric`.
- **Последняя известная версия:** 0.2.2.
- **HEAD:** `85568e18c7eab3dc1055e0ba3e2434e0c8d17d6a`.
- **Последняя известная сборка:** run 34707123137, success.
- **Последний игровой тест:** не подтверждён в доступном контексте.
- **Dedicated server:** текущий код не поддерживает AI tools на dedicated server; execution зависит от локального integrated server.
- **GitHub repo rename:** желаемое имя `Minecraft-AI-Agent`, пока не выполнено.
- **Открытые задачи:** обновить README.txt под фактическую 1.20.1 реализацию; после ручного GitHub rename обновить URL в metadata/документации, если потребуется; получить от владельца реальный runtime-тест.
- **Следующая история:** [001 — инвентаризация AI Agent и версий](updates/001-initial-inventory-and-version-lines.md).
