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
- Current version: 0.4.0
- Current HEAD (2026-11-28): 593103d924482fc687a7a07f39108879fc2dd5e5

## Реализовано в 0.4.0 — JFR Allocation Profiler

### 1. Короткие профили по запросу

JFR-профайлер выключен по умолчанию. Запускать его может оператор с permission level 2:
- `/optimizationfix profile start` — 60 секунд;
- `/optimizationfix profile start 90` — заданная длительность от 15 до 180 секунд;
- `/optimizationfix profile status` — состояние и оставшееся время;
- `/optimizationfix profile stop` — досрочно остановить запись;
- `/optimizationfix profile` — показать состояние.

JFR recording ограничен:
- временем профиля максимум 180 секунд;
- disk repository max size 128 MiB;
- max age около длительности + 15 секунд;
- хранятся последние пять завершённых `.jfr`-файлов.

Включённые события:
- `jdk.ObjectAllocationSample` со stack trace и throttle 150 samples/s;
- `jdk.GarbageCollection`;
- `jdk.GCHeapSummary`;
- `jdk.ThreadAllocationStatistics` раз в секунду.

### 2. Анализ .jfr

После остановки файл записывается в `logs/optimizationfix/profiles`, рядом создаётся текстовый отчёт. Потоковое чтение/агрегация запускается в отдельном служебном worker thread, не в server tick.

Итог включает:
- количество allocation samples;
- суммарный JFR sample weight по классам объектов;
- top allocation sites по первому frame вне JDK;
- количество GC, суммарную длительность и максимальную паузу;
- последние зарегистрированные heap usage Before GC / After GC;
- количество ThreadAllocationStatistics events.

**Критичная интерпретация:** `ObjectAllocationSample.weight` — статистический вес выборки, представляющий allocation pressure, а не точная запись каждого `new`, не retained heap и не объём объектов, всё ещё живых в памяти. Сравнивать распределение/горячие стеки, а не выдавать его за точную сумму удерживаемой RAM. Профиль со stack traces имеет overhead, поэтому не измерять по нему FPS/TPS.

Не включены heap dump, OldObjectSample и трассировка GC roots. Для подозрения на утечку это отдельное, более тяжёлое расследование; пока не активируем его автоматически.

### 3. Сохранение и анализ

Пути:
- `logs/optimizationfix/profiles/optimizationfix-*.jfr`
- `logs/optimizationfix/profiles/optimizationfix-*.txt`
- сводка также дописывается в `logs/minecraft-ai-agent/custom/latest.log` с WARN marker `[OPTIMIZATIONFIX-JFR]`.

При завершении записи по времени или остановке сервера профайлер также закрывает запись. JFR file и summary ограничены по размеру/числу сохранённых профилей.

## Реализовано в 0.3.0 — Memory Budget Governor

### 1. Периодическая телеметрия памяти

MemoryBudgetGovernor регистрируется через Fabric `ServerTickEvents.END_SERVER_TICK` и по умолчанию делает снимок раз в 200 server ticks (~10 секунд).

Снимок включает:
- JVM heap used / committed / max и процент от max;
- отдельные MemoryPoolMXBean: young/old generation, Metaspace, Code Cache и прочие доступные пулы;
- NIO direct и mapped BufferPoolMXBean;
- total/free физической RAM через OperatingSystemMXBean;
- JVM process committed virtual memory и process CPU load (если JVM/OS предоставляет их);
- количество живых Java-потоков, GC count/time;
- примерную скорость аллокаций по HotSpot ThreadMXBean per-thread allocation counters;
- измерения: loaded chunks, vanilla ServerChunkManager pending tasks, minY/maxY exclusive, число вертикальных секций;
- версии ModernFix, FerriteCore, C2ME, Distant Horizons и Big Globe, если установлены.

Thread allocation tracking включён в конфиге по умолчанию и активирует доступные HotSpot counters. Сумма считается по живым потокам; если поток завершился между замерами, дельта может быть занижена. Это не allocation profiler и не показывает классы/stack traces.

### 2. Memory pressure policy и безопасная реакция

