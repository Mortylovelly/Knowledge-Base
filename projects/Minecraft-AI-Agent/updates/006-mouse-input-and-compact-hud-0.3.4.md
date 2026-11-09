# 006 — Mouse input, compact HUD 120 × 70 и корректировка AI panel text (0.3.4)

Дата: 2026-11-09

## Исходное состояние

Пользователь протестировал JAR 0.3.3 и сообщил:
- кнопки Logs нажимались нестабильно, hover/курсор дёргался;
- drag/resize практически не работали;
- стартовый HUD всё ещё слишком большой;
- подписи в AI panel наезжали друг на друга;
- курсор уже управляется отдельным модом пользователя по клавише G.

## Изменения в активном репозитории

Repository: [Mortylovelly/Minecraft-AI-Agent](https://github.com/Mortylovelly/Minecraft-AI-Agent)

Рабочая ветка: `minecraft-1.20.1-fabric` (Minecraft 1.20.1 / Fabric / Java 17). Линия `main` 1.21.1 не изменялась.

Текущий HEAD: `853b29c24d2b175791367972f65f49caa53249c3`.

Коммиты:
- `f5a1740a17bc89a559c40c2e13d947a0f21d2bb2` — первичная корректировка HUD/mouse, layout и version bump 0.3.4.
- `165897acf55f62bc5fd85a45bf596359ea428039` — обратное преобразование координат с учётом open/close scale/offset анимации.
- `853b29c24d2b175791367972f65f49caa53249c3` — README поясняет использование внешнего cursor-toggle.

Изменённые файлы:
- `src/main/java/com/mortylovelly/minecraftai/client/AiLogOverlay.java`
- `src/main/java/com/mortylovelly/minecraftai/mixin/client/MouseMixin.java`
- `src/main/java/com/mortylovelly/minecraftai/client/AiChatScreen.java`
- `gradle.properties` — `mod_version=0.3.4`
- `README.txt`

## Что исправлено в исходниках

1. Начальный размер Logs HUD уменьшен с 360 × 210 до **120 × 70 GUI pixels**.
2. Для узких/низких размеров добавлена компактная раскладка: короткий LOGS title, небольшие кнопки COPY / collapse / close и ограниченный preview строк логов. При увеличении окна возвращаются обычная шапка, фильтры и счётчики.
3. Исправлена координата Y: используется GUI origin сверху, без прежнего переворота Y.
4. Обработчик мыши больше не вызывает `unlockCursor()` или `lockCursor()` при открытии, tick, закрытии и возврате из AI screen. Режим курсора остаётся под контролем отдельного пользовательского мода (клавиша G).
5. MouseMixin обрабатывает HUD input только при отсутствии Screen и когда курсор уже свободен; клики, drag и scroll переводятся из window coordinates в scaled GUI coordinates.
6. Добавлено обратное преобразование pointer coordinates для scale/vertical offset анимации HUD, чтобы hitboxes соответствовали визуальному положению панели во время открытия/закрытия.
7. В AI panel статус, chat ID и memory label обрезаются по реальной доступной ширине, чтобы не пересекаться друг с другом. Заголовок ограничен областью до кнопок управления.
8. История/архив логов, COPY, фильтры и цветовая схема не переписывались намеренно; это изменение ввода и layout.

## CI / сборка

Последний проверенный JAR остаётся **0.3.3**:
- Actions run [42682628073](https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/42682628073) — success для HEAD `e23ef96bfeeadd17c20258eae46edcc93103a0e6`.
- Artifact ID `13702743060`, основной JAR `minecraft-ai-agent-0.3.3.jar`.

Для HEAD 0.3.4:
- новый GitHub Actions run не появился;
- временный PR [#3](https://github.com/Mortylovelly/Minecraft-AI-Agent/pull/3) использовался только для проверки pull_request trigger, run не был создан; PR закрыт без merge;
- локальный git clone/build не выполнен: среда сборки не смогла разрешить `github.com` (DNS/network error).

Следовательно, 0.3.4 — source update, **не подтверждённая сборка**. Не переименовывать старый JAR 0.3.3 в 0.3.4 и не выдавать его за новый artifact.

## Журнал Minecraft от пользователя

Представленный фрагмент показывает:
- Minecraft 1.20.1;
- Fabric Loader 0.19.5;
- Java 17;
- загружен `minecraft_ai_agent 0.3.3`;
- в просмотренных строках отсутствуют `MouseMixin`, `AiLogOverlay` и ошибки применения нашего mixin.

В том же логе видны сообщения других компонентов: Faunify entity missing attributes, Mojang authentication 401, Man of Many Planes malformed/empty recipe JSON, а также предупреждения ModernFix/Distant Horizons. Они не являются доказательством неисправности HUD AI Agent.

## Runtime status

Не подтверждено в реальной игре:
- кнопки, hover и плавность курсора;
- использование внешнего G mouse toggle;
- перетаскивание и resize;
- стартовый 120 × 70 compact HUD и переход к расширенному виду;
- AI panel text spacing;
- анимация и звуки после изменения hitboxes.

Следующий обязательный шаг — запустить Actions build exact HEAD `853b29c24d2b175791367972f65f49caa53249c3`, получить JAR 0.3.4 и только затем повторить проверку в Minecraft.