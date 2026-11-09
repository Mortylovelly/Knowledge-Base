# 008 — Свободный курсор по клавише Э и возврат режима после ESC (0.3.6)

Дата: 2026-11-09

## Исходный запрос

Пользователь попросил встроенный free-cursor toggle: включение указателя отдельной клавишей, управление мышью без вращения камеры, использование курсора в HUD, временная передача управления обычным Minecraft Screen при открытии ESC/других экранов и автоматическое восстановление режима после возврата в мир. Повторное нажатие клавиши должно отключать свободную мышь.

## Проверка SeasonalDayCycle

Repository: [Mortylovelly/seasonaldaycycle](https://github.com/Mortylovelly/seasonaldaycycle), текущий `main` HEAD `81ea83e64c3b7b2b32546085f6e6e17c244f6ba6`.

В текущем исходнике `MixinMinecraft` вызывает только `ClientTimeInterpolator.onRenderTick`; найденный `ClientSetup` содержит обработчики цикла времени. Текущий tree не содержит G-key binding или кода cursor unlock/toggle. Поэтому переносить текущую реализацию G было невозможно; функция реализована независимо в Minecraft AI Agent. Репозиторий SeasonalDayCycle не изменялся.

## Реализация в Minecraft AI Agent

- Repository: [Mortylovelly/Minecraft-AI-Agent](https://github.com/Mortylovelly/Minecraft-AI-Agent).
- Рабочая ветка: `minecraft-1.20.1-fabric` (Minecraft 1.20.1 / Fabric / Java 17).
- Версия: `0.3.6`.
- Commit с free cursor toggle: `33311f027c179287bc90e630a752c6a8b45ea227`.
- Commit с исправлением native pointer coordinates для hover Logs HUD: `51204558b823ec5c6237243758d855fdfbe714cf`.
- HEAD: `51204558b823ec5c6237243758d855fdfbe714cf`.

### Поведение

1. KeyBinding `key.minecraft_ai_agent.toggle_free_cursor` зарегистрирован с default `GLFW_KEY_APOSTROPHE` — физическая клавиша **Э** русской раскладки (рядом с Enter). Клавишу можно переназначить в Options → Controls → Minecraft AI Agent. Она не конфликтует с E / Inventory.
2. Нажатие Э в мире вызывает `Mouse.unlockCursor()`; повторное нажатие — `Mouse.lockCursor()`. В action bar выводится состояние.
3. Пока free cursor включён, mouse movement не передаётся управлению камерой; mouse buttons/scroll не проходят в игровые действия и не ломают/ставят блоки, не меняют hotbar. Logs HUD продолжает принимать свои клики, drag/resize и scroll.
4. При открытом ESC/pause/inventory/options Screen курсором управляет обычный Minecraft Screen. Состояние free-cursor toggle сохраняется; после закрытия Screen в мир Agent повторно вызывает `unlockCursor()`, если Minecraft снова захватил мышь.
5. Повторное нажатие Э в мире выключает режим и возвращает стандартный захват мыши.
6. MouseMixin использует native GLFW cursor mode и native pointer coordinates; hover состояния header buttons Logs HUD тоже считываются напрямую из GLFW, так как mouse callback перехватывается для защиты от поворота камеры.
7. При disconnect свободный режим сбрасывается.

Изменённые файлы:
- `src/main/java/com/mortylovelly/minecraftai/client/AiFreeCursor.java` — новый контроллер режима.
- `src/main/java/com/mortylovelly/minecraftai/client/MinecraftAiAgentClient.java` — регистрация и обработка keybinding.
- `src/main/java/com/mortylovelly/minecraftai/mixin/client/MouseMixin.java` — блокировка игровых mouse actions и camera look в free-cursor mode.
- `src/main/java/com/mortylovelly/minecraftai/client/AiLogOverlay.java` — raw pointer для hover кнопок.
- `src/main/resources/assets/minecraft_ai_agent/lang/ru_ru.json` и `en_us.json` — названия клавиши и actionbar сообщений.
- `README.txt` — инструкции для free cursor.
- `gradle.properties` — `mod_version=0.3.6`.

## CI / build / artifact

- Actions run [42689710703](https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/42689710703), run #307: `completed / success`, exact HEAD `51204558b823ec5c6237243758d855fdfbe714cf`.
- Build и Upload mod jar steps успешны.
- Artifact ID: `13704804539`, archive `minecraft-ai-agent-1.20.1`, 233066 bytes, SHA256 `a211f43e1a0d7a3723235710b1a9ccdbeca756ab383bfc583a778af101cbdef0`.
- JAR: `minecraft-ai-agent-0.3.6.jar`, 166970 bytes, SHA256 `b545bcd4db48170ad6e15df1888a59afc9ee360f7dbab57c0fb3878784dc0aed`.
- Metadata verified: version `0.3.6`, Minecraft `1.20.1`, Java `>=17`, Fabric API `>=0.92.11+1.20.1`.
- JAR contains `AiFreeCursor.class`, `AiLogOverlay.class`, `MinecraftAiAgentClient.class`, `MouseMixin.class`, `minecraft_ai_agent.mixins.json` и `minecraft-ai-agent-refmap.json`.

## Runtime status

Actions build and JAR contents are verified. **Real in-game runtime test is still pending.** Test in Minecraft 1.20.1 Fabric:
- press Э: cursor becomes visible, camera stops following pointer, status message indicates enabled;
- mouse clicks and scroll do not break/place blocks or change hotbar while free cursor is active;
- click/drag/resize/COPY/filter/scroll in Logs HUD;
- open pause menu with ESC while enabled, use the menu normally, close it and confirm free cursor returns;
- open/close AI Screen with Right Shift while enabled and confirm return behavior;
- press Э again and confirm normal mouse capture returns;
- open Options → Controls and verify binding is listed/rebindable;
- disconnect from world while enabled and ensure state resets;
- test with other cursor-related mods disabled first, then with the user's previous G-key mod, to detect possible double-toggle conflicts.

Do not treat successful compilation as proof of runtime behavior.