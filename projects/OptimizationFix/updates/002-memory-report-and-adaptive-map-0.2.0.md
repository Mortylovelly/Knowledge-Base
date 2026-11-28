# Optimization Fix 0.2.0 — измерение эффекта и адаптивная карта NBT

Дата: 2026-11-28
Repo: https://github.com/Mortylovelly/Fix-test
Branch: main
Version: 0.2.0
Commit: ea5118ab3240e78458f399a96279308e7200c982

## Что изменено

1. NBT string lookup переносится на HEAD NbtString.of(String): cache hit возвращает существующий NbtString до создания второй обёртки. Exact metric: wrapperAllocationsAvoided.
2. Added conservative cumulative object-graph byte estimate. Это не реальная величина освобождённой RAM; также выводятся текущие heap/old-generation/GC stats.
3. /optimizationfix stats выдаёт report в chat, latest.log с [WARN] [OPTIMIZATIONFIX-STATS] и AI Agent custom RAM log: logs/minecraft-ai-agent/custom/latest.log.
4. Compact map experiment включён в конфиге. Новые NbtCompound начинают с ArrayMap до восьми distinct keys, затем один раз promoted to Object2ObjectOpenHashMap.
5. ModernFix compatibility: при активном mixin.perf.nbt_memory_usage собственные NBT compound-key и adaptive-map mixins подавляются; независимый NbtString value dedup остаётся.
6. Compatibility state cached at startup; compound mixins fail-closed until config/compatibility loaded.
7. Java string dedup flag reported; optional -XX:+UseStringDeduplication только если G1 и флаг отключён.

## Как читать эффективность

- wrapperAllocationsAvoided — точное число пропущенных созданий NbtString wrapper.
- Estimated duplicate object graph — расчётная верхняя оценка, не измеренная свободная RAM.
- JVM heap/oldGen/GC — моментальный снимок, не прямая причинная оценка от мода.
- adaptive maps created/promoted — сколько маленьких структур создано и сколько выросло до hash map.
- Effective switches показывает, что действительно включено, а что отключено из-за ModernFix.

## Проверка и артефакт

GitHub Actions run #11, ID 46175808662, exact commit ea5118ab3240e78458f399a96279308e7200c982: build + tests + artifact upload success.

- Artifact ID: 15274819734
- ZIP size: 44,949 bytes
- ZIP SHA256: 56f1e9becfaf8dd7195b5fd4680ba6435c12b46d3f884fab4e037fa6eb504616
- JAR size: 33,515 bytes
- JAR SHA256: 982f3cf5a173d5bb1321cae6e2f5b0926b11253890ac79351d5fed837572f734

JAR contents/metadata were inspected after artifact download. CI compilation and unit tests passed. No full in-game modpack runtime test has been performed yet.

## Совместимость с GTA VI / другими движками

Rockstar developer interview describes LOD re-architecture, parallelization and streaming prioritization. These are native engine/world-streaming mechanisms, not directly reusable as a Java NBT patch. The applied concepts are object reuse, bounded weak caches, compact small structures and growth to hash lookup for large structures. No claim of equivalent engine-level optimization.
