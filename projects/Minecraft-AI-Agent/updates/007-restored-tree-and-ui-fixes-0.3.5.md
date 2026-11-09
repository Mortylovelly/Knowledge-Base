# 007 — Восстановление полного дерева, внешний G-cursor и UI fixes (0.3.5)

Дата: 2026-11-09

## Рабочая ветка и исправление структуры Git

- Repository: [Mortylovelly/Minecraft-AI-Agent](https://github.com/Mortylovelly/Minecraft-AI-Agent)
- Активная ветка: `minecraft-1.20.1-fabric` (Minecraft 1.20.1 / Fabric / Java 17).
- `main` (1.21.1) не изменялась.
- До исправления ветка указывала на неполное Git tree из 14 entries: отсутствовали `build.gradle`, Gradle Wrapper, большая часть исходников, Fabric resources и `.github/workflows/build.yml`.
- В коммите `10c0ccf05f377435f6eb5c668bffe644befad66f` полное дерево восстановлено из последнего целого состояния 0.3.3 (63 entries), затем наложены обновлённые исходники 0.3.5.
- Следующий коммит `7968e4dd8d6b535fb374c031cf9ef8d69aaf53fc` исправил Java compile errors: явные `(int)` casts для `Math.round(double)` в четырёх местах drag/resize.

## Изменения 0.3.5

1. Logs HUD сохраняет стартовый размер 120 × 70 GUI pixels и компактный режим.
2. MouseMixin больше не опирается на `client.mouse.isCursorLocked()`. Он проверяет реальный GLFW cursor mode, считывает позицию курсора через `glfwGetCursorPos` и пересчитывает window coordinates в scaled GUI coordinates один раз.
3. При работе внешнего cursor-toggle мода по клавише G Agent не вызывает `lockCursor()` или `unlockCursor()`; режим указателя остаётся под управлением внешнего мода.
4. Пока курсор свободен и HUD активен, движение указателя не передаётся управлению камерой; scroll не меняет выбранный hotbar slot.
5. Drag и resize используют координаты с компенсацией анимации HUD; исправлены приведения `long` к `int` для координат/размеров.
6. AI panel адаптирует расположение action controls и provider buttons при узкой ширине; provider buttons переносятся в два ряда, статус/ID чата/память занимают отдельные обрезаемые колонки, заголовок не заезжает на action buttons.
7. Полная структура проекта и workflow GitHub Actions восстановлены.

## GitHub Actions и артефакты

- Commit HEAD: [7968e4dd8d6b535fb374c031cf9ef8d69aaf53fc](https://github.com/Mortylovelly/Minecraft-AI-Agent/commit/7968e4dd8d6b535fb374c031cf9ef8d69aaf53fc).
- Actions run [42687782242](https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/42687782242), run #305: `completed / success`; steps Build и Upload mod jar успешны.
- Промежуточный run #304 (`42687558262`) выявил четыре compile errors из-за отсутствующих int casts; эти ошибки исправлены в коммите выше.
- Artifact ID: `13706335574`, archive `minecraft-ai-agent-1.20.1`, 229662 bytes, SHA256 `4b68b0973d4ce7a228936be199fd2eca36efa999cbea6369a18bb99f58eada14`.
- JAR: `minecraft-ai-agent-0.3.5.jar`, 164874 bytes, SHA256 `70c527ffc288a604da7ed26683fb1cce6c1074a7a1e010a72c5277345e51e83d`.
- JAR metadata verified: version `0.3.5`, Minecraft `1.20.1`, Java `>=17`, Fabric API `>=0.92.11+1.20.1`. Present: `AiLogOverlay.class`, `AiChatScreen.class`, `MouseMixin.class`, `minecraft_ai_agent.mixins.json`, `minecraft-ai-agent-refmap.json`.

## Runtime status

Build and JAR contents are verified. Actual in-game runtime behavior is **not yet verified**. The next test should cover:
- G-key cursor toggle with Logs open: buttons click reliably, pointer does not jitter and does not rotate the camera;
- drag from HUD title and resize from bottom-right corner;
- initial compact 120 × 70 window, expanding/collapsing, scrolling and COPY;
- no overlapping AI panel title, action buttons, provider controls, status, chat ID and memory labels;
- ESC / Right Shift transitions, animations and sounds;
- integrated singleplayer and dedicated Fabric server (same JAR on client/server; OP2 for server log stream).

Do not treat successful compilation as proof of the runtime test.