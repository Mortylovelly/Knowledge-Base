# 003 — Optimization Fix 0.3.0: Memory Budget Governor

Дата: 28.11.2026  
Minecraft: 1.20.1 Fabric / Java 17  
Repo: https://github.com/Mortylovelly/Fix-test  
Branch: main  
Commit: `d0c1ff4b9efa9d9ebfcb70f837a88ee968c861f4`

## Что добавлено

- Периодическая телеметрия heap, JVM memory pools, direct/mapped buffers, системной физической RAM, GC и live Java threads.
- Приблизительная скорость аллокаций через HotSpot ThreadMXBean counters; это не allocation profiler, и завершившиеся между замерами потоки могут занижать дельту.
- Телеметрия ServerWorld: loaded chunks, ServerChunkManager pending tasks, minY/maxY, vertical sections per chunk и slots capacity indicator.
- Детект версии ModernFix, FerriteCore, C2ME, Distant Horizons и Big Globe.
- Pressure thresholds 85% WARN / 92% CRITICAL / 80% re-arm по умолчанию.
- При первом critical event за эпизод можно очистить только ограниченные weak lookup indexes Optimization Fix.
- Новая команда `/optimizationfix trim`; `/optimizationfix stats` теперь сохраняет в custom RAM log полный NBT + governor report.
- Config version 3.

## Важные ограничения

Governor не вызывает System.gc(), не выгружает чанки/сущности, не меняет view distance, world height, генерацию или геймплей. Не очищает кэши других модов и не меняет чужие очереди.

Очистка слабых интернер-индексов удаляет hash/weak-reference metadata, но не удаляет объекты NBT, которые удерживаются самим Minecraft. Это небольшая защитная мера, а не средство освободить большой объём RAM.

`loadedChunkSectionSlots` — произведение загруженных чанков на вертикальные секции измерения, не точное число непустых/реально выделенных секций. `ServerChunkManager pendingTasks` не является полной внутренней очередью C2ME. LOD cache Distant Horizons не измеряется и не меняется без подтверждённого API.

## Сборка / артефакты

- GitHub Actions run #12 / ID 46177383315: success.
- Commit: `d0c1ff4b9efa9d9ebfcb70f837a88ee968c861f4`.
- ZIP artifact ID: 15276211763.
- ZIP: `optimization-fix-0.3.0-artifact.zip`, 65,451 bytes, SHA256:
  `838d1265b84e575aa7d4eaeb0f9b76d2c764b84c43d020b25390f1b13f04276b`.
- JAR: `optimization-fix-0.3.0.jar`, 47,629 bytes, SHA256:
  `b90201f16bf1eadff2d6a0448a5fcfbab525969ab556c3e829309973ffc004dd`.
- Sources JAR: `optimization-fix-0.3.0-sources.jar`, 26,043 bytes.
- JAR metadata and presence of Governor/policy/command classes checked.
- CI/JUnit passed. Full in-game user-modpack test pending.

## Первичная проверка в игре

1. Установить JAR в отдельный тестовый профиль Minecraft 1.20.1 Fabric.
2. Удалить предыдущую версию Optimization Fix из `mods`.
3. Загрузить тестовый мир, подождать 30–60 секунд, затем выполнить `/optimizationfix stats`.
4. Проверить startup.log/latest.log на Mixin warnings/errors и секцию `[OPTIMIZATIONFIX-STATS]`.
5. Проверить, что `logs/minecraft-ai-agent/custom/latest.log` содержит WARN-строки отчёта.
6. В Big Globe проверить значения minY/maxY, loadedChunks и verticalSectionsPerChunk в нужном измерении.
7. Проверить `/optimizationfix trim` на копии мира: команда должна сообщить только число очищенных индексных записей, без изменения мира.
8. Пока не подтверждены реальные игровые тесты, не заявлять измеренное снижение RAM и не включать это в основной профиль без бэкапа.

