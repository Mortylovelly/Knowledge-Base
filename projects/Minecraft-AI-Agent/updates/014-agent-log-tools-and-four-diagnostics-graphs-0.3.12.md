# 014 — Четыре графика и подключение AI Agent к логам/телеметрии (0.3.12)

Дата: 2026-11-10

## Запрос

Добавить GPU и CPU диаграммы, заменить диаграмму ping на TPS, не переделывать остальной дизайн панели, а главное — предоставить самому AI Agent доступ к журналу Logs HUD и к значениям/историям графиков для анализа производительности и ответа на вопросы о происходящем.

## Что изменилось в панели

- Сохранён существующий стиль диаграмм, оформление панели, расположение текстовых показателей и поведение шестерёнки/переключателя DIAGNOSTICS.
- PING остался текстовым показателем.
- Диаграмма ping заменена на TPS.
- Всего теперь четыре диаграммы с той же отрисовкой и компактным размером, сетка 2×2: FPS и TPS в первой строке, GPU и CPU во второй.
- Нижняя область диагностики увеличена ровно настолько, чтобы добавить второй ряд диаграмм; остальная панель не менялась.

## Интеграция AI Agent с Logs HUD

Добавлены два client-local tool в общий список функций `AiAgentService`, используемый стандартным циклом tool calling Gemini, DeepSeek, Groq и OpenRouter.

### `search_agent_logs`

- Ищет по трём удерживаемым файлам архива Logs HUD: `logs/minecraft-ai-agent/latest.log`, `latest.log.1`, `latest.log.2`.
- Поиск по умолчанию идёт от новых строк к старым.
- Фильтры: текст/имя logger, уровень `all/error/warning/info/debug/trace`, только `[CUSTOM]`.
- Есть ограничение количества результатов и offset pagination для чтения старых результатов.
- Максимальное окно хранения — до трёх файлов по 4 MiB (около 12 MiB совокупно). Результат включает число найденных и просмотренных строк; модель получает только совпадения/текущую страницу, а не безграничный сырой dump.
- Применяется redaction известных форматов API keys/tokens/passwords до возврата результатов.

Область доступа намеренно точная: AI Agent читает именно архив, который собирает Logs HUD. Это **не** произвольный file browser, не прямое чтение vanilla Minecraft `logs/latest.log` и не сбор всех логов сторонних клиентских модов. Строки с сервера попадут в архив только при работе существующего Log4j bridge/subscription; прежнее требование OP2 остаётся.

### `get_agent_diagnostics`

Возвращает live telemetry и четыре истории графиков:
- текущий FPS, средний FPS и максимум;
- ping, server TPS, средний server tick interval;
- GPU/CPU utilization, RAM used/total/percent;
- framebuffer resolution;
- состояние потока логов и счётчики TOTAL/WARN/ERROR;
- массивы FPS, TPS, GPU, CPU: по 52 выборки, oldest-to-newest, приблизительный шаг 500 ms.

Unavailable readings передаются как `-1`, это неизвестное значение, а не нулевой процент. GPU percent по-прежнему зависит от доступного NVIDIA `nvidia-smi`; CPU/RAM отражают системные показатели ОС.

Tool selector добавляет эти инструменты к вопросам о логах, предупреждениях, ошибках, crashes, лаге, FPS/TPS, GPU/CPU/RAM и общих вопросах вроде «что происходит на сервере?». Для широкого troubleshooting он может получить сразу и логи, и метрики, чтобы сопоставить временные метки ошибок с просадками графиков.

Сбор FPS продолжает работать при открытом AI chat screen. Системный CPU/RAM/GPU мониторинг не выключается вместе с визуальным переключателем DIAGNOSTICS; агент может запрашивать метрики, даже когда пользователь не показывает графики.

Логовые строки в system instruction помечены как недоверенные диагностические данные: агент должен анализировать их, но не принимать их текст за инструкции или команды.

## Изменённые файлы

- `AiLogOverlay.java` — четыре графика FPS/TPS/GPU/CPU, ping оставлен текстом; увеличена только нижняя диагностика.
- `AiDiagnostics.java` — четыре history buffer и сбор метрик независимо от переключателя HUD.
- `AiAgentService.java` — tools `search_agent_logs` и `get_agent_diagnostics`, intent selection, увеличенные лимиты результата для диагностических вызовов и инструкции по безопасному анализу log data.
- `AiLogFile.java` — поиск по трём архивным файлам, filters и offset pagination.
- `MinecraftAiAgentClient.java` — сбор FPS продолжается и при открытом Minecraft screen/AI chat.
- `gradle.properties` — версия 0.3.12.
- `README.txt` — описание графиков и новых возможностей AI diagnostics.

## Commit и сборка

Repository: https://github.com/Mortylovelly/Minecraft-AI-Agent

Branch: `minecraft-1.20.1-fabric`; Minecraft 1.20.1 / Fabric / Java 17.

Commit / exact HEAD: `08d76b629327d349b87876a10f885b0da4240668`.

GitHub Actions run #315: https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/42903464260 — completed / success для exact HEAD.

Artifact ID: `13803357879`, name `minecraft-ai-agent-1.20.1`, ZIP 261770 bytes, SHA256 `cc88cda3eb9a06679319a73461701fa235cecf7c2b5245a4cd8b8a3adffa39ce`.

JAR: `minecraft-ai-agent-0.3.12.jar`, 186630 bytes, SHA256 `45fade0581b3ddfa7e5903a5251d796a8f9d2fc7f92cc764c3fd042ba0f042a2`.

JAR metadata: Minecraft 1.20.1, Java >=17. Проверены классы `AiAgentService`, `AiDiagnostics`, `AiLogFile`, `AiLogOverlay`, `AiServerDiagnostics`, `MinecraftAiAgentClient`. Bytecode checks: четыре диаграммы; оба agent tools; archive search/offset; четыре массива метрик.

## Runtime status

Сборка и статическая проверка JAR успешны. Реальный запуск Minecraft с 0.3.12 ещё не выполнен. Проверить отображение четырёх диаграмм и пропорциональное масштабирование, работу обоих tools во всех провайдерах, постраничный поиск старых логов, redaction, чтение telemetry при выключенном HUD switch и диагностическое объяснение ошибок. Отдельно проверить лог bridge на dedicated server с OP2 и понимать границы архива: Vanilla/third-party client logs сами по себе в него не добавляются.

