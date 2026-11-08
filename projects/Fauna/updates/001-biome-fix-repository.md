# 001 — Уточнение назначения репозитория и расположения биомного фикса

Дата: 2026-11-08

## Уточнение владельца
Пользователь подтвердил: код биомов и фикс распределения биомов находятся в репозитории [Mortylovelly/Fauna-](https://github.com/Mortylovelly/Fauna-). Не искать эту механику в Vertex / `seasonaldaycycle`.

## Проверка источников
Проверена ветка `main`:
- `gradle.properties`: Minecraft 1.20.1, Fabric Loader 0.19.5, Fabric API 0.92.12+1.20.1, Java target 17.
- `fabric.mod.json`: mod ID `fix`, отображаемое имя `Fix - Modpack Compatibility & Balance`.
- `src/main/java/com/mortylovelly/fix/biome/BiomeDistributionFix.java`: сам алгоритм распределения биомов.
- `src/main/java/com/mortylovelly/fix/mixin/ChunkSectionBiomeMixin.java`: inject в `ChunkSection.populateBiomes` на TAIL, priority 900, вызывает `BiomeDistributionFix.process`.
- `src/main/java/com/mortylovelly/fix/FixMod.java`: регистрирует compatibility resource pack, вызывает `BiomeDistributionFix.logSettings()`.
- `src/main/resources/resourcepacks/compat_fixes/data/yungscavebiomes/`: отдельные loot-table compatibility данные для YUNG's Cave Biomes.

## Алгоритм по исходникам
- Детерминированные большие области с jitter; quart scale — 4 блока.
- Обычные регионы: 3500 quart / 14 000 блоков номинальный шаг; ширина с jitter примерно 8 400–19 600 блоков.
- Горные регионы: 6000 quart / 24 000 блоков номинальный шаг; ширина с jitter примерно 14 400–33 600 блоков.
- Шанс сохранения горного кандидата: 35%.
- Реки, океаны, пещерные биомы, deep dark, Nether и End относятся к защищённым категориям по biome path checks.
- Код логирует диагностику с префиксами `[Fix][BiomeDiag]`, `[Fix][BiomeHook]`, `[Fix][BiomeProbe]`, `[Fix][BiomeStats]`.

## Ограничения проверки
Это инспекция кода, а не игровой тест. Нельзя заявлять, что актуальная сборка уже прошла тест генерации в мире. Для runtime подтверждения нужен игровой лог с `[Fix][BiomeHook] ACTIVE` и статистикой, плюс проверка результата генерации.

## Правило для будущих задач
При любых вопросах пользователя о биомном распределении/биомном фиксе сначала открывать репозиторий Fauna- (проект Fix), файл `BiomeDistributionFix.java` и связанный mixin. Не путать с Vertex.
