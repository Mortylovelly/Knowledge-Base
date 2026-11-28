# Optimization Fix

## Назначение

Отдельный Fabric-мод для снижения потребления памяти в большой пользовательской сборке без изменения игровых механик.

## Целевая платформа

- Minecraft 1.20.1
- Fabric
- Java 17
- Fabric Loader 0.19.5
- Fabric API 0.92.12+1.20.1
- Yarn 1.20.1+build.10
- Gradle 8.12

**Не менять target Minecraft/loader без прямого указания владельца.**

## Репозиторий

- GitHub: https://github.com/Mortylovelly/Fix-test
- Branch: `main`
- Mod ID: `optimizationfix`
- Display name: `Optimization Fix`
- Current version: `0.1.0`
- Current HEAD (2026-11-28): `6bbe8693edddd21adfaa506df3a42c24d2b4eaac`
- Code/test commit: `62007079586fbb0af828efb8a8c4ead065230ef5`

## Реализовано в 0.1.0

### Включено по умолчанию

1. **NBT String deduplication.** Mixin к `NbtString.of(String)` повторно использует уже существующий живой `NbtString` с полностью равным текстом.
2. **NBT Compound key deduplication.** Перед `NbtCompound.put(String, NbtElement)` строковый ключ проверяется на точное равенство с живой канонической строкой.
3. **Bounded weak-value interner.**
   - Отдельный Guava cache для NbtString и ключей Compound.
   - По умолчанию максимум 4096 hash entries на каждый кэш.
   - В каждой записи только одна WeakReference.
   - При коллизии hashCode разных строк новая строка пропускается; разные значения не объединяются.
   - Пустые и строки длиннее лимита не индексируются; default max length 128.
   - Кэш не держит NBT объекты или строки сильными ссылками.

### Экспериментальная функция — выключена

Опция `experimental.compact_compound_maps=false` меняет backing map новых пустых `NbtCompound` на fastutil open-addressing map с начальной ёмкостью 2. Функция оставлена отключённой из-за возможного отличия порядка обхода Map. Не включать в основной мир до отдельного теста на копии мира и полной сборки.

## Команды и конфигурация

- `/optimizationfix stats` — счётчики reuse/indexed/skipped/collisions/cache entries/cleared weak references.
- `/optimizationfix reload` — перечитать конфиг и пересоздать кэши.
- Config: `config/optimizationfix.properties`.
- Default options:
  `nbt.deduplicate_strings=true`,
  `nbt.deduplicate_compound_keys=true`,
  `experimental.compact_compound_maps=false`,
  `interner.max_entries_per_type=4096`,
  `interner.max_string_length=128`.

Счётчики показывают повторное использование, но не дают прямого значения сэкономленных MiB.

## Аудит существующего модпака

Проверен сохранённый список из 145 файлов от **22 ноября 2026 года** в Knowledge-Base, Fauna update 005. Это датированный снимок владельца, не live-скан папки mods на 28 ноября.

Ключевые уже установленные оптимизационные моды:
- FerriteCore 6.0.1
- ModernFix 5.25.2
- MemoryLeakFix 1.1.5
- BadOptimizations 2.4.1
- Lithium 0.11.4
- C2ME alpha.11.18
- Noisium 2.3.0
- ServerCore 1.5.2
- VMP beta.7.102
- Krypton 0.2.3
- Alternate Current 1.9.0
- Distant Horizons 3.2.0-b

Также присутствуют Sodium 0.5.13, Iris 1.7.6, Big Globe 5.3.2, Create и аддоны, существа, worldgen и YUNG's structures.

Пересечения, которые нужно уважать:
- FerriteCore имеет свои оптимизации blockstate/model maps и ряда структур памяти; не копировать эти функции.
- ModernFix имеет отдельные статические NBT/dedup пути для BlockStateData/Patchouli и оптимизации моделей/регистров; не копировать их.
- MemoryLeakFix исправляет конкретные lifecycle/reference leak cases; в проверенной версии его TagKey weak-interner был привязан к версии 1.18.2 и не совпадает с текущей runtime NbtString/Compound-key областью.
- Не дублировать функции Lithium, C2ME, Noisium, ServerCore, VMP и других установленных оптимизационных модов без отдельного подтверждённого пробела.

На дату исходного аудита не обнаружен общий runtime interner обычных NbtString значений и ключей NbtCompound в перечисленных совместимых оптимизационных модулях. Перед будущим расширением повторять аудит установленного jar/версии каждого мода.

## Сборка и артефакт

- GitHub Actions run #6 / ID `46089594257`: success для exact latest HEAD `6bbe8693edddd21adfaa506df3a42c24d2b4eaac`.
- Build, tests, remapJar и artifact upload successful.
- Artifact ID `15240482782`, ZIP `optimization-fix-1.20.1`, SHA256 `06671e8bff8136bd51aec655ebc76b74e23143f388f18cb6819e7a9047320cda`.
- JAR: `optimization-fix-0.1.0.jar`, 15,420 bytes, SHA256 `28c48d294bdd0d673acc45a3db1610a21e981414f85d9ee75c5e16b2dfcd093f`.
- JAR inspection: Fabric metadata, Mixin config, remap refmap and all expected mod classes present.
- CI/unit tests passed. In-game startup and full modpack runtime tests are **pending**.

## Следующий этап

1. Тестировать на отдельном профиле/копии мира, не на единственном основном мире.
2. Проверить startup, Mixin application, загрузку/сохранение NBT, инвентари, сундуки, энтити и переходы между измерениями.
3. Выполнить `/optimizationfix stats` после обычной игры и после загрузки мира.
4. Снимать одинаковые heap/GC/native/process показатели до и после, при сопоставимых условиях.
5. Не заявлять процент/объём экономии RAM без контролируемого измерения.
6. Compact NBT maps остаются выключенными до отдельной runtime/compatibility проверки.
