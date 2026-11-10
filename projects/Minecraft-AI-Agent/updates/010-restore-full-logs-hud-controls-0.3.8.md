# 010 — Восстановление полноценного Logs HUD: кнопки, фильтры и счётчики (0.3.8)

Дата: 2026-11-10

## Проблема

После установки 0.3.7 пользователь подтвердил, что G/free cursor и перемещение мыши работают, но Logs HUD открывался без привычной полной панели: не было видимых трёх фильтров и счётчиков с Minecraft-предметами; кнопки заголовка отображались лишь в урезанном компактном варианте.

## Причина

В `AiLogOverlay.java` размер по умолчанию был `120 × 70`. Метод `compact()` переключал layout в compact, если ширина меньше 240 или высота меньше 140. В compact-ветке render рисовал только LOGS, упрощённые C / + / × и строки логов. Ветки `drawStats()` и `drawTabs()` вызывались только в полном layout. Эти методы и счётчики фактически оставались в исходниках, но default size постоянно скрывал их.

## Исправление 0.3.8

Repository: [Mortylovelly/Minecraft-AI-Agent](https://github.com/Mortylovelly/Minecraft-AI-Agent)  
Branch: `minecraft-1.20.1-fabric`  
Minecraft 1.20.1 / Fabric / Java 17

- Размер панели по умолчанию изменён с 120 × 70 на **360 × 210 GUI-пикселей**.
- Минимальные границы полного режима подняты до **240 × 140** (при меньшем доступном viewport bounds ограничиваются экраном).
- Вертикальный предел изменения размера увеличен до 75% высоты GUI, чтобы полный layout помещался также при высоком GUI scale. Ширина по-прежнему ограничена половиной GUI.
- В полном режиме снова видны кнопки **COPY**, свернуть и закрыть.
- Вернулись все три фильтра: **Logs**, **Custom logs**, **Errors**.
- Вернулись три карточки счётчиков: total (изумруд), warning (светопыль), error (редстоун).
- Иконки не заменены нарисованными символами: используется `DrawContext.drawItem(new ItemStack(item), ...)` для ванильных предметов Minecraft.
- Compact fallback оставлен только для viewport, который физически не может вместить полную компоновку.
- G free cursor и `MouseMixin` не изменялись; текущая рабочая обработка свободного указателя сохранена.

## Изменённые файлы

- `src/main/java/com/mortylovelly/minecraftai/client/AiLogOverlay.java` — размер по умолчанию и минимальные/максимальные bounds.
- `README.txt` — описание полноценной панели.
- `gradle.properties` — `mod_version=0.3.8`.

## Commit и CI

- Commit: [`a19cfd96be0c4de4cddc8d0ff435ce2c06300aba`](https://github.com/Mortylovelly/Minecraft-AI-Agent/commit/a19cfd96be0c4de4cddc8d0ff435ce2c06300aba)
- HEAD ветки `minecraft-1.20.1-fabric`: `a19cfd96be0c4de4cddc8d0ff435ce2c06300aba`.
- Actions run [42883023714](https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/42883023714), run #309: `completed / success`.
- Job `build`, шаги `Build` и `Upload mod jar`: `success`.
- Artifact ID: `13795396525`, ZIP 233398 bytes, SHA256 `7877a4cd9ed883956d9086071b571880ebac8c0d9869b906c22d24d43305f837`.
- JAR: `minecraft-ai-agent-0.3.8.jar`, 167183 bytes, SHA256 `aeb690d4033f6faca77cf9030827d034c137b9c984ebe046cc7267ae17b24c53`.
- Fabric metadata: version `0.3.8`, Minecraft `1.20.1`, Java `>=17`, Fabric API `>=0.92.11+1.20.1`.
- JAR содержит `AiLogOverlay.class`, `MouseMixin.class`, `MinecraftAiAgentClient.class`, free cursor classes, mixin config и refmap.
- Bytecode проверен через `javap`: static defaults — width 360 и height 210; render вызывает `drawStats` и `drawTabs`; `drawStat` строит ItemStack и вызывает native Minecraft DrawContext item renderer; методы счётчиков используют total/warning/error count.

## Runtime status

GitHub Actions build и JAR verification успешны. **Реальный тест в Minecraft ещё не выполнен.** Пользователю нужно заменить JAR 0.3.7 на 0.3.8 и проверить:
1. Панель открывается в полном виде 360 × 210 (или доступный размер, ограниченный viewport).
2. Видны COPY / collapse / close.
3. Видны вкладки Logs / Custom logs / Errors, каждая переключает ленту.
4. Видны три счётчика с ванильными предметами emerald / glowstone dust / redstone и меняющимися значениями.
5. Drag / resize / scroll / hover не ломают свободную мышь G.
6. Открытие ESC/AI Screens и возврат в мир не ломают cursor mode.

Compile success и bytecode verification не являются подтверждением in-game runtime-поведения.
