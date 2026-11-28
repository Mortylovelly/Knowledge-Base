# 016 — Minecraft AI Agent 0.3.22: расширенная диагностика памяти

- **Дата:** 2026-11-28
- **Проект:** Minecraft AI Agent
- **Линия Minecraft / ветка:** Minecraft 1.20.1 / Fabric, \`minecraft-1.20.1-fabric\`
- **Версия мода:** \`0.3.22\`
- **Статус сборки:** GitHub Actions #366 completed / success для exact HEAD
- **Runtime:** ещё не проверен непосредственно в Minecraft

## Зачем понадобилось обновление

В логах Minecraft около 03:22:57–03:25:23 старый профайлер сообщал ноль событий JFR каждые 15 секунд. При этом у JVM были активные выделения памяти и частые сборки G1. Также Windows-опрос памяти постоянно переходил в fallback \`tasklist-working-set-only\`, из-за чего Private Bytes и системный Available RAM не измерялись.

## Изменения 0.3.22

### JFR и распределение аллокаций по потокам

- Добавлено событие \`jdk.ThreadAllocationStatistics\` с периодом 2 секунды. Оно дополнительно показывает, поступают ли события из самого JFR stream, даже если ObjectAllocationSample / TLAB события отсутствуют.
- Добавлен независимый fallback через \`com.sun.management.ThreadMXBean\`: счётчик накопленных объектных аллокаций каждого Java-потока снимается каждые 2 секунды, вычисляется delta за интервал.
- В \`JFR-ALLOC\` добавлены thread counter status, число выборок, объём выделений и список потоков с максимальным объёмом аллокаций. Выводятся имя потока, ID, приблизительный владелец по текущему stack trace и верхний кадр.
- Связь текущего стека с модом является эвристической, а не точным attribution всех аллокаций: поток может выделять объекты в нескольких модулях или переключаться между задачами между моментами измерения.
- Предупреждение о нулевых ObjectAllocation events теперь выводится один раз за запуск, а не каждые 15 секунд.
- Сохранены JFR ObjectAllocationSample, ObjectAllocationInNewTLAB, ObjectAllocationOutsideTLAB и OldObjectSample. TLAB capacity и sampling weights по-прежнему являются оценками allocation pressure, не размером retained heap.

### Windows process / system memory

- Вместо зависимости от одного WMI Performance Counter для AvailableBytes добавлен прямой вызов Windows \`GlobalMemoryStatusEx\`, возвращающий total physical memory и available physical memory.
- Для процесса добавлен fallback через \`Win32_Process\`: Working Set, Private Page Count (fallback для Private Bytes) и Virtual Size.
- Если WMI Performance Counter недоступны, остаётся нативный Available RAM и базовые показатели процесса; источник каждого значения явно отражается в \`processMemorySource\` / \`systemMemorySource\`.
- Ошибка PowerShell/WMI не скрывается полностью: краткая причина попадает в поле source при переходе к tasklist fallback.
- \`WorkingSetPrivate\`, системные cache/commit/pools остаются n/a, если соответствующие performance counters действительно недоступны; не подменять их похожими, но семантически другими числами.

### Что уже было в 0.3.21 и сохранено

- Heap / OldGen / NonHeap / Direct / Mapped, GC counts/times и thread count.
- Process Working Set, Private Working Set, Private Bytes, Virtual Bytes.
- System Available / Committed / Cache / pools, когда Windows counters доступны.
- Optional Native Memory Tracking через \`jcmd\`; NMT нельзя включить после старта JVM.

Для следующего диагностического запуска можно добавить в JVM arguments:
\`\`\`text
-XX:NativeMemoryTracking=summary
\`\`\`
Затем полностью перезапустить Minecraft. Без этой опции NMT останется disabled, даже если \`jcmd\` присутствует.

## Анализ исходного фрагмента логов

В показанном отрезке:
- Java Working Set был около 9.0 GiB.
- Heap used колебался примерно от 3.2 до 5.3 GiB; heap max — 13.28 GiB.
- OldGen рос примерно с 3.2 до 4.46 GiB, затем около 03:25:07 снизился до 3.02 GiB. Это означает, что значительная часть накопленных объектов была освобождена GC; этот интервал сам по себе не доказывает непрерывную утечку.
- Direct buffers были около 23 MiB, mapped buffers около 55 MiB, NonHeap около 584 MiB.
- Private Bytes / Private Working Set были n/a; NMT был disabled; системный AvailableBytes был unavailable. Поэтому разницу между ~9 GiB Working Set и Java heap нельзя было надёжно разложить по native/JVM категориям.
- JFR allocation event counters были нулевыми, parseFailures — 0. Это означает, что обработчик не получил событий, а не то, что Minecraft или моды не выделяли память.

## GitHub и проверенная сборка

- **HEAD:** \`ccf5b0a34b4f30276e541c9d8117f1dbb90d31b7\`
- **Commits этого обновления:** per-thread profiler fallback; Windows GlobalMemoryStatusEx / Win32_Process fallback; восстановление обработчика OldObjectSample.
- **GitHub Actions:** [run #366 / 46088228739](https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/46088228739) — completed / success, JDK 17, Build и Upload mod jar successful.
- **Artifact:** ID \`15240261099\`, name \`minecraft-ai-agent-1.20.1\`, ZIP size 350911 bytes.
- **Artifact ZIP SHA256:** \`4c69497faf2be2cb6436da10ab557e3eb91599b0089de35055aecc6f99299c11\`.
- **JAR:** \`minecraft-ai-agent-0.3.22.jar\`, 252483 bytes.
- **JAR SHA256:** \`5f1c310a98b089c7e791d6dd7b94711a571f14399c24350fcecc089345eb62c1\`.
- **Metadata:** mod version 0.3.22, Minecraft 1.20.1, Java >=17, Fabric Loader >=0.17.2, Fabric API >=0.92.11+1.20.1.
- JAR inspection подтвердил наличие \`AiAllocationProfiler\`, \`AiProcessMemoryProbe\`, \`AiNativeMemoryProbe\` и \`AiMemoryTelemetry\`, включая новые thread profiler classes.

## Что нужно проверить в игре

1. Установить только один JAR AI Agent версии 0.3.22; убрать старый JAR, чтобы не было двух версий.
2. Для native memory отчёта включить \`-XX:NativeMemoryTracking=summary\` и перезапустить JVM.
3. После загрузки мира проверить \`MEMORY-PROFILER-CAPABILITIES\`: значения доступности JFR event types и статус ThreadMXBean.
4. Проверить \`JFR-ALLOC\`: появились ли \`threadStatisticsEvents\`, \`threadCounterAllocated\`, \`topThreads\`; есть ли ObjectAllocation events и topMods.
5. Проверить \`JVM-MEMORY\`: значения processMemorySource, systemMemorySource, javaPrivateBytes, systemAvailable и NMT.
6. Прислать короткий отрезок этих строк после нескольких минут игры. Только после этого можно сравнивать allocation-heavy потоки, native memory и retained heap.
