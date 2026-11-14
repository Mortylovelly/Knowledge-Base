# 001 — Подтверждена Fabric-линия Vertex / Seasonal Day Cycle для 1.20.1

- **Дата:** 2026-11-14
- **Проект:** seasonaldaycycle / Vertex
- **Линия Minecraft / ветка:** Minecraft 1.20.1 / Fabric, `minecraft-1.20.1-fabric`
- **Версия мода:** `1.1.0-mc1.20.1`
- **Статус записи:** подтверждено по branch, build files, metadata, исходникам и Actions; runtime не проверен

## Контекст

Старый паспорт указывал только историческое сведение о Forge 1.20.1. В репозитории существует отдельная ветка `minecraft-1.20.1-fabric`; проверялась только она. Ветка `fabric-1.21.1` и `main` не исследовались.

README активной ветки всё ещё описывает Fabric 1.21.1 и говорит, что Forge 1.20.1 находится на main. Эти строки не соответствуют фактическим build files/metadata ветки `minecraft-1.20.1-fabric`.

## Версии

- Minecraft 1.20.1; Yarn `1.20.1+build.10`.
- Fabric Loader `0.17.2`; Fabric API `0.92.11+1.20.1`.
- Fabric Loom 1.9.2; Gradle wrapper 8.12; Java source/target/release 17.
- Mod ID `seasonaldaycycle`; display name `Vertex`; mod version `1.1.0-mc1.20.1`.
- Serene Seasons integration optional; metadata suggests Serene Seasons >=9.1.0.

## Команды и конфигурация

- `/daycycle info` — показывает сезон, фазу, длину полных суток, длительность дня/ночи и время до смены фазы; работает только в Overworld.
- `/daycycle gui` — открывает экран настроек; доступно только игроку.
- `config/seasonaldaycycle.json` — общая клиентско-серверная конфигурация/значения по умолчанию. Default cycle length 72,000 ticks; допустимый диапазон 20–1,728,000 ticks с шагом 20.
- `.seasonaldaycycle.json` в корне сохранения мира — per-world cycle length, фактически читаемая/сохраняемая через `DayCycleWorldConfig`.
- GUI передаёт изменение на сервер. Менять значение может singleplayer или игрок с OP level 2; сервер синхронизирует значение всем подключённым игрокам.

## Поведение, подтверждённое исходниками

- Серверный END_WORLD_TICK обработчик меняет скорость продвижения времени в Overworld, учитывая GameRule `doDaylightCycle`.
- При наличии Serene Seasons используется текущий sub-season; без него применяются базовые весенние пропорции.
- В README и коде заданы сезонные пропорции: весна 30/30 мин, лето 35/25 мин, осень 30/30 мин, зима 25/35 мин при базовом полном цикле 60 минут.
- README заявляет, что мод не меняет server TPS; это проверка документации/архитектуры, не runtime-тест.

## Коммиты и сборки

- **HEAD:** `58c28046e8850aa9a7e333f455d5beaf624f58fd`
- **Commit:** `Restore exact 1.20.1 packet time sync target` (2026-09-13).
- **Actions:** [run 34780330389](https://github.com/Mortylovelly/seasonaldaycycle/actions/runs/34780330389), conclusion `success`, exact HEAD SHA.
- **Artifact:** `seasonaldaycycle`, ID `10325321316`, присутствует и не истёк.

## Проверка в игре/на сервере

Runtime-тест этой ветки не подтверждён. Успешная сборка не доказывает точность синхронизации времени, работу GUI или совместимость с конкретной версией Serene Seasons.

## Следующий шаг

Обновить README в активной ветке при будущей кодовой задаче и проверить GUI/команды в одиночном мире и на dedicated server с OP2 и без OP2.
