# Fauna- — Fix: Modpack Compatibility & Balance

- **Репозиторий:** [Mortylovelly/Fauna-](https://github.com/Mortylovelly/Fauna-)
- **Название мода:** Fix — Modpack Compatibility & Balance.
- **Mod ID:** `fix`.
- **Целевая линия:** Minecraft 1.20.1 / Fabric / Java 17 bytecode.
- **Подтверждённая ветка:** `main`.
- **Текущая версия:** `0.4.10`.
- **Последний HEAD:** `9b0a3a3c15f6ce1d0287b71a368678cdb94f3d9f`.
- **Последний Actions build:** [run 43606993599](https://github.com/Mortylovelly/Fauna-/actions/runs/43606993599), success для exact HEAD.
- **Artifact:** `fix-build`, ID `14118611033`.
- **Runtime:** тест текущей версии в реальном модпаке не подтверждён.

## Среда сборки

| Компонент | Значение |
|---|---|
| Minecraft | 1.20.1 |
| Yarn | 1.20.1+build.10 |
| Fabric Loader | 0.19.5 |
| Fabric API | 0.92.12+1.20.1 |
| Loom | 1.17.21 |
| Gradle в workflow | 9.5.0 |
| JDK в workflow | 21 |
| Java source/target/release | 17 |
| mod_version | 0.4.10 |

Важно: runtime/bytecode target остаётся Java 17, но текущий CI запускает Gradle/Loom на JDK 21. README объясняет это требованием Loom 1.17.21. Это отклонение от стандартного build JDK 17 следует учитывать при будущей унификации.

## Что делает активная main

- Регистрирует встроенный resource pack `compat_fixes`.
- Если загружен Faunify, проверяет и восстанавливает отсутствующие default attributes для выбранных сущностей.
- Регистрирует правила удаления отдельных дублирующих natural-spawn entries между fauna-модами; сами entity registrations, spawn eggs, ручной summon и уже существующие сущности не удаляются.
- Содержит targeted compatibility loot-table данные для YUNG's Cave Biomes / Cave Biomes Delight.
- Логирует наличие ряда модов модпака для диагностики.

## Биомы и генерация мира — важно

Текущая активная `main` **не изменяет распределение биомов или terrain**. В её текущем дереве нет `BiomeDistributionFix.java` и `ChunkSectionBiomeMixin.java`; `fabric.mod.json` не подключает mixin config, а `fix.mixins.json` удалён. README прямо говорит, что мод не меняет biome palettes, `/locate biome`, горы, шумы, terrain или world height.

Update 001 сохраняет историческое состояние на 2026-11-08, когда биомный алгоритм ещё был описан в main. Для текущего состояния использовать update 002.

В списке веток также есть `biome-diagnostics-0.4.1`, `biome-distribution-realism` и CI recovery branch. Они не проверялись и не являются подтверждёнными рабочими линиями; не открывать их для разработки без подтверждения владельца.

## Fauna: состав сборки и runtime findings

В сохранённом 292-модовом логе 2026-11-08 подтверждены девять mob/fauna модов: Alex's Mobs Continued 2.2.2, Naturalist 5.0pre5, Wildlife 1.19.2-2.2, Critters and Companions 2.7.1, Faunify 1.3.3, Friends&Foes 3.0.9, Ecologics 2.2.7, Hybrid Birds 1.3.0 и YUNG's Cave Biomes 2.0.5. Naturalist Delight — аддон, не отдельный mob pack.

Важные ограничения текущего Fix:
- `FaunaDeduplicator` удаляет только natural spawn entries. Он не удаляет сами сущности, spawn eggs, summon или уже существующих мобов.
- В коде неверный ID Ecologics Crab: указан `ecologics:crab`, реальная сущность — `ecologics:coconut_crab`; следовательно, это правило не удаляет её spawn.
- Общая функция dedup проверяет наличие двух модов и entity duplicate ID, но не проверяет наличие preferred entity. Так можно убрать spawn вида без его реальной замены. Особо перепроверить Naturalist/Wildlife pairings именно для установленного Naturalist 5.0pre5.
- В логе 2026-11-08 22 Faunify entity errors `has no attributes` были до сообщения Fix `Faunify entity attributes are already registered`. Это не доказывает, что ранняя регистрационная ошибка исправлена: текущая проверка запускается поздно, а в этом запуске добавила ноль записей.
- В логе 2026-11-11 C2ME repeatedly reports `Failed to create mob` for Faunify Beefly. Stack points to `BeeflyGoToKnownFlowerGoal` reading `ThreadLocalRandom` from C2ME worker instead of its owner Server thread. Current Fix has no threading/random patch, so this interaction is not fixed.
- Регистрация dedup callback в логе не равна доказанному удалению каждого вида: per-entity removals need explicit runtime diagnostics/test.

Подробно: [003 — аудит fauna-модов и реальной работы Fix](updates/003-fauna-modpack-overlap-and-fix-runtime-audit.md).

## Команды и конфигурация

В текущем entrypoint/дереве не обнаружена регистрация slash-команд. Отдельный config-класс или config-файл не обнаружен.

## Ключевые исходники

- `FixMod.java` — инициализация resource pack, Faunify fix, fauna dedup и startup diagnostics.
- `FaunaDeduplicator.java` — выбранные правила удаления дублирующих natural spawns.
- `FaunifyAttributeFix.java` — восстановление отсутствующих default attributes для списка Faunify entities.
- `src/main/resources/resourcepacks/compat_fixes/` — встроенные compatibility data.

## GitHub Actions

- HEAD SHA: `9b0a3a3c15f6ce1d0287b71a368678cdb94f3d9f`.
- Commit: `Remove obsolete biome distribution implementation` (2026-11-14).
- [Actions run 43606993599](https://github.com/Mortylovelly/Fauna-/actions/runs/43606993599): `completed / success`, exact HEAD.
- Artifact `fix-build`, ID `14118611033`, присутствует и не истёк на момент аудита.

## Текущее состояние

- Активная подтверждённая линия: Minecraft 1.20.1 Fabric, `main`.
- Активный Fix — мод совместимости и баланса fauna, а не генератор биомов.
- Build проходит, но CI использует JDK 21 при Java 17 bytecode target.
- Runtime-аудит нашёл проблемы в fauna-dedup и Faunify+C2ME; не считать эти функции полностью проверенными или исправленными.
- Для возвращения биомного алгоритма нужно сначала выбрать целевую feature-ветку вместе с владельцем.

## История

- [001 — Уточнение назначения репозитория и расположения биомного фикса](updates/001-biome-fix-repository.md) — историческое состояние на 2026-11-08.
- [002 — Текущее состояние Fix: биомный алгоритм отсутствует в main](updates/002-main-branch-worldgen-removal-and-current-build.md)
- [003 — Аудит fauna-модов и реальной работы Fix](updates/003-fauna-modpack-overlap-and-fix-runtime-audit.md)
