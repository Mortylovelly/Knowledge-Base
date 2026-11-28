# 006 — Optimization Fix 0.6.0: GC Heap Usage Trend

Дата: 28.11.2026  
Minecraft: 1.20.1 Fabric / Java 17  
Repo: https://github.com/Mortylovelly/Fix-test  
Branch: main  
Commit: `a15a4e39264423cf8b5555d522b39edfe7796349`

## Что изменено

- Новая bounded модель `GcHeapTrend` для JFR-событий `jdk.GCHeapSummary`.
- Строки `Before GC` и `After GC` связываются по одному `gcId`, а не по порядку чтения.
- Хранится максимум 128 последних GC ID.
- В отчёт добавлены: before/after rows, число полных GC пар, first/latest post-GC heap usage, знаковая дельта, минимум и максимум.
- Тренд есть в allocation и retained режимах, так как оба уже включают GCHeapSummary.
- Нет дополнительного периодического server-tick опроса: агрегация идёт при разборе JFR-файла worker thread.
- Не добавлено forced GC, heap dump, chunk unload или изменение world state.

## Тесты

- Pairing событий с перемешанным порядком.
- Неполные пары не объединяют разные GC ID.
- Ограничение истории 128 GC ID.
- Отбрасывание неверных GC ID, отрицательного heapUsed и неизвестной фазы.
- JFR availability test Java 17 теперь дополнительно проверяет `jdk.GCHeapSummary`.

## Границы интерпретации

`jdk.GCHeapSummary.heapUsed` показывает общее использование heap в фазе события. Это не old-generation-only live set. Молодые, mixed и full GC могут давать разные post-GC значения. Положительная дельта первого/последнего значения в одном коротком профиле не доказывает утечку. Сопоставлять несколько профилей при одинаковой нагрузке и GC-паттерне.

## CI / артефакт

- GitHub Actions run #15 / ID 46179181658: **success** для exact commit выше.
- JDK 17, Gradle build, JUnit tests и artifact upload: success; workflow завершился `BUILD SUCCESSFUL`.
- Artifact ID 15276872650; ZIP `optimization-fix-0.6.0-artifact.zip`, 97,287 bytes, SHA256 `262fad5c655b8645e48119cc3fa76afb943dd61c7a59525b821a7f746fb0f0fd`.
- Main JAR `optimization-fix-0.6.0.jar`, 70,387 bytes, SHA256 `accd616e9dca7dc91d9d395fc9c9e73a57c01844110f0fa23433885801db2e35`.
- Sources JAR `optimization-fix-0.6.0-sources.jar`, 36,604 bytes, SHA256 `fa6637248fb5635187ddb6c8b8931b4085f8b159f08240c1d97d91ec273473d3`.
- ZIP скачан и локально проверен; metadata и наличие классов `GcHeapTrend` / `JfrMemoryProfiler` подтверждены.
- Полный запуск в пользовательском Minecraft-модпаке: pending.

## Первое применение

1. Поставить 0.6.0 на тестовый Fabric 1.20.1 профиль, предварительно убрав предыдущий Optimization Fix JAR.
2. В мире выполнить `/optimizationfix profile start 60` либо `/optimizationfix profile retained 60`.
3. Играть обычным сценарием во время записи; не форсировать GC.
4. Забрать .jfr и .txt из `logs/optimizationfix/profiles`.
5. Для дальнейшего разбора приложить `logs/latest.log` и `logs/minecraft-ai-agent/custom/latest.log`.
