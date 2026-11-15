# 001 — Повторяющаяся ошибка Big Globe: недопустимый диапазон случайной высоты

- **Дата:** 2026-11-15 (времена в логе 18:25:10–18:26:00; timezone не подтверждён).
- **Проект:** Big Globe — генерация мира и datapacks.
- **Версия / ветка Minecraft:** предполагаемый контекст 1.20.1 Fabric / Java 17 по общему стандарту базы; точные версии в данном фрагменте не показаны.
- **Версии модов:** Big Globe, Distant Horizons и C2ME точными номерами не указаны.
- **Статус:** частично; семантика исключения установлена, конкретные значения traits и первопричина не установлены.

## Контекст

Владелец сообщил, что игра в целом работает хорошо, но в логе многократно появляются ошибки Big Globe Script с интервалом около пяти секунд. Некоторые стеки показывают обычный путь генерации через C2ME, другие — вызов генерации features со стороны Distant Horizons.

Ранее владелец сообщал о datapack, меняющем структуру гор, а также об отдельных изменениях высоты/масштаба мира. Точные файлы прежнего datapack в Knowledge Base на момент записи не обнаружены.

## Симптом

Ключевые строки:

    [ERROR] [Big Globe/Scripting] Exception was:
    java.lang.IllegalArgumentException: bound must be greater than origin
    at java.base/jdk.internal.util.random.RandomSupport.checkRange(RandomSupport.java:232)
    at java.base/java.util.random.RandomGenerator.nextInt(RandomGenerator.java:679)
    at builderb0y.scripting.parsing.ScriptParser$Generated_940.generate(Script:6)
    at knot//builderb0y.bigglobe.features.dispatch.ScriptedFeatureDispatcher$Holder.generate(...)

Проблемная часть исходника:

    int y = min(
        random.nextInt(world_traits.bigglobe:min_y(x, z), world_traits.bigglobe:core_max_y(x, z)),
        random.nextInt(world_traits.bigglobe:lava_y(x, z), world_traits.bigglobe:core_max_y(x, z))
    )

Далее скрипт проверяет, входит ли блок в #bigglobe:molten_rocks, и устанавливает bigglobe:hidden_lava или minecraft:lava[level=0] в зависимости от окружающих блоков.

## Исследование

- RandomGenerator.nextInt(origin, bound) требует, чтобы верхняя граница была строго больше нижней.
- Поэтому в момент ошибки хотя бы для одного вызова core_max_y(x, z) не превосходил origin (min_y либо lava_y).
- Скрипт исполняется внутри Big Globe ScriptedFeatureDispatcher.
- В стеке встречаются C2ME и Distant Horizons, поскольку они участвуют в вызовах генерации чанков / LOD.
- Репозиторий Knowledge-Base прочитан: в прежнем PROJECTS_INDEX отсутствовал отдельный проект Big Globe/datapack; поэтому создан отдельный паспорт и журнал этой работы.
- README и паспорт SkyLimitless подтверждают, что это отдельный мод высоты, а не сам datapack Big Globe.
- Репозиторий extremedifficulty не является подтверждённым datapack: просмотренные последние commits относятся к MobAIHandler.java.

Не проверены: координаты x/z; численные min_y/lava_y/core_max_y; исходный datapack и его JSON; Big Globe preset/config/traits; точные версии модов; последствия для чанков и DH LOD.

## Причина

**Подтверждено:** один из диапазонов nextInt(origin, bound) при выполнении некорректен: bound <= origin.

**Вероятная причина, не подтверждена:** несогласованные Big Globe height traits на части координат после изменений генерации/высоты мира.

**Не доказано:** что виноваты C2ME или Distant Horizons. Наличие их классов в стеке не означает, что они задают неправильный диапазон.

## Изменения

| Файл | Что изменено | Зачем |
|---|---|---|
| Knowledge Base: projects/BigGlobe-Worldgen/PROJECT.md | Создан паспорт проекта | Сохранить назначение, ограничения и текущее состояние |
| Knowledge Base: projects/BigGlobe-Worldgen/updates/README.md | Создан индекс истории | Не терять нумерацию записей |
| Knowledge Base: эта запись | Зафиксирована ошибка и степень уверенности выводов | Не повторять диагностику и не путать гипотезы с фактами |

Игровой datapack, моды и сохранение мира не изменялись.

## Коммиты и сборки

- Коммиты исходного datapack/mod: не установлены.
- GitHub Actions: не запускался.
- Build/artifact: отсутствуют; это документационная запись.

## Проверка в игре/на сервере

Владелец сообщил, что игра работает в целом хорошо, но ошибки повторяются. После исправления теста не было, потому что игровые файлы не менялись.

## Инструкции и риски

- Не удалять C2ME или Distant Horizons только из-за присутствия их классов в стеке.
- Не считать сохранение повреждённым по одному этому фрагменту.
- Сделать резервную копию мира до будущих изменений datapack/worldgen.
- Повторные исключения означают, что конкретный feature вызов не выполняется штатно на некоторых вызовах. Точный эффект на terrain и LOD пока не установлен.

## Открытые вопросы и следующий шаг

1. Установить точное расположение/репозиторий/commit datapack, который ранее менял горы.
2. Получить актуальные Big Globe traits и world preset/config.
3. Найти координаты и значения, из-за которых core_max_y оказывается меньше или равен min_y либо lava_y.
4. Только после чтения полной структуры скрипта и механизма datapack override решить, исправлять ли сами traits или добавлять корректный fallback.
5. Проверить новую генерацию и DH LOD, сохранив ранее одобренную форму гор.

## Связанная запись

- Паспорт: [PROJECT.md](../PROJECT.md)