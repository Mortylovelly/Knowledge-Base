# 003 — Отдельное отключение структур и оценка поверхностных предметов

- **Дата:** 2026-11-15.
- **Проект:** Big Globe / Worldgen, Minecraft 1.20.1 Fabric.
- **Статус:** подтверждено по пользовательским ZIP, документации Big Globe и опубликованным datapack-проектам; точный CPU delta от удаления поверхностных блоков не измерен.

## Контекст

Владелец спросил, можно ли отключать структуры Big Globe выборочно, и насколько сильно сервер нагружают камни, палки и кремень, которые генерируются на поверхности.

## Выборочное отключение структур

Подтверждено, что структуры Big Globe можно отключать отдельно с помощью datapack override. Есть готовые отдельные проекты для удаления только Obelisk и только Abandoned City. Также есть отдельные datapack-проекты, убирающие весь набор custom structures и отдельный набор vanilla structures.

Custom structures list in the published pack includes: abandoned city, bigger desert pyramid, campfire, large dungeon, large house, log cabin, medium dungeon, medium house, portal temple, portal temple cracked, small dungeon, small house, soul campfire, well, windmill. Vanilla structures are handled separately.

Obelisk is a known CPU-heavy candidate: the dedicated Remove Obelisk datapack describes its generation as requiring high CPU load and says disabling it can positively affect performance. No exact performance percentage is provided.

Removing a structure from future generation does not automatically erase structures already stored in generated chunks.

## Палки, кремень и камни на поверхности

В найденном `BigGlobe_Clean_Overworld_1.20.1.zip` файл `data/bigglobe/tags/worldgen/bigglobe_feature_dispatchers/overworld/small_features.json` has `replace: true` and includes only glow_lichen, flowers, small_foliage. Thus it removes the entire `surface_materials` dispatcher, not three independent toggles.

Upstream Big Globe feature list describes sticks and flint as vanilla items placeable as blocks, and rocks as smaller-than-a-block decorative/functional blocks. They are not dropped ItemEntity constantly ticking on the ground.

The upstream surface_materials dispatcher source places them during chunk generation: rocks loop count derives from hilliness, sticks loop count derives from foliage_at_surface, flint chance derives from riverBias. Exact runtime cost depends on terrain/traits and was not benchmarked.

Conclusion: these blocks add some one-time world-generation work and additional block states in chunk data, but do not create ongoing per-entity tick load. Expected performance gain from removing them is small and limited mainly to chunk generation; do not remove them solely to improve TPS/RAM. If the visual clutter is unwanted, selectively removing one category is possible by overriding the dispatcher script while preserving the other categories.

## Рекомендация

- Keep rocks, sticks and flint by default if their appearance/gameplay is acceptable.
- Disable Obelisk first if the goal is to reduce one of the known expensive structure-generation tasks.
- Consider Abandoned City or other structures for world style/clutter only; no comparable CPU cost was established for each of them.
- Do not replace the entire small_features tag without retaining other desired dispatchers.

## Источники

- Big Globe feature list: https://github.com/Builderb0y/BigGlobe/blob/V6/List%20of%20features.md
- Big Globe surface material script: https://github.com/Builderb0y/BigGlobe/blob/V6/src/common/resources/data/bigglobe/bigglobe/worldgen/feature_dispatcher/overworld/surface_materials.json
- Big Globe - Remove Obelisk: https://modrinth.com/datapack/big-globe-remove-obelisk
- Big Globe - Remove Abandoned City: https://modrinth.com/datapack/big-globe-remove-abandoned-city
- Big Globe - Remove Big Globe Structures: https://modrinth.com/datapack/big-globe-remove-big-globe-structures
- Big Globe - Remove Vanilla Structures: https://modrinth.com/datapack/big-globe-remove-vanilla-structures

## Проверка

- Архив Clean Overworld: inspected `small_features.json`, `large_features.json`, and world preset references. No game files were edited.
- Source code inspected from current upstream V6 branch; its exact JSON may differ from target 5.2.0/5.3.2 releases. Before producing a final custom dispatcher override, verify the target version's resource path and contents against the matching Big Globe JAR.
- No in-game benchmark or controlled TPS comparison was performed.