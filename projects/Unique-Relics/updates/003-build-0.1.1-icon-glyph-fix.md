# 003 — Unique Relics 0.1.1: исправление glyph-карты и успешная сборка

- **Дата:** 2026-11-14
- **Проект:** Unique-Relics
- **Линия Minecraft / ветка:** Minecraft 1.20.1 / Fabric, `main`
- **Версия мода:** `0.1.1`
- **Статус записи:** подтверждено по HEAD, build files, metadata и Actions; runtime не проверен

## Контекст

После реализации первой системы smithing-trim relic предыдущая документированная версия была 0.1.0. Текущий HEAD обновляет карту private-use glyphs для иконок шаблонов/материалов.

## Исследование

- `gradle.properties`: Minecraft 1.20.1, Yarn `1.20.1+build.10`, Fabric Loader `0.19.5`, Fabric API `0.92.12+1.20.1`, `mod_version=0.1.1`.
- `build.gradle`: Java target 17; plugin фактически указан как Fabric Loom `1.9-SNAPSHOT`. Свойство `loom_version=1.9.2` в gradle.properties не подставляется в plugin declaration и сейчас не определяет используемую версию.
- Gradle wrapper: 8.12.
- `fabric.mod.json`: Mod ID `unique_relics`, Minecraft 1.20.1, Java >=17.
- Workflow использует Java 17 и Gradle wrapper.
- Основная логика находится в `SmithingScreenHandlerMixin.java`; клиентский tooltip и glyph mapping — в `UniqueRelicsClient.java`.

## Реализация

Сохраняется механика записи 002: smithing trim recipe создаёт прежний тип предмета, pattern сохраняется, trim material выбирается случайно из registry, а NBT `unique_relics` хранит template/pattern/material/rarity. При взятии результата записываются кузнец и время; клиент показывает локализованный tooltip. Новые item types и отдельная регистрация предметов не используются.

В HEAD изменён только `src/main/resources/assets/unique_relics/font/icons.json`: исправлена таблица glyph-кодов для иконок. Логика ковки не менялась в этом commit.

## Коммиты и сборки

- **HEAD:** `3b7da34a1dbe965832b5d301624a7b421bad2a3b`
- **Commit:** `Fix private-use glyphs in relic icon font` (2026-11-08).
- **Actions:** [run 42500971628](https://github.com/Mortylovelly/Unique-Relics/actions/runs/42500971628), conclusion `success`, exact HEAD SHA.
- **Artifact:** `unique-relics-build`, ID `13625302028`, присутствует и не истёк.

## Команды и конфигурация

Slash-команды и отдельные config-файлы в текущем дереве исходников не обнаружены.

## Проверка в игре/на сервере

Игровой runtime-тест 0.1.1 и совместимость с Tool Trims / Elytra Trims не подтверждены. Успешная сборка не доказывает работу mixin, tooltip и сторонних trim-рецептов в игре.

## Следующий шаг

Проверить ванильную броню, Tool Trims и Elytra Trims в Minecraft 1.20.1 Fabric; проверить preview, взятие результата, сохранение NBT после перезахода и корректность иконок.
