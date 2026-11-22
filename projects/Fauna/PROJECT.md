# Fauna- — Fix: Modpack Compatibility & Balance

- **Репозиторий:** [Mortylovelly/Fauna-](https://github.com/Mortylovelly/Fauna-)
- **Название мода:** Fix — Modpack Compatibility & Balance.
- **Mod ID:** `fix`.
- **Целевая линия:** Minecraft 1.20.1 / Fabric / Java 17 bytecode.
- **Подтверждённая ветка:** `main`.
- **Текущая версия исходников:** `0.4.13`.
- **Последний HEAD:** [`2427d98e726d035112badd199a7e8ec7250a85c7`](https://github.com/Mortylovelly/Fauna-/commit/ecadeb503f1e3d1eaaa3b2f6a472ae861088437d).
- **Последний Actions build:** [run 45036701275](https://github.com/Mortylovelly/Fauna-/actions/runs/45036701275), `completed / success` для exact HEAD.
- **Artifact:** `fix-build`, ID `14759399821`, не истёк на момент update 004.
- **Runtime:** проверка руд в созданном мире и серверный тест после 0.4.13 не подтверждены.

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
| mod_version | 0.4.13 |

Runtime/bytecode target остаётся Java 17, но CI запускает Gradle/Loom на JDK 21.

## Что делает активная main

- Регистрирует встроенный resource pack `compat_fixes`.
- Если загружен Faunify, проверяет и восстанавливает отсутствующие default attributes для выбранных сущностей.
- Регистрирует правила удаления отдельных дублирующих natural-spawn entries между fauna-модами; сами entity registrations, spawn eggs, ручной summon и уже существующие сущности не удаляются.
- Содержит targeted compatibility loot-table data для YUNG's Cave Biomes / Cave Biomes Delight.
- Логирует наличие ряда модов модпака для диагностики.
- С версии 0.4.12 содержит целевые Big Globe ore-generation compatibility data для BetterNether и Create Nuclear.

## Биомы и генерация мира — важно

Текущая активная `main` не меняет распределение биомов или terrain. В её текущем дереве нет `BiomeDistributionFix.java` и `ChunkSectionBiomeMixin.java`; `fabric.mod.json` не подключает mixin config, а `fix.mixins.json` удалён. README говорит, что мод не меняет biome palettes, `/locate biome`, горы, шумы, terrain или world height.

Update 001 сохраняет историческое состояние на 2026-11-08, когда биомный алгоритм ещё был описан в main. Для текущего состояния использовать update 002.

В списке веток также есть `biome-diagnostics-0.4.1`, `biome-distribution-realism` и CI recovery branch. Они не проверялись и не являются подтверждёнными рабочими линиями; не открывать их для разработки без подтверждения владельца.

## Состав сборки

- Актуальный список модпака на 19 ноября 2026 года — **95 позиций**, полностью сохранён в [update 004](updates/004-current-modpack-and-bigglobe-ore-fixes.md).
- Этот список является текущим ориентиром до тех пор, пока владелец сам не сообщит об изменениях.
- Владелец может добавлять/удалять отдельные моды; не считать это автоматической сменой версии или полной заменой сборки.
- Повторы и обрезанные имена исходного списка сохранены без изменений.
- В update 003 отдельно описан исторический лог от 8 ноября с 292 загруженными модами. Не объединять тот лог с актуальным 95-позиционным списком без подтверждения, что это одна и та же установка.

## Fauna: исторический runtime audit

В сохранённом 292-модовом логе 2026-11-08 подтверждены девять mob/fauna модов: Alex's Mobs Continued 2.2.2, Naturalist 5.0pre5, Wildlife 1.19.2-2.2, Critters and Companions 2.7.1, Faunify 1.3.3, Friends&Foes 3.0.9, Ecologics 2.2.7, Hybrid Birds 1.3.0 и YUNG's Cave Biomes 2.0.5. Naturalist Delight — аддон, не отдельный mob pack. Это исторический runtime audit, а не полный roster текущего списка из 95 файлов.

Важные ограничения текущего Fix, выявленные в update 003:
- `FaunaDeduplicator` удаляет только natural spawn entries.
- В коде неверный ID Ecologics Crab: указан `ecologics:crab`, реальная сущность — `ecologics:coconut_crab`.
- Общая функция dedup не проверяет наличие preferred entity до удаления duplicate spawn.
- Лог о регистрации dedup callback не доказывает фактическое удаление каждого вида.
- В логе 2026-11-11 подтверждена ошибка Faunify Beefly при работе с C2ME worker thread; Fix 0.4.12 в рамках ore-generation задачи не содержит threading/random patch.

Подробно: [003 — аудит fauna-модов и реальной работы Fix](updates/003-fauna-modpack-overlap-and-fix-runtime-audit.md).

## Команды и конфигурация

В текущем entrypoint/дереве не обнаружена регистрация slash-команд. Отдельный config-класс или config-файл не обнаружен.

## Ключевые исходники

- `FixMod.java` — инициализация resource pack, Faunify fix, fauna dedup и startup diagnostics.
- `FaunaDeduplicator.java` — выбранные правила удаления дублирующих natural spawns.
- `FaunifyAttributeFix.java` — восстановление отсутствующих default attributes для списка Faunify entities.
- `src/main/resources/resourcepacks/compat_fixes/` — встроенные compatibility data, включая новые ore-generation JSON.

## GitHub Actions

- HEAD SHA: `ecadeb503f1e3d1eaaa3b2f6a472ae861088437d`.
- Commit: [Fix Big Globe ore generation and record current modpack inventory](https://github.com/Mortylovelly/Fauna-/commit/ecadeb503f1e3d1eaaa3b2f6a472ae861088437d), 2026-11-18.
- [Actions run 45036701275](https://github.com/Mortylovelly/Fauna-/actions/runs/45036701275): `completed / success`, exact HEAD.
- Artifact `fix-build`, ID `14475569247`, присутствует и не истёк на момент update 004.

## Текущее состояние

- Активная подтверждённая линия: Minecraft 1.20.1 Fabric, `main`.
- Активная версия исходников Fix: 0.4.12.
- Build 0.4.12 проходит на exact HEAD; CI использует JDK 21 при Java 17 bytecode target.
- Текущий список сборки: 95 позиций, update 004; считать его актуальным до сообщения владельца об изменениях.
- BetterNether/Create Nuclear ore-generation fixes добавлены, но распределение руд ещё не проверялось в игре.
- Autunite deepslate replacement требует отдельной проверки/возможного дополнения.
- Runtime-аудит update 003 нашёл проблемы в fauna-dedup и Faunify+C2ME; не считать их исправленными изменениями 0.4.12.
- Для возвращения биомного алгоритма нужно сначала выбрать целевую feature-ветку вместе с владельцем.

## История

- [001 — Уточнение назначения репозитория и расположения биомного фикса](updates/001-biome-fix-repository.md) — историческое состояние на 2026-11-08.
- [002 — Текущее состояние Fix: биомный алгоритм отсутствует в main](updates/002-main-branch-worldgen-removal-and-current-build.md)
- [003 — Аудит fauna-модов и реальной работы Fix](updates/003-fauna-modpack-overlap-and-fix-runtime-audit.md)
- [004 — Актуальный состав модпака и исправления генерации руд Big Globe](updates/004-current-modpack-and-bigglobe-ore-fixes.md)
