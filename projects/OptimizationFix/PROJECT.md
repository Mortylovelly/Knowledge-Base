# Optimization Fix

## Назначение

Отдельный Fabric-мод для снижения накладных расходов памяти на NBT в большой пользовательской сборке без изменения игровых механик, worldgen, мобов, рендера или тиков.

## Целевая платформа

- Minecraft 1.20.1
- Fabric
- Java 17
- Fabric Loader 0.19.5
- Fabric API 0.92.12+1.20.1
- Yarn 1.20.1+build.10
- Gradle 8.12

Не менять target Minecraft/loader без прямого указания владельца.

## Репозиторий

- GitHub: https://github.com/Mortylovelly/Fix-test
- Branch: main
- Mod ID: optimizationfix
- Display name: Optimization Fix
- Current version: 0.2.0
- Current HEAD (2026-11-28): ea5118ab3240e78458f399a96279308e7200c982

## Реализовано в 0.2.0

### 1. NBT String pre-allocation deduplication

Mixin к NbtString.of(String) сначала проверяет bounded weak-value interner на HEAD метода. Если уже существует равный живой NbtString, он возвращается до вызова ванильного конструктора NBT-обёртки. При cache miss ванильный объект индексируется на RETURN; при конкурентной вставке возвращается канонический объект.

- preFactoryHits / wrapperAllocationsAvoided — точное число случаев, когда повторная NBT-обёртка не создавалась.
- Входной String уже существует до вызова фабрики; его предварительное выделение не отменяется.
- Строки сравниваются полностью, hash collision не объединяет различные значения.

### 2. Bounded weak-value interners

Отдельные ограниченные Guava-кэши для NbtString и ключей NbtCompound:
- максимум 4096 hash entries на кэш по умолчанию;
- weak values, без бессрочного удерживания NBT-объектов;
- лимит индексируемой строки по умолчанию 128 символов;
- коллизии hashCode приводят к пропуску индексирования, а не к неверному объединению значений;
- счётчики lookup/hit/miss/index/reuse/collision/skipped/live entries/cleared weak references.

### 3. Адаптивная карта NBT Compound — включена в конфиге

experimental.compact_compound_maps=true включена по умолчанию и выставляется true при миграции старого конфига 0.1.0.

- Новый NbtCompound сначала использует fastutil array-backed map.
- До 8 различных ключей хранение остаётся компактным.
- При вставке 9-го различного ключа происходит одноразовое продвижение в fastutil open-addressing hash map.
- Это уменьшает накладные расходы малых Compound, но не оставляет крупные Compound с линейным поиском.
- Счётчики: созданные компактные карты и число promoted-to-hash-map.

### 4. Автоматическая совместимость с ModernFix

В текущем списке модпака (снимок 145 файлов от 22.11.2026) указан ModernFix 5.25.2.

ModernFix patch mixin.perf.nbt_memory_usage уже дедуплицирует имена ключей Compound и использует array map для очень маленьких NBT Compound. Это пересечение напрямую подтверждено документацией ModernFix.

Optimization Fix проверяет config/modernfix-mixins.properties:
- если ModernFix отсутствует — собственные Compound key/map Mixin разрешены;
- если mixin.perf.nbt_memory_usage=true или параметр отсутствует (default) — собственные NbtCompoundMixin и NbtCompoundStorageMixin подавляются;
- если параметр явно false — собственные функции доступны;
- если конфиг не удаётся прочитать — fail-safe: дублирующие Compound Mixin подавляются.

Проверка кэшируется на старте, потому что решения Mixin нельзя безопасно переключать командой reload. До завершения загрузки конфига Compound-функции также fail-closed (false). Независимый NbtString value interner остаётся включённым.

### 5. Измерение эффективности и логирование

Команда (permission level 2): /optimizationfix stats

Команда выводит отчёт в чат и обычный лог Minecraft с маркером [OPTIMIZATIONFIX-STATS] и уровнем WARN. Дополнительно каждая строка попадает в кастомный RAM-лог Minecraft AI Agent:

logs/minecraft-ai-agent/custom/latest.log

Формат кастомной строки: [timestamp] [WARN] [OPTIMIZATIONFIX-STATS] [CUSTOM]. Архив ротируется на 4 MiB в latest.log.1 и latest.log.2, по схеме custom-log каталога AI Agent.

Отчёт включает:
- exact count NbtString wrappers avoided by pre-factory hits;
- cumulative estimated duplicate object-graph bytes not retained;
- NBT string/key interner lookup and collision statistics;
- adaptive map creation and promotion counts;
- effective and configured enable/disable state of each Optimization Fix feature;
- ModernFix detected / nbt_memory_usage status and compatibility reason;
- JVM heap used/committed/max, old generation, GC counts/time, collector names;
- UseStringDeduplication JVM flag and HotSpot layout assumptions.

