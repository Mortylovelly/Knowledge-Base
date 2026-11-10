# Minecraft AI Agent

## Кратко

- **Актуальное название проекта:** Minecraft AI Agent.
- **Старое имя/ошибочный исторический label:** Ad Astra Create Addon; больше не отражает назначение проекта.
- **Текущий GitHub URL:** [Mortylovelly/Minecraft-AI-Agent](https://github.com/Mortylovelly/Minecraft-AI-Agent).
- **Исторический slug:** `adastra-create-addon`; актуальный slug `Minecraft-AI-Agent` уже подтверждён GitHub API.
- **Mod ID:** `minecraft_ai_agent`.
- **Название в Fabric metadata:** `Minecraft AI Agent`.
- **Maven group:** `com.mortylovelly.minecraftai`.
- **Archive name:** `minecraft-ai-agent`.
- **Активная версия для нашей работы:** Minecraft 1.20.1 Fabric, Java 17.
- **Активная ветка:** `minecraft-1.20.1-fabric`.
- **mod_version:** `0.3.11` (GitHub Actions run #314 completed successfully).
- **Текущий HEAD:** `708297c594af3dce64537e20b06a059678ec19ac` в `minecraft-1.20.1-fabric`.
- **Восстановление структуры:** commit `10c0ccf05f377435f6eb5c668bffe644befad66f` восстановил полный 63-entry tree из последнего целого коммита 0.3.3; последующие изменения применены поверх восстановленного дерева без удаления остальных файлов.
- **UI/mouse 0.3.11:** сохранены G free cursor и масштабируемая Logs HUD 0.3.10. Кнопка сворачивания заменена шестерёнкой: она открывает отдельный экран SETTINGS с единственным переключателем DIAGNOSTICS; повторное нажатие шестерёнки возвращает основную панель. Включённая диагностика добавляется снизу в ту же панель под логами, счётчиками и вкладками; отдельного окна нет, весь блок масштабируется и изменяет размер вместе с панелью. Строки показывают ping, измеренные серверные TPS и средний интервал тика, текущий/средний/максимальный FPS, AVG ON, разрешение, GPU/CPU/RAM, координаты и измерение; внизу отображаются компактные графики FPS и ping.
- **Цвета диагностики:** FPS <30 красный, 30–59 жёлтый, >=60 зелёный; ping <=80 мс зелёный, <=160 мс жёлтый, выше красный; TPS >=19 зелёный, >=15 жёлтый, ниже красный; время тика <=50 мс зелёный, <=65 мс жёлтый, выше красный. Для CPU/GPU/RAM используются пороги <=60% зелёный, <=85% жёлтый, выше красный.
- **Сбор метрик:** FPS и ping берутся на клиенте; server TPS и средний интервал измеряются на сервере по времени между началами последовательных тиков и отправляются совместимому клиенту раз в 10 тиков. Для TPS/tick metrics новая версия мода должна стоять и на сервере, и на клиенте. GPU utilization читается через локальный `nvidia-smi`; если инструмент/драйвер недоступен, выводится `—`, а не придуманный процент. CPU и RAM показывают системную нагрузку/память, а не только процесс Minecraft.
- **Последний успешный build:** run [42896146890](https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/42896146890), run #314, conclusion `success`, exact HEAD `708297c594af3dce64537e20b06a059678ec19ac`.
- **Artifact 0.3.11:** ID `13800697149`, ZIP 252076 bytes, SHA256 `98a5884bd69255758bd74240ca04106d06a1e0d016f0cf7a4874694035b80c2d`; JAR `minecraft-ai-agent-0.3.11.jar`, 180586 bytes, SHA256 `c23a8271439bd6f8842756d4b312a8d87e3091b15770d8ecc4d4c6082bb9cfbf`.
- **JAR metadata/content:** 0.3.11 / Minecraft 1.20.1 / Java >=17; verified `AiDiagnostics`, `AiServerDiagnostics`, `AiLogOverlay`, `AiAgentNetwork`, `MinecraftAiAgentClient` and `MinecraftAiAgent`. Bytecode includes server start-to-start tick interval sampling, sending two diagnostic floats, CPU/memory MXBean metrics and optional `nvidia-smi` support.
- **Проверка в игре:** GitHub Actions build и JAR verification успешны. Runtime-тест в Minecraft ещё не выполнен; это не подтверждает фактический вид интерфейса/корректность поведения на игровом клиенте.

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
- mod_version: `0.3.11`; Actions run 42896146890 completed successfully for HEAD 708297c594af3dce64537e20b06a059678ec19ac; artifact JAR verified. In-game runtime test remains pending.
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

README.txt обновлён для 0.3.2: Right Shift открывает основную панель, кнопка Logs показывает плавающую панель, а COPY копирует сохранённый архив.

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
- отдельная кнопка Logs для плавающей панели серверных логов;
- статус выполнения;
- показ предыдущих сообщений текущего чата.

Начиная с 0.3.3, log panel — независимый HUD, а не дочерняя часть `AiChatScreen`. Кнопка Logs открывает HUD и плавно закрывает AI-экран; после закрытия AI через ESC окно логов остаётся в игре. При повторном открытии AI HUD временно скрывается, чтобы не лежать поверх AI, и снова появляется после закрытия экрана. В 0.3.6 стартовый размер Logs HUD — 120 × 70 GUI-пикселей; в компактном режиме показаны короткий заголовок, кнопки COPY/свернуть/закрыть и строки логов, а вкладки/счётчики доступны после расширения. Перетаскивание — за заголовок; изменение размера — за нижний правый угол; максимум — половина GUI-ширины/высоты. Фон серо-графитовый, углы слегка закруглены, открытие/закрытие анимировано. INFO — зелёный, WARN — жёлтый, ERROR/FATAL — красный, DEBUG — синий, TRACE — приглушённый серый. В 0.3.6 добавлен независимый free-cursor toggle по физической клавише Э. В обычном мире он вызывает `unlockCursor()`; повторное нажатие вызывает `lockCursor()`. Если открыт ESC/pause/options/inventory Screen, курсором управляет стандартный Minecraft Screen; после его закрытия Agent автоматически восстанавливает свободный указатель. В free-cursor mode mouse движение не вращает камеру, кнопки/scroll не проходят в игровые действия, а Logs HUD по-прежнему обрабатывает клики, drag/resize и scroll. GLFW native pointer coordinates используются для hover кнопок, даже если mouse callback перехватывается. Actions build 0.3.6 успешен; реальный runtime-тест ещё не выполнен.

Очистка истории чата не удаляет долговременную память: экран явно сообщает, что история очищена, а память чата сохранена.

## Диагностика Logs HUD (0.3.11)

В версии 0.3.11 кнопка «свернуть» заменена кнопкой-шестерёнкой. Она переключает обычную Logs HUD на отдельный простой экран SETTINGS. На этом экране есть только один переключатель DIAGNOSTICS; повторное нажатие шестерёнки возвращает обычный экран логов. Бывший режим узкой свёрнутой полосы в этой версии удалён.

При включённом переключателе диагностика появляется **в нижней части той же Logs HUD**, под логами, счётчиками и фильтрами. Это не отдельная панель и не popup. Нижняя область входит в общую геометрию: перетаскивание, resize и пропорциональное масштабирование применяются ко всему окну.

Показатели:
- PING — latency игрока из Minecraft player-list entry, мс.
- TPS и TICK — фактически измеряемая частота сервера и средний интервал между началами последовательных тиков в миллисекундах. Сервер передаёт два float примерно дважды в секунду. Оба конца соединения должны использовать мод 0.3.11 или более новую совместимую версию.
- FPS — текущий, средний за сессию после включения диагностики и максимальный; рядом отображается AVG ON.
- RES — разрешение framebuffer Minecraft.
- GPU — текущая загрузка через NVIDIA `nvidia-smi`, если он установлен и доступен локально. Для AMD/Intel или недоступного NVSMI пока выводится `—`.
- CPU и RAM — системные показатели ОС, а не отдельные показатели процесса Java.
- XYZ и DIM — координаты игрока и ID измерения.
- Два компактных графика показывают историю FPS и ping; история обновляется примерно раз в 0,5 секунды.

Цвета: FPS меньше 30 — красный, 30–59 — жёлтый, 60 и выше — зелёный. Ping до 80 мс — зелёный, до 160 мс — жёлтый, выше — красный. TPS от 19 — зелёный, от 15 — жёлтый, ниже — красный. Средний тик до 50 мс — зелёный, до 65 мс — жёлтый, выше — красный. CPU/GPU/RAM до 60% — зелёный, до 85% — жёлтый, выше — красный. Для недоступных показателей отображается `—`.

### Сборка 0.3.11

- Repository: [Mortylovelly/Minecraft-AI-Agent](https://github.com/Mortylovelly/Minecraft-AI-Agent).
- Branch: `minecraft-1.20.1-fabric`; Minecraft 1.20.1 / Fabric / Java 17.
- Feature commit: `ad44fe7ff38979d7408a33e34fc8c1df97356b0f`.
- Final TPS measurement commit / exact HEAD: `708297c594af3dce64537e20b06a059678ec19ac`.
- GitHub Actions run #314: [42896146890](https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/42896146890) — completed / success.
- Artifact ID: `13800697149`; ZIP 252076 bytes; SHA256 `98a5884bd69255758bd74240ca04106d06a1e0d016f0cf7a4874694035b80c2d`.
- JAR: `minecraft-ai-agent-0.3.11.jar`, 180586 bytes; SHA256 `c23a8271439bd6f8842756d4b312a8d87e3091b15770d8ecc4d4c6082bb9cfbf`.
- JAR metadata checked: version 0.3.11, Minecraft 1.20.1, Java >=17. New client/server diagnostics classes and packet paths confirmed in JAR; bytecode inspected for tick interval measurement, packet sending, CPU/RAM and optional NVIDIA GPU lookup.
- Runtime test in Minecraft has not yet been performed. In particular, test the gear/settings switch, diagnostics toggle, chart drawing, resize at minimum scale, and server TPS display with the matching mod installed on both sides.

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
| `logs/minecraft-ai-agent/latest.log`, `.1`, `.2` | Полученный панелью поток серверных Log4j events и локальная диагностика AI Agent | До 4 MiB на каждый из 3 файлов; ротация; common API key/token patterns redacted |
| `config/minecraft_ai_agent_game_chat.jsonl` | Локальный архив полученных игровых сообщений | До 1800 сообщений; только то, что клиент реально получил во время подключения |
| В памяти клиента `AiLogClient` | Экранный буфер панели Logs | До 5000 событий текущего соединения; сбрасывается при JOIN/DISCONNECT; отдельно от постоянного дискового архива |
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

- В версии 0.3.0+ инструменты выполняются на стороне сервера через Fabric networking (AiAgentNetwork → AiAgentServerNetworking → AiAgentServerTools), поэтому архитектура рассчитана на dedicated Fabric server и integrated server. На сервере должен быть установлен тот же JAR.
- Один запрос ограничен 4 раундами tool calls и 8 вызовами инструментов.
- `MAX_PLACE_BLOCKS=512`, `MAX_BLUEPRINT_OPS=96`, `MAX_BUILD_VOLUME=20000`, `MAX_CLEAR_VOLUME=32768`, `MAX_OBSERVE_RADIUS=10`, `MAX_ENTITY_RESULTS=20`.
- `AiBuildTool`: максимум 40 частей, 60 000 изменённых блоков, горизонтальный предел 96, вертикальный 64; начало постройки обычно 4 блока впереди игрока; локальные координаты: +X вправо, +Y вверх, +Z вперёд.
- Для строительства текущая system instruction просит использовать `build_structure`, а если не указаны стиль/материалы, сначала предложить план и дождаться подтверждения пользователя.
- Результат инструмента должен быть подтверждён tool result; агенту запрещено заявлять об успехе, если tool не подтвердил действие.
- Изменяющие мир операции и просмотр серверных логов требуют OP level 2; read-only инструменты доступны без OP.
- Серверный log stream ограничен очередью и размером пакетов; client buffer — 5000 событий. Подписка на логи включается при JOIN и требует OP 2.
- Типовые API key/token patterns маскируются перед передачей логов клиенту.

## Реальная архитектура активной ветки

`Minecraft Client GUI (AiChatScreen)`
→ `AiAgentService`
→ HTTP request выбранному provider API
→ function/tool call модели
→ `AiAgentNetwork` (Fabric C2S packet)
→ `AiAgentServerNetworking`
→ `AiAgentServerTools` на серверном потоке
→ tool result модели
→ ответ пользователю.

Отдельный поток логов:
`Log4j root logger server`
→ `AiAgentServerLogBridge` (queue + batches + OP2)
→ `log_batch` packets
→ `AiAgentNetwork`
→ `AiLogClient` (bounded ring buffer + disk archive)
→ `HudRenderCallback` (только когда нет активного Minecraft Screen)
→ независимый HUD `AiLogOverlay`.
`MouseMixin` передаёт HUD-окну клики, перетаскивание и прокрутку. Когда открыт `AiChatScreen` или другой экран, HUD не рисуется поверх него; после закрытия AI HUD возвращается, если не был закрыт пользователем.

Дополнительно:
- `AiGameChatHistory` локально сохраняет до 1800 полученных сообщений и выдаёт модели только результаты поиска/короткие recent messages.
- `AiAgentLog` отправляет локальные диагностические записи через `AiLogClient`; тот пишет их в `logs/minecraft-ai-agent/latest.log` вместе с полученными логами сервера.
- API keys хранятся только в клиентской конфигурации и не отправляются серверу.

## Устаревшие/неподключённые файлы

В репозитории также присутствуют `backend/` и `scripts/` с Python/FastAPI/uvicorn и старой схемой локального backend на портах 8765/8787. Но текущий Java flow активной ветки непосредственно использует HTTP API провайдеров и не вызывает этот локальный backend.

В репозитории остаются старые каталоги `backend/` и `scripts/` с Python/FastAPI prototype; активная Java реализация 1.20.1 их не использует. README.txt в HEAD 0.3.4 описывает Right Shift, независимый Logs HUD, стартовый размер 120 × 70, компактный режим, resize до половины экрана и использование внешнего cursor-toggle мода. Сборка этой редакции ещё не подтверждена.

## Установка и использование (1.20.1)

1. Установить Minecraft 1.20.1 Fabric / Java 17.
2. Последний подтверждённый JAR — `minecraft-ai-agent-0.3.11.jar` из Actions artifact `minecraft-ai-agent-1.20.1` (run 42896146890; artifact ID 13800697149). JAR version/MC/Java metadata и клиентские/серверные классы диагностики проверены.
3. Перезапустить сервер; для server tools и просмотра логов игроку нужны OP level 2.
4. Нажать Right Shift; настроить provider/API key в клиентской панели.
5. Нажать «Проверить» и отправить запрос.
6. Нажать Logs для просмотра серверных логов.
7. Проверить `/aiagent status`.

Старый Python `backend/start.bat` не требуется для активной Java-реализации.

## Сборка и Actions

Команда локальной сборки:
```bash
./gradlew build --no-daemon
```

Workflow:
`.github/workflows/build.yml`

В workflow добавлен `workflow_dispatch`, чтобы можно было вручную запускать сборку из GitHub Actions UI; обычные push и pull_request triggers сохранены.

- Push и pull_request проверяют ветки `main` и `minecraft-1.20.1-fabric`.
- Для активной 1.20.1 ветки workflow использует JDK 17, `chmod +x gradlew`, `./gradlew build --no-daemon`.
- Artifact: `minecraft-ai-agent-1.20.1`.

Последний подтверждённый build версии 0.3.1:
- commit / HEAD: `12010fe20872f20f20206acefaf7b16ac1d7cebb`
- Actions run: [42667953958](https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/42667953958)
- result: completed / success; Build + Upload artifact successful для exact HEAD
- artifact ID: `13696843616`, `minecraft-ai-agent-1.20.1`, 210665 bytes
- ZIP SHA256: `42006ec6fd98d6f5579575aedbb093cca1639af752169dc8a09bea8918f9e30c`
- JAR size: 151308 bytes; SHA256: `586c0b4a5f6c9b658ae0ab41f887e152e5784500a1f9d0e32cf51cdedc7603b7`.

Первая попытка сборки 0.3.2:
- feature commit: `42b7a0f2e19b79b5a3065d3e444b367e6f3c52ea`;
- run [42672344378](https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/42672344378): `failure`, GitHub вернул 0 jobs и 0 artifacts, лог Gradle отсутствовал; причина — сбой создания/запуска job, а не установленная ошибка компиляции.

Повторная сборка после добавления `workflow_dispatch`:
- build commit / HEAD: `d11654d0b8995211ee23281ec876721a30b94c40` (родитель — feature commit `42b7a0f...`; изменён workflow, исходники 0.3.2 сохранены без изменения);
- run [42676207842](https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/42676207842): `completed / success`;
- job `build`: `success`; шаги `Set up JDK 17`, `Build` и `Upload mod jar` — `success`;
- artifact ID `13699458967`, name `minecraft-ai-agent-1.20.1`, size 216171 bytes;
- extracted JAR: `minecraft-ai-agent-0.3.2.jar`, 155219 bytes, SHA256 `1783d584b769f1edba0c6d8e0f2c12b861fea0a71ef50534800488b65426efbf`.
- Сам JAR проверен: metadata version 0.3.2, Minecraft 1.20.1, Java >=17; присутствует `AiLogFile.class`.

Более ранний port build на SHA `530c2f638c4316f3a57507034698e9dab91dbfe7` завершился failure; затем ошибка renderBackground была исправлена commit `85568e1`, который прошёл успешно. Успешный build не означает полный runtime-тест в игре.

## Известная история разработки по commits

1. На линии 1.21.1 в сентябре 2026 были добавлены процедурный AI builder и high-level build tool; улучшены tool routing и token economy.
2. 12 сентября 2026 AI Agent был перенесён в отдельную ветку Minecraft 1.20.1 Fabric: build config, project versions, metadata, workflow и Java source.
3. CI проверка 1.20.1 сначала падала, затем после исправления сигнатуры `AiChatScreen.renderBackground` последний build прошёл.
4. Точные детали старых разговоров/тестов, не отражённые в GitHub commits, пока не восстановлены.

## Текущее состояние (2026-11-10)

- **Активная платформа:** Minecraft 1.20.1 Fabric / Java 17.
- **Активная ветка:** minecraft-1.20.1-fabric.
- **Текущая версия исходников:** 0.3.11 (GitHub Actions build success; in-game runtime test pending).
- **Последний HEAD:** `708297c594af3dce64537e20b06a059678ec19ac`.
- **Последний подтверждённый build/artifact:** 0.3.11, run [42896146890](https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/42896146890), успешен для exact HEAD `708297c594af3dce64537e20b06a059678ec19ac`.
- **Artifact 0.3.11:** ID `13800697149`, ZIP 252076 bytes, SHA256 `98a5884bd69255758bd74240ca04106d06a1e0d016f0cf7a4874694035b80c2d`; JAR `minecraft-ai-agent-0.3.11.jar`, 180586 bytes, SHA256 `c23a8271439bd6f8842756d4b312a8d87e3091b15770d8ecc4d4c6082bb9cfbf`.
- **0.3.11:** Actions run #314 completed successfully; artifact and JAR verified. Runtime test pending.
- **Основная архитектура:** client AI provider + UI; server networking/tools; server Log4j → log bridge → client disk archive + independent HUD Logs panel.
- **Log panel 0.3.3:** independent HUD; initial 360 × 210; archive/COPY persisted. Этот build собран успешно.
- **Log panel 0.3.10:** same UI as 0.3.9, but log text no longer uses fixed GUI `enableScissor`/`disableScissor` clipping under scaled matrices. Text is line-wrapped to the logical width; render loop draws only the calculated visible rows. This removes the fixed-coordinate clip area that covered most log content at small panel scales. Build verified; runtime pending.
- **Diagnostics HUD 0.3.11:** gear opens SETTINGS, one DIAGNOSTICS toggle enables a bottom-attached monitoring area in the same scalable panel. Includes ping, server TPS/tick ms, FPS current/average/max, resolution, GPU/CPU/RAM, XYZ/dimension and FPS/ping history charts. Server TPS requires matching mod on server and client; GPU percent requires NVIDIA `nvidia-smi`.
- **Чат:** локальный JSONL archive до 1800 сообщений, поиск текста/координат, @AI trigger.
- **NPC/mobs:** создание именованной серверной сущности, навигация follow/move/attack/stop. Полноценное possession с камерой не реализовано.
- **Skin/player NPC:** копирование скина и полноценная fake-player entity не реализованы.
- **Токены:** быстрые локальные команды, selective tools, локальный поиск чата; UI статистики API расходов пока нет.
- **Free cursor 0.3.7:** default G key (`GLFW_KEY_G`); upgrades a saved 0.3.6 apostrophe/Э binding to G once, persists with `options.write()`; other custom key bindings are retained. Unlock/lock cursor toggle, suspend to vanilla Screen and restore on return to world; prevent camera look and world click-through while enabled; Logs HUD remains interactive. Build verified; runtime pending.
- **GitHub repo:** актуальный slug Mortylovelly/Minecraft-AI-Agent; main — неактивная линия 1.21.1 и не является рабочей 1.20.1 веткой.
- **Проверка в Minecraft:** предыдущий log excerpt относится к Minecraft 1.20.1, Fabric Loader 0.19.5, Java 17 и mod 0.3.3; посторонние warnings Faunify/Mojang auth/Man of Many Planes не являются ошибками Agent. Runtime-тест 0.3.11 ещё не выполнен.

### Открытый roadmap

1. Полная система чтения/поиска чата, нормализация координат и структурированная память; текущее ограничение — клиент архивирует только реально полученные сообщения за время подключения.
2. Расширение сообщений с пользовательскими видимыми именами. Системная подпись не является подписанной идентичностью другого игрока; не подделывать authenticated chat.
3. Полноценное possession моба, включая камеру и управление, а не только server navigation.
4. NPC со скином/внешностью/именем выбранного игрока без отдельного входа аккаунта.
5. Расширенный набор инструментов и воспроизводимый test runner для тестирования сторонних модов.
6. Дальнейшая экономия токенов: локальный parser частых команд, compact context, caching, persistent structured memory и usage/cost dashboard.
7. Развитие custom log integration для собственных модов; автоматический приём всех произвольных client-side logger сторонних модов пока не реализован.
8. Runtime-тест 0.3.11: проверить gear → SETTINGS → DIAGNOSTICS ON/OFF → gear back; убедиться, что нижний блок остаётся внутри той же панели и корректно масштабируется/изменяет размер, а графики FPS/ping обновляются. Проверить серверные TPS/tick ms с 0.3.11 на клиенте и сервере, а также поведение GPU на системе с NVIDIA NVSMI. Повторно проверить уменьшенную Logs HUD, scroll, filters, counters, G free cursor, drag, resize, disconnect и COPY.