Дефолты: WARN при 85% max heap, CRITICAL при 92%, повторное взведение действия при падении до 80%.

При первом CRITICAL-снимке в одном pressure episode опционально очищаются только bounded weak-value lookup indexes самого Optimization Fix. NBT values, мир, сущности, чанки и сохранения не модифицируются. Поскольку значения интернеров слабые, освобождается только метадата индексов; это ограниченный safety valve, а не способ высвободить гигабайты heap. После очистки индекс заново наполняется и временно даёт меньше dedup hits.

Governor не вызывает `System.gc()`, не выгружает чанки/сущности, не меняет view/simulation distance, не меняет высоту измерений и не трогает кэши/очереди других модов.

### 3. Диагностика высоких измерений и очередей

Для каждого ServerWorld выводятся:
- loadedChunks;
- ServerChunkManager pendingTasks;
- bottomY и topY exclusive;
- verticalSectionsPerChunk;
- loadedChunkSectionSlots = loadedChunks × verticalSectionsPerChunk.

Последнее поле — потенциальный диапазон секций в загруженных чанках, а не точное число непустых/реально выделенных секций.

Граница интеграции:
- Vanilla ServerChunkManager pendingTasks не включает гарантированно все приватные очереди C2ME. В отчёте счётчик так и обозначен, C2ME внутреннюю очередь не изменяет.
- Внутренний LOD cache Distant Horizons не очищается/не измеряется через неподтверждённый API. Мод лишь определяет наличие и версию DH.
- Никакой автоподстройки worldgen, загрузки чанков, height limits или DH LOD не выполняется.

### 4. Новые команды

- `/optimizationfix stats` — свежий NBT + memory budget snapshot; обычный WARN log и тот же `logs/minecraft-ai-agent/custom/latest.log`.
- `/optimizationfix trim` — вручную очищает только собственные weak lookup indexes; запись также идёт в custom RAM log.
- `/optimizationfix profile start [15-180]` / `stop` / `status` — короткая запись JFR и сводка по аллокациям/GC.
- `/optimizationfix reload` — перечитывает governor/NBT config, Mixin-решение по ModernFix остаётся startup-only.

### 5. Конфигурация 0.3.0

Добавлены:
- `governor.enabled=true`
- `governor.interval_ticks=200`
- `governor.heap_warn_percent=0.85`
- `governor.heap_critical_percent=0.92`
- `governor.heap_rearm_percent=0.80`
- `governor.trim_own_caches_on_critical=true`
- `telemetry.track_thread_allocations=true`

Конфиг мигрирует на version 3 без изменения target 1.20.1 / Fabric / Java 17.

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

- /optimizationfix stats — подробный NBT + Memory Budget Governor отчёт в chat, WARN log и AI Agent custom RAM log.
- /optimizationfix trim — очистить собственные bounded weak lookup indexes.
- /optimizationfix reload — перечитать конфиг и пересоздать interners; governor применяет настройки сразу, ModernFix Mixin compatibility остаётся startup-only.
- Config: config/optimizationfix.properties, config.version=3.
- Default options дополнительно: governor.enabled=true, interval_ticks=200, heap_warn=0.85, heap_critical=0.92, heap_rearm=0.80, trim_own_caches_on_critical=true, track_thread_allocations=true.

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

## Сборка и артефакт 0.4.0

- GitHub Actions run #13 / ID 46177975840: **success** для exact HEAD `593103d924482fc687a7a07f39108879fc2dd5e5`.
- JDK 17, Gradle build, JUnit tests и upload artifact: success.
- Artifact ID 15276930103; ZIP `optimization-fix-0.4.0-artifact.zip`, 85,362 bytes, SHA256 `00a1f9bbc98b3159ff2059d90519eea53d0689ed2f98b26b4f2ca0586f9769ec`.
- Main JAR `optimization-fix-0.4.0.jar`, 61,506 bytes, SHA256 `86d696ff431f9171d1f11133cdec648680ddb26513cb68241a84c035b370dbf3`.
- Sources JAR `optimization-fix-0.4.0-sources.jar`, 32,623 bytes, SHA256 `268aad7de34df862df445f2c314a4a9eefe32bd7ae533498a303f6877cb04273`.
- ZIP and JAR were downloaded and inspected locally. JAR metadata: id=optimizationfix, version=0.4.0, Minecraft 1.20.1, Java >=17, Fabric API >=0.92.12+1.20.1. Confirmed JFR profiler, bounded counter, Governor and command classes are packaged.
- CI/unit tests passed. Actual JFR recording inside the user's complete modpack and live compatibility test remain pending.

