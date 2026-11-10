# 012 — Исправление невидимого clipping в уменьшенной Logs HUD (0.3.10)

Дата: 2026-11-10

## Симптом

Пользователь сообщил: при уменьшении Logs HUD вся панель и текст визуально масштабируются правильно, но чем меньше становится окно, тем сильнее невидимая граница закрывает строки логов; остаётся видна только небольшая часть.

## Причина

`AiLogOverlay.render()` рисовал log content под общим `MatrixStack` scale, но дополнительно вызывал `DrawContext.enableScissor(x+8, ...)` и `disableScissor()`. На Minecraft 1.20.1 этот GUI scissor rectangle не компоновался с пользовательским transform панели. В результате clip box оставался в другой системе координат и при уменьшении панели становился фиксированной невидимой стенкой.

## Исправление 0.3.10

Repository: https://github.com/Mortylovelly/Minecraft-AI-Agent
Branch: minecraft-1.20.1-fabric
Commit: https://github.com/Mortylovelly/Minecraft-AI-Agent/commit/81e031ca7cb936ee4ef2ba2101dcdb125061a817
Version: 0.3.10 / Minecraft 1.20.1 / Fabric / Java 17

- Удалены `enableScissor` и `disableScissor` из render пути log content.
- Безопасная область теперь контролируется логикой вывода: `updateCache()` переносит строки по логической ширине панели, а render loop берёт только `visibleLines` из CACHED_LINES.
- Фон и текст лога остаются под тем же общим scale transform; кнопки, фильтры, счетчики, collapsed strip и resize не менялись.
- `MouseMixin`, `AiFreeCursor` и G binding не менялись.
- README обновлён, версия gradle.properties — 0.3.10.

## Build и проверка артефакта

- GitHub Actions run #312: https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/42891079487 — completed / success.
- Exact HEAD: 81e031ca7cb936ee4ef2ba2101dcdb125061a817.
- Artifact ID: 13799370169, ZIP 233525 bytes, SHA256 2ac548865098d056dcd523d3e7d4b9d8ba555b2d20fd8bc4dbeab2eac63a3d41.
- JAR `minecraft-ai-agent-0.3.10.jar`, 166931 bytes, SHA256 c44026b6c1087effe65a079dbd592e69522a49d1a2ccba6e62ce4731fc7be4f1.
- JAR metadata: Minecraft 1.20.1, Java >=17, Fabric API >=0.92.11+1.20.1.
- В JAR присутствуют AiLogOverlay, AiFreeCursor, MinecraftAiAgentClient, MouseMixin, mixin config и refmap.
- `javap -c -p AiLogOverlay` проверен: `enableScissor`/`disableScissor` отсутствуют; вызовы `drawStats`/`drawTabs` и `appendWrapped` присутствуют.

## Runtime status

GitHub Actions и проверка JAR успешны. Реальный in-game runtime test ещё не выполнен. Пользователю проверить уменьшение панели минимум до 25% и убедиться, что строки логов не перекрываются и scroll продолжает работать. Затем проверить collapsed strip, resize, filters, G free cursor и click handling.

Сборка подтверждает компиляцию и содержимое JAR, но не заменяет проверку непосредственно в Minecraft.