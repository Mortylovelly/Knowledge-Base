# 013 — Встроенная диагностика Logs HUD (0.3.11)

Дата: 2026-11-10

## Запрос

Заменить кнопку сворачивания Logs HUD на шестерёнку настроек. Добавить простой экран настроек с одним переключателем диагностики. При включении показывать компактную статистику внизу той же панели, без отдельного окна; весь интерфейс должен оставаться масштабируемым.

## Что изменено

- Кнопка «−» и режим узкой свернутой полосы удалены.
- Шестерёнка переключает основную Logs HUD и экран SETTINGS. Повторное нажатие возвращает к логам.
- Экран SETTINGS пока содержит только переключатель DIAGNOSTICS ON/OFF.
- При включении диагностика занимает нижнюю часть той же панели, под основным содержимым, счётчиками и вкладками. Существующая общая система transform/resize масштабирует весь блок пропорционально.
- В диагностике: PING (мс), server TPS, средний интервал серверного тика (мс), FPS сейчас, AVG и MAX, маркер AVG ON, framebuffer resolution, GPU/CPU/RAM, XYZ, dimension.
- Внизу два компактных графика истории FPS и ping, выборки примерно каждые 500 мс.
- Цветовые пороги FPS: меньше 30 красный; 30–59 жёлтый; 60+ зелёный.
- Ping: до 80 мс зелёный; до 160 мс жёлтый; выше красный.
- TPS: 19+ зелёный; 15–18.9 жёлтый; ниже 15 красный. Средний интервал тика: до 50 мс зелёный; до 65 мс жёлтый; выше красный.
- CPU/GPU/RAM: до 60% зелёный; до 85% жёлтый; выше красный.
- FPS, ping, resolution и system resource information вычисляются на клиенте. CPU/RAM — показатели всей системы.
- GPU utilization пока читается через локальный NVIDIA `nvidia-smi`. При отсутствии доступного NVSMI показывается `—`, а не случайный или выдуманный процент.
- Server TPS и средний интервал тика рассчитываются на сервере по интервалам между началами последовательных тиков; сервер отправляет два значения клиентам примерно два раза в секунду. Для этих двух метрик мод 0.3.11 должен быть установлен на обеих сторонах соединения.

## Изменённые файлы

- `src/main/java/com/mortylovelly/minecraftai/client/AiLogOverlay.java` — шестерёнка, экран настроек, переключатель, нижняя область диагностики, метрики и графики, геометрия панели с учётом увеличенной высоты.
- `src/main/java/com/mortylovelly/minecraftai/client/AiDiagnostics.java` — новый клиентский сбор FPS/ping/system metrics, история для графиков и локальное чтение NVIDIA utilization.
- `src/main/java/com/mortylovelly/minecraftai/client/AiAgentNetwork.java` — получение серверного diagnostic packet и сброс метрик при disconnect.
- `src/main/java/com/mortylovelly/minecraftai/client/MinecraftAiAgentClient.java` — инициализация и сбор клиентских метрик.
- `src/main/java/com/mortylovelly/minecraftai/server/AiServerDiagnostics.java` — новый server-side sampler реальных интервалов между тиками и отправка TPS/tick ms.
- `src/main/java/com/mortylovelly/minecraftai/MinecraftAiAgent.java` — регистрация server diagnostics.
- `gradle.properties` — версия 0.3.11.
- `README.txt` — описание новой панели и ограничений диагностики.

## Commits и сборка

Repository: https://github.com/Mortylovelly/Minecraft-AI-Agent

Branch: `minecraft-1.20.1-fabric` (Minecraft 1.20.1 / Fabric / Java 17).

Feature commit: `ad44fe7ff38979d7408a33e34fc8c1df97356b0f`.

Final follow-up commit / exact HEAD: `708297c594af3dce64537e20b06a059678ec19ac`. В follow-up поправлено измерение TPS: вместо длительности callback используется интервал между началом текущего и предыдущего server tick.

GitHub Actions run #314: https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/42896146890 — completed / success для exact final HEAD.

Artifact ID: `13800697149`, name `minecraft-ai-agent-1.20.1`, ZIP 252076 bytes, SHA256 `98a5884bd69255758bd74240ca04106d06a1e0d016f0cf7a4874694035b80c2d`.

JAR: `minecraft-ai-agent-0.3.11.jar`, 180586 bytes, SHA256 `c23a8271439bd6f8842756d4b312a8d87e3091b15770d8ecc4d4c6082bb9cfbf`.

JAR проверен: metadata 0.3.11, Minecraft 1.20.1, Java >=17; присутствуют `AiDiagnostics`, `AiServerDiagnostics`, `AiLogOverlay`, `AiAgentNetwork`, `MinecraftAiAgentClient`, `MinecraftAiAgent`. Bytecode inspection подтвердил sampling интервалов тиков, отправку серверных значений, CPU/RAM MXBean и optional `nvidia-smi`.

## Runtime status

Реальный запуск Minecraft с этой сборкой ещё не выполнен. Build success и статическая проверка JAR подтверждают компиляцию и наличие кода, но не доказывают отсутствие UI/runtime ошибок.

Проверить в игре: gear/settings toggle; единственный diagnostics switch; возврат по gear; размещение блока под вкладками в той же панели; пропорциональный resize, в том числе минимальный масштаб; обновление FPS/AVG/MAX, ping/TPS/tick ms; графики; доступность GPU на NVIDIA системе; integrated и dedicated server с версией мода 0.3.11 с обеих сторон.

