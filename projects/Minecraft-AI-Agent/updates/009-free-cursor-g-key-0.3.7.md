# 009 — Переключение свободного курсора на G (0.3.7)

Дата: 2026-11-09

## Исходный запрос

Пользователь сообщил, что предыдущая клавиша Э не работает, и попросил назначить свободную мышь на G.

## Изменения

Repository: [Mortylovelly/Minecraft-AI-Agent](https://github.com/Mortylovelly/Minecraft-AI-Agent)  
Активная ветка: `minecraft-1.20.1-fabric`  
Minecraft 1.20.1 / Fabric / Java 17  
Версия: `0.3.7`

- Default binding изменён с `GLFW_KEY_APOSTROPHE` на `GLFW_KEY_G`.
- Подписи привязки обновлены: `G` в `ru_ru.json` и `en_us.json`.
- README указывает G как клавишу по умолчанию.
- Добавлена одноразовая миграция: если у пользователя сохранилась предыдущая привязка `GLFW_KEY_APOSTROPHE` от 0.3.6, при первом client tick она заменяется на `toggleFreeCursorKey.getDefaultKey()` (G), обновляется карта привязок через `KeyBinding.updateKeysByCode()` и сохраняется через `client.options.write()`. Другие сохранённые клавиши не сбрасываются.
- Поведение самого free cursor, MouseMixin и Logs HUD не менялось.

## Изменённые файлы

- `src/main/java/com/mortylovelly/minecraftai/client/MinecraftAiAgentClient.java`
- `src/main/resources/assets/minecraft_ai_agent/lang/ru_ru.json`
- `src/main/resources/assets/minecraft_ai_agent/lang/en_us.json`
- `README.txt`
- `gradle.properties` — `mod_version=0.3.7`

## Commit и CI

- Commit: [`de26b52dd65392c8834f02c68821567e25c4e91d`](https://github.com/Mortylovelly/Minecraft-AI-Agent/commit/de26b52dd65392c8834f02c68821567e25c4e91d)
- HEAD активной ветки: `de26b52dd65392c8834f02c68821567e25c4e91d`.
- Actions run [42690986065](https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/42690986065), run #308: `completed / success`.
- Build и Upload mod jar steps: `success`.
- Artifact ID: `13706533403`, archive `minecraft-ai-agent-1.20.1`, 233356 bytes, SHA256 `b16002643643048f933156407f0ecd64fbe2447f3c90dbf0b26046e7c670f374`.
- JAR: `minecraft-ai-agent-0.3.7.jar`, 167174 bytes, SHA256 `f0252cd05fbd1c8ca114a9a2cb16aba8cc3bc6cd2e3d2935738bf52f85268b36`.
- JAR metadata проверены: version `0.3.7`, Minecraft `1.20.1`, Java `>=17`, Fabric API `>=0.92.11+1.20.1`.
- В JAR присутствуют `AiFreeCursor.class`, `AiLogOverlay.class`, `MinecraftAiAgentClient.class`, `MouseMixin.class`, mixin config и refmap. Bytecode подтверждает default keycode 71 (GLFW G) и код миграции старого keycode 39 (apostrophe).

## Runtime status

GitHub Actions build и JAR verification успешны. **Проверка непосредственно в игре ещё не выполнена.**

После установки 0.3.7 проверить:
1. Нажатие G включает свободный курсор, камера не вращается.
2. Повторное нажатие G возвращает обычный захват мыши.
3. Если в `options.txt` осталась старая привязка Э от 0.3.6, она автоматически заменяется на G после запуска клиента.
4. ESC и другие Minecraft Screens продолжают работать со своим курсором; после закрытия экрана свободный курсор восстанавливается.
5. Logs HUD принимает clicks/drag/resize/scroll/COPY, а игровые действия не проходят сквозь свободный курсор.
6. Новую привязку можно изменить через Options → Controls → Minecraft AI Agent.

Успешная сборка не является подтверждением runtime-поведения.
