# PROJECTS INDEX — каталог модов

Это оглавление известных проектов Mortylovelly. Ссылки «Документация» ведут к паспорту проекта в этой базе; ссылки «Код» — к реальному GitHub-репозиторию. Перед работой проверяй актуальные ветку, код и Actions. Общий рабочий стандарт — Minecraft 1.20.1 Fabric / Java 17; другие версии не развивать без прямого разрешения владельца.

| Проект | Документация | Код | Активная линия по умолчанию | Что известно | Статус документации |
|---|---|---|---|---|---|
| SkyLimitless | [PROJECT.md](projects/SkyLimitless/PROJECT.md) · [история](projects/SkyLimitless/updates/README.md) | [GitHub](https://github.com/Mortylovelly/SkyLimitless) | Fabric 1.20.1: `minecraft-1.20.1-fabric` | Увеличение высоты Overworld; команды; Tectonic; registry fix 0.3.4 | Подробно; server runtime тест ещё нужен |
| MinecartPlus | [PROJECT.md](projects/MinecartPlus/PROJECT.md) · [история](projects/MinecartPlus/updates/README.md) | [GitHub](https://github.com/Mortylovelly/MinecartPlus) | Требуется сверить ветку 1.20.1 или спросить владельца, если вариантов несколько | Вагонетки, зачарования тяги/попутного ветра, GeckoLib | Частично; не использовать 1.21.1 без разрешения |
| Fauna- / Fix | [PROJECT.md](projects/Fauna/PROJECT.md) · [история](projects/Fauna/updates/README.md) | [GitHub](https://github.com/Mortylovelly/Fauna-) | Fabric 1.20.1 / Java 17: проверенная ветка `main` | Fix — Modpack Compatibility & Balance; **здесь находится BiomeDistributionFix и mixin распределения биомов** | Биомный алгоритм осмотрен; runtime-тест не подтверждён |
| Minecraft AI Agent | [PROJECT.md](projects/Minecraft-AI-Agent/PROJECT.md) · [история](projects/Minecraft-AI-Agent/updates/README.md) | [GitHub](https://github.com/Mortylovelly/Minecraft-AI-Agent) | Fabric 1.20.1: `minecraft-1.20.1-fabric` | AI tools на dedicated server, чат-архив, NPC/mob navigation, persistent archive/COPY и independent Logs HUD (0.3.3) | JAR 0.3.3 собран успешно (Actions run 42682270795); тест HUD в Minecraft ещё нужен |
| Unique-Relics | [PROJECT.md](projects/Unique-Relics/PROJECT.md) · [история](projects/Unique-Relics/updates/README.md) | [GitHub](https://github.com/Mortylovelly/Unique-Relics) | Fabric 1.20.1 / Java 17: main | Первая ковка: прежний предмет + trim pattern/random material + NBT Rare/smith/date | Build проходит; runtime тест ковки и сторонних trim-модов ещё нужен |
| VitalCraft | [PROJECT.md](projects/VitalCraft/PROJECT.md) · [история](projects/VitalCraft/updates/README.md) | [GitHub](https://github.com/Mortylovelly/VitalCraft) | Нужно проверить 1.20.1 и уточнить ветку при неоднозначности | Репозиторий известен, подробности не восстановлены | Требует изучения |
| WeatherPlus- | [PROJECT.md](projects/WeatherPlus/PROJECT.md) · [история](projects/WeatherPlus/updates/README.md) | [GitHub](https://github.com/Mortylovelly/WeatherPlus-) | Нужно проверить 1.20.1 и уточнить ветку при неоднозначности | Репозиторий известен | Требует изучения |
| harderworld | [PROJECT.md](projects/harderworld/PROJECT.md) · [история](projects/harderworld/updates/README.md) | [GitHub](https://github.com/Mortylovelly/harderworld) | Нужно проверить 1.20.1 и уточнить ветку при неоднозначности | Репозиторий известен | Требует изучения |
| extremedifficulty | [PROJECT.md](projects/extremedifficulty/PROJECT.md) · [история](projects/extremedifficulty/updates/README.md) | [GitHub](https://github.com/Mortylovelly/extremedifficulty) | Нужно проверить 1.20.1 и уточнить ветку при неоднозначности | Репозиторий известен | Требует изучения |
| seasonaldaycycle | [PROJECT.md](projects/seasonaldaycycle/PROJECT.md) · [история](projects/seasonaldaycycle/updates/README.md) | [GitHub](https://github.com/Mortylovelly/seasonaldaycycle) | Сохранённое сведение: Forge 1.20.1; уточнить у владельца, если нужно продолжать | Сезонный цикл дня | Частично; не переносить в Fabric автоматически |

## Неактивные линии и ограничения

- Minecraft AI Agent: `main` — отдельная линия 1.21.1; не использовать, пока владелец явно не разрешит.
- SkyLimitless: `main` — отдельная линия 1.21.1; не использовать, пока владелец явно не разрешит.
- Ветка `ci-test-1201` в старом репозитории AI Agent — историческая тестовая ветка с неуспешными CI-прогонами, не использовать.
- Другие версии из архивных PROJECT.md — исторические сведения, а не разрешение вести разработку на них.

## Важные замечания

- Этот список не означает, что каждый проект активно поддерживается.
- Точные версии, названия веток и статус каждого проекта перепроверять через GitHub.
- Если target/branch не очевидны — спросить владельца до внесения изменений.
