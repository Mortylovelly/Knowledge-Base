# 005 — Независимый Logs HUD и обновление интерфейса 0.3.3

Дата: 9 ноября 2026  
Проект: Minecraft AI Agent  
Целевая платформа: Minecraft 1.20.1 Fabric / Java 17  
Рабочая ветка: \`minecraft-1.20.1-fabric\`

## Исходная проблема

Владелец сообщил, что предыдущее окно Logs:
- открывалось слишком большим, почти во весь экран;
- имело слишком маленький диапазон масштабирования;
- красило все строки в зелёный цвет, включая предупреждения и ошибки;
- выглядело полупрозрачным и визуально неровным;
- закрывалось вместе с \`AiChatScreen\` по Escape;
- всегда рисовалось поверх AI-панели;
- не имело аккуратной анимации и звукового отклика кнопок.

## Подтверждённая причина связанного жизненного цикла

В предыдущем коде \`AiLogOverlay.render()\` вызывался непосредственно из \`AiChatScreen.render()\`; мышиные события Logs также перехватывались методами самого \`AiChatScreen\`. Кроме того, \`AiChatScreen.close()\` вызывал \`AiLogOverlay.hide()\`. Поэтому лог-панель зависела от существования AI-экрана и не могла быть самостоятельной HUD-панелью.

Предыдущая версия использовала стартовый размер 430 × 310, а ограничения размера не были привязаны к половине экрана. Обработчик вкладок располагался раньше проверки resize-зоны, поэтому нижний правый угол мог перехватываться вкладкой.

## Что изменено в версии 0.3.3

### Независимый HUD

- Отрисовка Logs перенесена на \`HudRenderCallback\`.
- HUD отображается только когда нет активного Minecraft Screen, поэтому он больше не лежит поверх AI-панели, меню или чата.
- Кнопка **Logs** в AI-панели открывает HUD и запускает плавное закрытие AI-экрана.
- При закрытии \`AiChatScreen\` через Escape лог-панель остаётся открытой в игровом HUD.
- При повторном открытии AI HUD временно скрывается; после закрытия AI снова появляется, если пользователь сам не закрыл Logs.
- Добавлен \`MouseMixin\` для обработки кликов, перетаскивания и прокрутки отдельной HUD-панели при отсутствии другого экрана.

### Размер и внешний вид

- Первоначальный размер: **360 × 210 GUI-пикселей**.
- Изменение размера ограничено половиной ширины и высоты GUI-экрана.
- Resize-zone проверяется до вкладок; перетаскивание выполняется за заголовок.
- Фон полностью непрозрачный, серо-графитовый; края слегка закруглены.
- AI-панель также получила непрозрачную серую подложку, небольшое скругление и короткую анимацию появления/закрытия.
- Лог-панель получает короткую анимацию открытия/закрытия и небольшой scale/fade эффект.

### Цвета и звуки

- INFO — зелёный.
- WARN/WARNING — жёлтый.
- ERROR/FATAL/CRITICAL — красный.
- DEBUG — синий.
- TRACE — приглушённый серый.
- Добавлена нормализация вариантов названий severity до применения цвета.
- Кнопки стандартного AI GUI используют стандартный Minecraft UI click sound.
- Для HUD controls добавлен \`AiUiSound\` на базе \`SoundEvents.UI_BUTTON_CLICK\`; pitch отличается для открытия, закрытия и обычного нажатия.

### Сохранённые функции

В 0.3.3 сохранены:
- три вкладки Logs / Custom logs / Errors;
- счётчики total / warning / error;
- прокрутка и перенос строк;
- COPY всего дискового архива, включая ротационные файлы;
- архив \`logs/minecraft-ai-agent/latest.log\`, \`.1\`, \`.2\`;
- маскирование типовых ключей и токенов;
- требования OP 2 для просмотра серверного потока.

## Изменённые файлы репозитория

Ветка \`minecraft-1.20.1-fabric\`:
- \`src/main/java/com/mortylovelly/minecraftai/client/AiLogOverlay.java\` — самостоятельный HUD, размеры, resize/drag, цвета отображения, скругления, анимация.
- \`src/main/java/com/mortylovelly/minecraftai/client/AiChatScreen.java\` — анимация AI-экрана; Logs открывает HUD и закрывает AI; HUD не рисуется поверх AI.
- \`src/main/java/com/mortylovelly/minecraftai/client/AiLogClient.java\` — нормализация уровней и цветовая палитра.
- \`src/main/java/com/mortylovelly/minecraftai/client/MinecraftAiAgentClient.java\` — HUD renderer и tick integration.
- \`src/main/java/com/mortylovelly/minecraftai/client/AiUiSound.java\` — звуковые эффекты UI.
- \`src/main/java/com/mortylovelly/minecraftai/mixin/client/MouseMixin.java\` — mouse input независимого HUD.
- \`src/main/resources/minecraft_ai_agent.mixins.json\` — mixin config.
- \`src/main/resources/fabric.mod.json\` — подключение mixin config.
- \`src/main/java/com/mortylovelly/minecraftai/MinecraftAiAgent.java\` — строка версии.
- \`gradle.properties\` — mod_version 0.3.3.
- \`README.txt\` — описание поведения и размера Logs HUD.

## Коммиты и Actions

- Основной redesign commit: \`e1e62fdf97ab2bdb325c0b1d151ef541698fffd0\`.
- Первая сборка 0.3.3: run [42681580115](https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/42681580115) — failure из-за передачи \`SoundEvents.UI_BUTTON_CLICK\` (Reference<SoundEvent>) в метод, ожидавший SoundEvent.
- Исправление звука: commit \`934812330045c73275eddc007d635e86947131eb\`, вызов использует \`.value()\`; run [42681832149](https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/42681832149) — success.
- Промежуточный commit с размерами/resize: \`27e6a0954de189ab6967ffb3735a44067f05835f\`; его tree ошибочно был построен от старой основы и вернул прежнюю версию AiUiSound; run [42682033214](https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/42682033214) — failure по той же ошибке.
- Восстановление corrected sound поверх актуального дерева + финальная компактная панель: commit \`9a98f66af642af528fcc406346b1a16a4add09db\`.
- Actions run [42682270795](https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/42682270795) — completed / success для source commit `9a98f66...`.
- Final exact-HEAD run [42682628073](https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/42682628073) — completed / success; job `build`, `Build` и `Upload mod jar` — success для `e23ef96bfeeadd17c20258eae46edcc93103a0e6`.
- README-only commit `e23ef96bfeeadd17c20258eae46edcc93103a0e6` синхронизировал размер в документации с 360 × 210; Actions run `42682628073` подтвердил успешную сборку exact HEAD.

## Проверка артефакта

Actions artifact:
- Final artifact ID: `13702743060`.
- Имя: \`minecraft-ai-agent-1.20.1\`.
- ZIP: 224661 bytes.
- ZIP SHA256: `0b3b9cc450c3e79b3d43b6e70b8e1a5d77feeeb25089595ecd482f88307a81ad`.
- Основной JAR: \`minecraft-ai-agent-0.3.3.jar\`, 161714 bytes.
- JAR SHA256: \`a9a0636394cc9f8363f6d35fccfafa22a0a08d851652444a23233058e02085ce\`.

Извлечённый JAR проверен:
- \`fabric.mod.json\`: version 0.3.3; Minecraft 1.20.1; Java >=17; Fabric API >=0.92.11+1.20.1.
- присутствуют \`AiLogOverlay.class\`, \`AiUiSound.class\`, \`MouseMixin.class\`;
- присутствуют \`minecraft_ai_agent.mixins.json\` и \`minecraft-ai-agent-refmap.json\`.

## Статус тестирования

**Подтверждено:** код сохранён в активной ветке; build и upload artifact успешны на source commit; JAR существует и его metadata/classes/mixin config/refmap проверены.

**Не проверено в Minecraft:** запуск с реальной игрой, применение MouseMixin при запуске клиента, появление и возврат HUD после Escape, drag/resize, фактические цвета на разных Log4j levels, звуки, COPY и интеграция с dedicated server. Успешная компиляция не является runtime-тестом.

## Следующий тест в игре

1. Удалить старые JAR AI Agent из \`mods\` и поставить один \`minecraft-ai-agent-0.3.3.jar\` на клиент и Fabric-сервер.
2. Зайти в мир/на сервер (для серверного потока логов необходим OP 2).
3. Нажать Right Shift, затем Logs: AI должен закрыться с анимацией, Logs — остаться в игре.
4. Нажать Right Shift, затем Escape: AI должен закрыться, Logs — вернуться, не перекрывая AI.
5. Проверить стартовый размер 360 × 210, перетаскивание заголовка и resize за нижний правый угол; размер не должен превышать половины экрана.
6. Проверить INFO/WARN/ERROR/FATAL/DEBUG colors, вкладки, COPY, закрытие X, анимации и click sounds.
7. Повторить на integrated singleplayer и dedicated Fabric server, сверить клиентский и серверный лог, отсутствие ошибок mixin/crash и некорректного click-through.

