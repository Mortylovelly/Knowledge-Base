# 004 — Optimization Fix 0.4.0: On-demand JFR Allocation Profiler

Дата: 28.11.2026  
Minecraft: 1.20.1 Fabric / Java 17  
Repo: https://github.com/Mortylovelly/Fix-test  
Branch: main  
Commit: `593103d924482fc687a7a07f39108879fc2dd5e5`

## Что добавлено

- Короткая ручная JFR recording-сессия по команде, выключенная по умолчанию.
- `/optimizationfix profile start` — 60 секунд; можно задать 15–180 секунд.
- `/optimizationfix profile status`, `/optimizationfix profile stop`.
- События allocation samples, stack traces, GC, heap summaries и ThreadAllocationStatistics.
- JFR repository ограничен maxSize 128 MiB, maxAge зависит от длительности профиля; хранятся последние пять .jfr записей.
- Потоковый анализ файла: веса аллокаций по object class, топ sites по первому non-JDK stack frame, GC count/pause и last heap summary.
- .jfr + .txt сохраняются в `logs/optimizationfix/profiles`; отчёт попадает в AI Agent custom RAM WARN log.
- Профайлер автоматически останавливается по истечении времени и при остановке сервера.

## Интерпретация

`jdk.ObjectAllocationSample.weight` — статистический вес sample event, а не точный полный byte counter и не retained heap. Сравнивать горячие classes/sites и относительные веса; затем разбирать .jfr в JDK Mission Control. Запись со stack traces имеет overhead, поэтому профиль не предназначен для сравнения FPS/TPS.

Heap dump, OldObjectSample и GC-root tracing не включены, поскольку это более тяжёлые leak diagnostics. Это не выполняющийся постоянно RAM optimizer, а необходимый шаг для определения реальных горячих точек до следующих targeted optimizations.

## Артефакты и CI

- GitHub Actions run #13 / ID 46177975840: success.
- Commit: `593103d924482fc687a7a07f39108879fc2dd5e5`.
- ZIP artifact ID 15276930103: `optimization-fix-0.4.0-artifact.zip`, 85,362 bytes, SHA256:
  `00a1f9bbc98b3159ff2059d90519eea53d0689ed2f98b26b4f2ca0586f9769ec`.
- Main JAR: `optimization-fix-0.4.0.jar`, 61,506 bytes, SHA256:
  `86d696ff431f9171d1f11133cdec648680ddb26513cb68241a84c035b370dbf3`.
- Sources JAR: `optimization-fix-0.4.0-sources.jar`, 32,623 bytes, SHA256:
  `268aad7de34df862df445f2c314a4a9eefe32bd7ae533498a303f6877cb04273`.
- JAR metadata and relevant class presence checked locally; CI and tests successful.
- Full user modpack runtime test and actual JFR recording remain pending.

## Первое профилирование в игре

1. Установить 0.4.0 в отдельном тестовом профиле, удалить предыдущий Optimization Fix JAR.
2. Запустить мир и прогреть его в течение 2–3 минут обычной игрой.
3. Выполнить `/optimizationfix profile start 60`.
4. В течение минуты выполнить один типовой сценарий: обычный бег по уже загруженным чанкам, worldgen/исследование новых чанков или телепорт по измерению — не смешивать разные сценарии в одном профиле.
5. Остановить командой `/optimizationfix profile stop` либо дождаться автоматической остановки.
6. Забрать файлы `logs/optimizationfix/profiles/*.jfr` и соответствующий `.txt`.
7. Отдельно прислать `latest.log` и `logs/minecraft-ai-agent/custom/latest.log`, если в отчёте видна ошибка или profiler unavailable.
8. Не объявлять RAM savings без сравнения одинаковых сценариев до/после и не менять оптимизационные Mixin без анализа top allocation sites.