## Сборка и артефакт 0.2.0

- GitHub Actions run #11 / ID 46175808662: success для exact HEAD ea5118ab3240e78458f399a96279308e7200c982.
- Build, JUnit tests, adaptive-map tests и artifact upload successful.
- Artifact ID 15274819734, ZIP optimization-fix-1.20.1, 44,949 bytes, SHA256 56f1e9becfaf8dd7195b5fd4680ba6435c12b46d3f884fab4e037fa6eb504616.
- JAR: optimization-fix-0.2.0.jar, 33,515 bytes, SHA256 982f3cf5a173d5bb1321cae6e2f5b0926b11253890ac79351d5fed837572f734.
- ZIP contains remapped JAR and sources JAR. Fabric metadata verified as Minecraft 1.20.1 / Java >=17 / Fabric API >=0.92.12+1.20.1; expected Mixin config, plugin and classes are present.
- CI/unit tests passed. In-game startup and full modpack runtime tests are pending.

## Сборка и артефакт 0.3.0

- GitHub Actions run #12 / ID 46177383315: **success** для exact HEAD `d0c1ff4b9efa9d9ebfcb70f837a88ee968c861f4`.
- Checkout, JDK 17, Gradle build + JUnit tests, artifact upload: successful.
- Artifact ID 15276211763, ZIP `optimization-fix-0.3.0-artifact.zip`, 65,451 bytes, SHA256 `838d1265b84e575aa7d4eaeb0f9b76d2c764b84c43d020b25390f1b13f04276b`.
- Main JAR `optimization-fix-0.3.0.jar`, 47,629 bytes, SHA256 `b90201f16bf1eadff2d6a0448a5fcfbab525969ab556c3e829309973ffc004dd`.
- Sources JAR `optimization-fix-0.3.0-sources.jar`, 26,043 bytes.
- JAR metadata checked: mod ID optimizationfix, version 0.3.0, Minecraft 1.20.1, Java >=17, Fabric API >=0.92.12+1.20.1. New Governor/policy/command classes are present.
- CI and unit tests passed. In-game startup and the complete user modpack runtime test remain pending; do not claim measured RAM savings before A/B tests.

## Следующий этап

1. Установить 0.4.0 на отдельном профиле/копии мира, не на единственном основном мире; удалить старые JAR Optimization Fix, не держать две версии Mod ID.
2. Проверить startup и Mixin application с текущими ModernFix 5.25.2 и остальными модами.
3. Проверить загрузку/сохранение NBT, инвентари, сундуки, entity data, chunk save/load и переходы между измерениями.
4. Запустить /optimizationfix stats и проверить блок WARN в custom RAM log.
5. Для профиля повторяемой нагрузки: /optimizationfix profile start 60; 60 секунд выполнять один одинаковый сценарий (например, пройти один и тот же маршрут в мире либо исследовать новые чанки), затем /optimizationfix profile stop.
6. Забрать .jfr и .txt из logs/optimizationfix/profiles. Отправить оба файла для разбора allocation top classes/sites. Не использовать сам профиль как FPS/TPS benchmark.
7. Для A/B сравнения использовать один мир, одинаковый маршрут/время сессии, те же настройки DH и одинаковый Xmx; сравнить heap/GC/direct/native/system RAM, loaded chunks и allocation rate.
8. Отдельно проверить авто-реакцию на критическом heap-пороге в тестовом профиле; не провоцировать OOM в основном мире.
9. Полный runtime-тест со всей пользовательской сборкой, включая фактическую работу JFR в игровом Java runtime, остаётся pending. CI success не означает, что in-game совместимость уже подтверждена. CI success не означает, что in-game совместимость уже подтверждена.