Граница измерения: байтовая оценка — приблизительная верхняя оценка размера дублируемого объектного графа (UTF-16 payload estimate), а не прямое измерение свободной RAM, не текущая разница heap и не гарантия фактической очистки GC. Heap/GC — моментальный снимок на время команды. Мод не вызывает принудительный GC.

### 6. JVM-level string dedup hint

Если отчёт показывает G1 и UseStringDeduplication=false, дополнительный необязательный Java 17 launcher flag: -XX:+UseStringDeduplication

В Java 17 этот флаг выключен по умолчанию и требует G1. Не включать вслепую: сравнить heap/GC на одинаковом сценарии.

## Команды и конфигурация

- /optimizationfix stats — подробный отчёт в chat, WARN log и AI Agent custom RAM log.
- /optimizationfix reload — перечитать конфиг и пересоздать interners; Mixin compatibility decisions фиксируются на старте и не меняются без перезапуска.
- Config: config/optimizationfix.properties.
- Default options:
  nbt.deduplicate_strings=true,
  nbt.deduplicate_compound_keys=true,
  experimental.compact_compound_maps=true,
  interner.max_entries_per_type=4096,
  interner.max_string_length=128.

## Аудит существующего модпака и пересечения

Проверен сохранённый список из 145 файлов от 22 ноября 2026 года в Knowledge-Base, Fauna update 005. Это датированный снимок владельца, не live-скан папки mods на 28 ноября.

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
- ModernFix mixin.perf.nbt_memory_usage — прямое пересечение с Compound key dedup и compact-map storage; автоматически подавляется при активном ModernFix patch.
- FerriteCore имеет свои оптимизации blockstate/model maps и ряда структур памяти; не копировать эти функции.
- MemoryLeakFix исправляет конкретные lifecycle/reference leak cases; не заменять ими собственную логику dedup.
- Не дублировать функции Lithium, C2ME, Noisium, ServerCore, VMP и других установленных оптимизационных модов без отдельного подтверждённого пробела.
- Ванильные numeric NBT scalar types уже используют собственные кэши; Optimization Fix не добавляет дублирующую числовую пуллинг-систему.

## Сравнение с игровыми движками

Публичные комментарии разработчиков GTA VI описывают масштабную переработку LOD, распараллеливание систем и приоритизацию streaming ради работы в ограниченной памяти. Это engine-level asset/world streaming, которое нельзя прямо переносить в Java/Fabric мод как универсальный RAM-патч. Для Optimization Fix применены переносимые принципы: не удерживать объекты сильными ссылками бессрочно, переиспользовать повторяющиеся immutable values до создания wrapper, хранить малые структуры компактно и переходить к hash lookup для больших. Не заявлять, что это эквивалент движку Rockstar.

## Сборка и артефакт 0.2.0

- GitHub Actions run #11 / ID 46175808662: success для exact HEAD ea5118ab3240e78458f399a96279308e7200c982.
- Build, JUnit tests, adaptive-map tests и artifact upload successful.
- Artifact ID 15274819734, ZIP optimization-fix-1.20.1, 44,949 bytes, SHA256 56f1e9becfaf8dd7195b5fd4680ba6435c12b46d3f884fab4e037fa6eb504616.
- JAR: optimization-fix-0.2.0.jar, 33,515 bytes, SHA256 982f3cf5a173d5bb1321cae6e2f5b0926b11253890ac79351d5fed837572f734.
- ZIP contains remapped JAR and sources JAR. Fabric metadata verified as Minecraft 1.20.1 / Java >=17 / Fabric API >=0.92.12+1.20.1; expected Mixin config, plugin and classes are present.
- CI/unit tests passed. In-game startup and full modpack runtime tests are pending.

## Следующий этап

1. Установить на отдельном профиле/копии мира, не на единственном основном мире.
2. Удалить старый optimization-fix-0.1.0.jar, не держать две версии одного Mod ID.
3. Проверить startup, Mixin application, загрузку/сохранение NBT, инвентари, сундуки, entity data, chunk save/load и переходы между измерениями.
4. Запустить /optimizationfix stats после обычной игры и после загрузки мира; проверить WARN-блок в custom RAM log.
5. Снимать одинаковые heap/GC/native/process показатели до и после в сопоставимых сценариях.
6. Не заявлять процент/объём экономии RAM без контролируемого A/B-измерения.
7. Полный runtime-тест со всей пользовательской сборкой остаётся pending.
