# 002 — Текущее состояние Fix: биомный алгоритм отсутствует в main

- **Дата:** 2026-11-14
- **Проект:** Fauna- / Fix — Modpack Compatibility & Balance
- **Линия Minecraft / ветка:** Minecraft 1.20.1 / Fabric, `main`
- **Версия мода:** `0.4.10`
- **Статус записи:** подтверждено по текущему HEAD, README, metadata, исходникам и Actions

## Контекст

Запись 001 от 2026-11-08 описывает существовавший тогда BiomeDistributionFix и связанный mixin. Текущее состояние ветки main изменилось: старую запись не переписываем, а фиксируем актуальное состояние здесь.

## Исследование

- `gradle.properties`: Minecraft 1.20.1, Yarn `1.20.1+build.10`, Fabric Loader `0.19.5`, Fabric API `0.92.12+1.20.1`, Loom `1.17.21`, mod version `0.4.10`.
- `build.gradle`: Java source/target/release 17.
- Workflow: JDK 21 и Gradle 9.5.0; README объясняет это требованием Loom 1.17.21. Следовательно, bytecode target — Java 17, но CI build runtime — Java 21.
- `fabric.mod.json`: Mod ID `fix`, Minecraft 1.20.1, Fabric.
- Текущие Java-файлы: `FixMod.java`, `FaunaDeduplicator.java`, `FaunifyAttributeFix.java`.
- В текущем recursive tree нет `BiomeDistributionFix.java` и `ChunkSectionBiomeMixin.java`; metadata не регистрирует mixin config, а `fix.mixins.json` удалён в последнем commit.
- README прямо указывает, что активный мод не меняет биомы, terrain, noise, горы или world height.

## Функциональность текущей main

- Регистрирует встроенный compatibility resource pack `compat_fixes`.
- Если загружен Faunify, проверяет и добавляет отсутствующие default attributes для выбранных сущностей.
- Регистрирует правила удаления отдельных дублирующих natural spawn entries между совместимыми fauna-модами, сохраняя entity registrations и уже существующих мобов.
- Включает ресурсные compatibility данные для YUNG's Cave Biomes / Cave Biomes Delight.
- В активном entrypoint не обнаружена регистрация slash-команд; конфигурационный файл/класс не обнаружен.

## Коммит и сборка

- **HEAD:** `9b0a3a3c15f6ce1d0287b71a368678cdb94f3d9f`
- **Commit:** `Remove obsolete biome distribution implementation` (2026-11-14).
- **Actions:** [run 43606993599](https://github.com/Mortylovelly/Fauna-/actions/runs/43606993599), conclusion `success`, exact HEAD SHA.
- **Artifact:** `fix-build`, ID `14118611033`, присутствует и не истёк.

## Проверка в игре/на сервере

Runtime-тест текущей версии 0.4.10 не подтверждён. Успешная сборка не доказывает поведение spawn deduplication, Faunify attribute repair или loot-table compatibility в реальном модпаке.

## Важное ограничение по веткам

У репозитория есть и другие ветки, включая `biome-diagnostics-0.4.1` и `biome-distribution-realism`. В этой проверке открывалась только подтверждённая ветка `main`; остальные ветки не исследовались и не считаются целевыми без отдельного подтверждения владельца.

## Следующий шаг

Если требуется вернуть или продолжить биомное распределение, сначала подтвердить у владельца, какая из feature-веток является целевой 1.20.1. Текущая main для этой функции не подходит.
