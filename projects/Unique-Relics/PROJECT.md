# Unique-Relics

- **Репозиторий:** [Mortylovelly/Unique-Relics](https://github.com/Mortylovelly/Unique-Relics)
- **Рабочая ветка:** `main` (единственная ветка, target 1.20.1 подтверждён паспортом и текущими metadata).
- **Minecraft:** 1.20.1 Fabric / Java 17.
- **Mod ID:** `unique_relics`.
- **Текущая версия:** `0.1.1`.
- **Последний HEAD:** `3b7da34a1dbe965832b5d301624a7b421bad2a3b`.
- **Последний Actions build:** [run 42500971628](https://github.com/Mortylovelly/Unique-Relics/actions/runs/42500971628), success для exact HEAD.
- **Artifact:** `unique-relics-build`, ID `13625302028`.
- **Runtime:** ковка и совместимость со сторонними trim-модами в игре ещё не подтверждены.

## Среда сборки

| Компонент | Значение |
|---|---|
| Minecraft | 1.20.1 |
| Yarn | 1.20.1+build.10 |
| Fabric Loader | 0.19.5 |
| Fabric API | 0.92.12+1.20.1 |
| Java source/target/release | 17 |
| Gradle wrapper | 8.12 |
| Plugin в build.gradle | Fabric Loom 1.9-SNAPSHOT |
| mod_version | 0.1.1 |

В `gradle.properties` есть `loom_version=1.9.2`, но `build.gradle` задаёт plugin version напрямую как `1.9-SNAPSHOT`; свойство сейчас не используется.

## Реализованная механика

Unique Relics не создаёт отдельные типы предметов и не ведёт глобальный реестр серийных номеров. Реликвия — исходный ItemStack с NBT.

- Используется штатный smithing table и рецепт smithing trim.
- Если рецепт выдаёт результат с реальным ArmorTrim, исходный item type сохраняется, pattern остаётся тем же, а trim material выбирается случайно из активного registry.
- Выбор материала кэшируется по сигнатуре входных стеков, чтобы preview не менял цвет при каждом обновлении.
- В NBT `unique_relics` хранятся template, pattern, material и rarity=`rare`.
- При взятии результата добавляются smith и forged_at.
- Клиентский tooltip показывает шаблон, материал/цвет, редкость, кузнеца и дату; glyph font использует иконки известных vanilla/Tool Trims предметов.
- Шанс ковки сейчас 100%, если обычный trim-рецепт прошёл.

Совместимость с Tool Trims и Elytra Trims зависит от их рецептов/registry и требует отдельного runtime-теста. JAR сторонних модов в репозиторий не добавлять.

## Команды и конфигурация

Slash-команды и отдельные config-файлы в текущем дереве исходников не обнаружены.

## Ключевые файлы

- `UniqueRelics.java` — инициализация.
- `SmithingScreenHandlerMixin.java` — обработка preview/output и NBT metadata.
- `ForgingScreenHandlerAccessor.java` — доступ к smithing input/output.
- `UniqueRelicsClient.java` — tooltip и glyph icons.
- `unique_relics.mixins.json`, `fabric.mod.json`, `assets/unique_relics/font/icons.json`, языковые файлы RU/EN.

## GitHub Actions

- HEAD SHA: `3b7da34a1dbe965832b5d301624a7b421bad2a3b`.
- Commit: `Fix private-use glyphs in relic icon font` (2026-11-08).
- [Actions run 42500971628](https://github.com/Mortylovelly/Unique-Relics/actions/runs/42500971628): `completed / success`, exact HEAD.
- Artifact `unique-relics-build`, ID `13625302028`, присутствует и не истёк на момент аудита.

## Текущее состояние

Сборка 0.1.1 проходит. Последний commit менял только glyph mapping, логика ковки осталась как в update 002. Runtime-проверка vanilla armor, Tool Trims, Elytra Trims, NBT persistence и tooltip ещё нужна.

## История

- [001 — Первичная концепция и техническая основа](updates/001-project-concept-and-skeleton.md)
- [002 — Первая система ковки без регистрации предметов](updates/002-first-smithing-relic-system.md)
- [003 — Unique Relics 0.1.1: исправление glyph-карты и успешная сборка](updates/003-build-0.1.1-icon-glyph-fix.md)
