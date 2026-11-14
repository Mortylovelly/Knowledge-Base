# seasonaldaycycle / Vertex

- **Репозиторий:** [Mortylovelly/seasonaldaycycle](https://github.com/Mortylovelly/seasonaldaycycle)
- **Подтверждённая рабочая ветка:** `minecraft-1.20.1-fabric`.
- **Minecraft:** 1.20.1 Fabric / Java 17.
- **Mod ID:** `seasonaldaycycle`.
- **Display name:** `Vertex`.
- **Версия:** `1.1.0-mc1.20.1`.
- **Последний HEAD:** `58c28046e8850aa9a7e333f455d5beaf624f58fd`.
- **Последний Actions build:** [run 34780330389](https://github.com/Mortylovelly/seasonaldaycycle/actions/runs/34780330389), success для exact HEAD.
- **Artifact:** `seasonaldaycycle`, ID `10325321316`.
- **Runtime:** тест активной ветки в игре/на dedicated server не подтверждён.

## Линии и ограничения

Проверялась только ветка `minecraft-1.20.1-fabric`. Другие доступные ветки `main` и `fabric-1.21.1` не исследовались и не должны выбираться автоматически.

README активной ветки устарел: он описывает Fabric 1.21.1 и утверждает, что Forge 1.20.1 находится на main. Фактические `gradle.properties`, `build.gradle` и `fabric.mod.json` проверенной ветки подтверждают Fabric 1.20.1.

## Среда сборки

| Компонент | Значение |
|---|---|
| Minecraft | 1.20.1 |
| Yarn | 1.20.1+build.10 |
| Fabric Loader | 0.17.2 |
| Fabric API | 0.92.11+1.20.1 |
| Fabric Loom | 1.9.2 |
| Gradle wrapper | 8.12 |
| Java source/target/release | 17 |
| Optional Serene Seasons | >=9.1.0 (suggested dependency) |
| mod_version | 1.1.0-mc1.20.1 |

## Функциональность

- Серверный END_WORLD_TICK обработчик меняет скорость продвижения времени в Overworld, если включён vanilla gamerule `doDaylightCycle`.
- При наличии Serene Seasons определяется текущий sub-season через optional reflection integration; без мода используются базовые весенние пропорции.
- Базовая длина полных суток — 72 000 ticks (60 реальных минут при 20 TPS).
- Сезонные пропорции: весна 30 мин день / 30 мин ночь; лето 35 / 25; осень 30 / 30; зима 25 / 35.
- GUI позволяет менять длину полного цикла; сервер сохраняет настройку отдельно для каждого мира и синхронизирует её игрокам.
- Изменение цикла разрешено в singleplayer или игроку с OP level 2. Без прав сервер отправляет клиенту текущее значение.
- README заявляет, что мод меняет скорость игрового времени без изменения server TPS; это не заменяет runtime-проверку.

## Команды

| Команда | Назначение |
|---|---|
| `/daycycle info` | Показывает сезон, фазу, длительность суток/дня/ночи и время до смены фазы; только Overworld |
| `/daycycle gui` | Открывает GUI настроек; доступно только игроку |

Других slash-команд в проверенном Java tree не обнаружено.

## Конфигурация и сохранения

- `config/seasonaldaycycle.json` — общие/default значения ModConfig; default cycle length 72 000 ticks, допустимый диапазон 20–1 728 000 ticks, шаг 20.
- `.seasonaldaycycle.json` в корне каждого world save — фактическое per-world значение cycleLengthTicks, которым управляет `DayCycleWorldConfig`.
- GUI отправляет `SetDayCycleLengthPayload`; сервер валидирует доступ и рассылает синхронизированное значение всем игрокам.
- **Ограничение текущего config-кода:** `ModConfig.Config` содержит `spring/summer/autumn/winter` day/night поля и санитизирует их, но getters `getSpringDayTicks` / `getSummerDayTicks` / `getAutumnDayTicks` / `getWinterDayTicks` сейчас используют жёстко заданные константы. Поэтому редактирование этих сезонных полей в `config/seasonaldaycycle.json` не меняет пропорции; фактически используется per-world `cycleLengthTicks` и фиксированные сезонные соотношения.

## Ключевые исходники

- `SeasonalDayCycle.java` — загрузка config, networking, tick handler и command registration.
- `DayCycleCommand.java` — `/daycycle info` и `/daycycle gui`.
- `DayCycleHandler.java` — сезонные пропорции и изменение времени.
- `ModConfig.java` — диапазон/шаг и default values.
- `DayCycleWorldConfig.java` — per-world сохранение цикла.
- `DayCycleNetworking.java` — проверка singleplayer/OP2 и sync.
- `VertexPanelController.java`, `DayCycleScreen.java` — клиентский Vertex/time GUI.
- `ClientPlayNetworkHandlerMixin.java`, `seasonaldaycycle.mixins.json` — клиентская обработка time sync/UI.

## GitHub Actions

- HEAD SHA: `58c28046e8850aa9a7e333f455d5beaf624f58fd`.
- Commit: `Restore exact 1.20.1 packet time sync target` (2026-09-13).
- [Actions run 34780330389](https://github.com/Mortylovelly/seasonaldaycycle/actions/runs/34780330389): `completed / success`, exact HEAD.
- Artifact `seasonaldaycycle`, ID `10325321316`, присутствует и не истёк на момент аудита.

## Текущее состояние

Линия 1.20.1 Fabric однозначно подтверждена по branch name и build metadata. Документация README в исходном репозитории устарела; Knowledge Base фиксирует фактическую линию. Runtime-тесты GUI, команд, server sync и Serene Seasons integration ещё не подтверждены.

## История

- [001 — Подтверждена Fabric-линия Vertex / Seasonal Day Cycle для 1.20.1](updates/001-fabric-1.20.1-branch-inventory.md)
