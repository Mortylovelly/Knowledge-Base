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

- GitHub Actions run #15 / ID 46179181658: запущен для exact commit выше.
- Build/test и artifact upload: статус будет зафиксирован по фактическому завершению Actions.
- JAR/ZIP и SHA256: добавить после скачивания и проверки артефакта.
- Полный запуск в пользовательском Minecraft-модпаке: pending.

## Первое применение

1. Поставить 0.6.0 на тестовый Fabric 1.20.1 профиль, предварительно убрав предыдущий Optimization Fix JAR.
2. В мире выполнить `/optimizationfix profile start 60` либо `/optimizationfix profile retained 60`.
3. Играть обычным сценарием во время записи; не форсировать GC.
4. Забрать .jfr и .txt из `logs/optimizationfix/profiles`.
5. Для дальнейшего разбора приложить `logs/latest.log` и `logs/minecraft-ai-agent/custom/latest.log`.
