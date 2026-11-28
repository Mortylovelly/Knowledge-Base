# 001 — Optimization Fix 0.1.0: первая bounded NBT deduplication

- **Дата:** 2026-11-28
- **Minecraft:** 1.20.1
- **Loader:** Fabric 0.19.5
- **Java:** 17
- **Fabric API:** 0.92.12+1.20.1
- **Статус CI:** success
- **Runtime:** pending

## Что добавлено

- Новая Fabric мод-структура, Gradle 8.12 wrapper и Java 17 CI.
- Повторное использование одинаковых неизменяемых NbtString значений.
- Повторное использование равных строковых ключей NbtCompound.
- Слабый bounded interner с лимитом 4096 hash entries на тип данных, одной WeakReference на hash и защитой от hash collisions.
- Команды `/optimizationfix stats` и `/optimizationfix reload`.
- Unit tests: equal strings reuse, hash collisions don't merge values, overlong strings are skipped.
- Experimental compact NbtCompound backing map оставлена выключенной по умолчанию.

## Что показал модпак-аудит

Использован сохранённый список из 145 файлов от 22.11.2026. Сборка уже включает FerriteCore, ModernFix, MemoryLeakFix, BadOptimizations, Lithium, C2ME, Noisium, ServerCore, VMP и другие оптимизационные модули. Новые функции ограничены runtime NbtString/Compound-key dedup; существующие blockstate/model, chunk scheduling, rendering и network optimizations не переписывались.

Список от 22 ноября — не live-скан папки mods на дату этого обновления. Перед расширением функций необходим повторный аудит текущих JAR.

## CI / артефакт

- Current branch HEAD after documentation-only commit: `6bbe8693edddd21adfaa506df3a42c24d2b4eaac`.
- Tested source/test commit: `62007079586fbb0af828efb8a8c4ead065230ef5`.
- GitHub Actions run #6 / ID `46089594257`: exact latest HEAD, success.
- Build, compileTestJava, test, remapJar and upload artifact successful.
- Artifact ID `15240592435`; ZIP SHA256 `cc469ab39d189cf721351c824e915db8e5053fb68b651ab3fa97ab928accd1f9`.
- JAR `optimization-fix-0.1.0.jar`, size 15420 bytes, SHA256 `28c48d294bdd0d673acc45a3db1610a21e981414f85d9ee75c5e16b2dfcd093f`.
- JAR content checked: expected classes, Fabric metadata, Mixin config and refmap present; intermediary field/method mappings inspected.

## Ограничения

CI success confirms source compilation, unit tests and remapping only. Minecraft startup with the complete 145-file pack, mixin interactions in runtime, and measured RAM savings have not yet been verified.

Не включать experimental compact map до теста на backup world.
