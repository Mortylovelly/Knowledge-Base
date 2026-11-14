# PROJECTS INDEX — каталог модов

Это оглавление известных проектов Mortylovelly. Ссылки «Документация» ведут к паспорту проекта в этой базе; ссылки «Код» — к реальному GitHub-репозиторию. Перед работой проверяй актуальные ветку, код и Actions. Общий рабочий стандарт — Minecraft 1.20.1 Fabric / Java 17; другие версии не развивать без прямого разрешения владельца.

| Проект | Документация | Код | Активная линия по умолчанию | Что известно | Статус документации |
|---|---|---|---|---|---|
| SkyLimitless | [PROJECT.md](projects/SkyLimitless/PROJECT.md) · [история](projects/SkyLimitless/updates/README.md) | [GitHub](https://github.com/Mortylovelly/SkyLimitless) | Fabric 1.20.1: `minecraft-1.20.1-fabric` | Увеличение высоты Overworld; команды; Tectonic; registry fix 0.3.4 | Подробно; server runtime тест ещё нужен |
| MinecartPlus | [PROJECT.md](projects/MinecartPlus/PROJECT.md) · [история](projects/MinecartPlus/updates/README.md) | [GitHub](https://github.com/Mortylovelly/MinecartPlus) | Fabric 1.20.1: `minecraft-1.20.1-fabric` | Traction/Tailwind, DataTracker, self-propelling vehicles, GeckoLib 4.8.4 | Build success exact HEAD; runtime pending; README ветки устарел (1.21.1) |
| Fauna- / Fix | [PROJECT.md](projects/Fauna/PROJECT.md) · [история](projects/Fauna/updates/README.md) | [GitHub](https://github.com/Mortylovelly/Fauna-) | Fabric 1.20.1: `main` | Compatibility/fаuna dedup, Faunify attributes, loot fixes; активная main не меняет биомы/terrain | Build 0.4.10 success; runtime pending; CI JDK21 / bytecode 17 |
| Minecraft AI Agent | [PROJECT.md](projects/Minecraft-AI-Agent/PROJECT.md) · [история](projects/Minecraft-AI-Agent/updates/README.md) | [GitHub](https://github.com/Mortylovelly/Minecraft-AI-Agent) | Fabric 1.20.1: `minecraft-1.20.1-fabric` | AI tools, server logs HUD, chat archive, NPC/mob navigation; исходники 0.3.17 | Actions run 43091757637 success exact HEAD; artifact есть; runtime pending; `/aiagent status` всё ещё пишет 0.3.3 |
| Unique-Relics | [PROJECT.md](projects/Unique-Relics/PROJECT.md) · [история](projects/Unique-Relics/updates/README.md) | [GitHub](https://github.com/Mortylovelly/Unique-Relics) | Fabric 1.20.1 / Java 17: main | Первая ковка: прежний предмет + trim pattern/random material + NBT Rare/smith/date | Build проходит; runtime тест ковки и сторонних trim-модов ещё нужен |
| VitalCraft | [PROJECT.md](projects/VitalCraft/PROJECT.md) · [история](projects/VitalCraft/updates/README.md) | [GitHub](https://github.com/Mortylovelly/VitalCraft) | Только `main`; target 1.20.1 не подтверждён | Код/функции не проверялись | Требуется уточнить у владельца; Actions/artifact не проверялись |
| WeatherPlus- | [PROJECT.md](projects/WeatherPlus/PROJECT.md) · [история](projects/WeatherPlus/updates/README.md) | [GitHub](https://github.com/Mortylovelly/WeatherPlus-) | Только `main`; target 1.20.1 не подтверждён | Код/функции не проверялись | Требуется уточнить у владельца; Actions/artifact не проверялись |
| harderworld | [PROJECT.md](projects/harderworld/PROJECT.md) · [история](projects/harderworld/updates/README.md) | [GitHub](https://github.com/Mortylovelly/harderworld) | Только `main`; target 1.20.1 не подтверждён | Код/функции не проверялись | Требуется уточнить у владельца; Actions/artifact не проверялись |
| extremedifficulty | [PROJECT.md](projects/extremedifficulty/PROJECT.md) · [история](projects/extremedifficulty/updates/README.md) | [GitHub](https://github.com/Mortylovelly/extremedifficulty) | Только `main`; target 1.20.1 не подтверждён | Код/функции не проверялись | Требуется уточнить у владельца; Actions/artifact не проверялись |
| seasonaldaycycle / Vertex | [PROJECT.md](projects/seasonaldaycycle/PROJECT.md) · [история](projects/seasonaldaycycle/updates/README.md) | [GitHub](https://github.com/Mortylovelly/seasonaldaycycle) | Fabric 1.20.1: `minecraft-1.20.1-fabric` | Сезонный цикл; `/daycycle info`, `/daycycle gui`; per-world config | Actions success exact HEAD; README исходного репозитория устарел; runtime pending |

## Неактивные линии и ограничения

- Minecraft AI Agent: `main` — отдельная линия 1.21.1; не использовать, пока владелец явно не разрешит.
- SkyLimitless: `main` — отдельная линия 1.21.1; не использовать, пока владелец явно не разрешит.
- Ветка `ci-test-1201` в старом репозитории AI Agent — историческая тестовая ветка с неуспешными CI-прогонами, не использовать.
- Другие версии из архивных PROJECT.md — исторические сведения, а не разрешение вести разработку на них.

## Важные замечания

- Этот список не означает, что каждый проект активно поддерживается.
- Точные версии, названия веток и статус каждого проекта перепроверять через GitHub.
- Если target/branch не очевидны — спросить владельца до внесения изменений.
