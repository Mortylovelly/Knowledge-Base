# 017 — Minecraft AI Agent 0.3.23: восстановление CPU/GPU и отдельный график heap

- Дата: 2026-11-28
- Minecraft: 1.20.1
- Branch: minecraft-1.20.1-fabric
- Java: 17
- mod_version: 0.3.23
- Latest HEAD at documentation update: b72f82dd40cb540dc8523cac44d1a37f02902827
- CI: pending for final HEAD at time this entry was created
- Runtime: pending

## Что исправлено в Logs HUD

Верхняя диагностика возвращена к старому составу:
- RES — разрешение framebuffer;
- GPU — загрузка NVIDIA GPU через nvidia-smi;
- CPU — системная загрузка CPU;
- RAM — использование физической RAM всего ПК.

Отдельная строка памяти Minecraft:
- PROC — процесс Java, Working Set;
- HEAP — heap used / Xmx из параметров лаунчера;
- NHEAP — non-heap JVM.

Нижний график теперь называется MINECRAFT HEAP и показывает heap used как процент от Xmx. Для него используется текущий HeapMemoryUsage с интервалом 500 мс, а не системное RAM значение, которое раньше становилось пустым при недоступном Windows AvailableBytes.

Графики FPS, TPS, GPU и CPU сохранены. Высота диагностической области увеличена на 14 GUI-пикселей, чтобы новая строка и графики не перекрывали друг друга. Сохранены XYZ и DIM.

## Исправление Windows process/system memory

Старый PowerShell-запрос опирался на WMI Get-CimInstance; в пользовательском логе он завершался exit 3 с WQL filter error. Из-за этого:
- process memory падал до tasklist Working Set only;
- system AvailableBytes становился unavailable;
- system RAM индикатор и история оказывались пустыми;
- Private Bytes / Private Working Set не были доступны.

Новый probe больше не использует WMI/PerfProc:
- Win32 GlobalMemoryStatusEx даёт total physical и available physical memory;
- .NET System.Diagnostics.Process.GetProcessById получает WorkingSet64, PrivateMemorySize64 и VirtualMemorySize64 текущего Java процесса;
- unavailable private working set остаётся -1, а не подменяется похожим числом;
- process/system probe запускается раз в 5 секунд.

## Agent diagnostics API

get_agent_diagnostics теперь возвращает:
- system_ram_* — физическая RAM всей системы;
- java_working_set_bytes — резидентная память процесса Minecraft Java;
- java_private_bytes — private commit;
- java_heap_used_bytes, java_heap_max_bytes, java_heap_used_percent;
- charts.system_ram — история RAM всего ПК;
- charts.minecraft_heap — история Java heap Minecraft.

Описание tool и system instruction обновлены, чтобы AI различал системную RAM и heap Minecraft.

## Лог пользователя от 28.11.2026

В предоставленном отрезке:
- -Xmx13250M;
- heap в заключительной части около 4.9–5.5 GiB;
- Old Gen около 4.48 GiB;
- process Working Set около 8.9 GiB;
- system AvailableBytes и private process metrics были n/a из-за старого WMI/PowerShell path;
- JFR object allocation events = 0, но ThreadMXBean fallback показал около 9.14 GiB allocations за один 15-second interval, преимущественно в DH worldgen/render и render threads. Это allocation volume, не retained heap.
- NMT disabled; для отдельного запуска можно включить -XX:NativeMemoryTracking=summary до запуска JVM.

Optimization Fix 0.1.0 нельзя подтвердить по этому файлу: в приложенном логе нет строки инициализации mod ID optimizationfix и нет вывода /optimizationfix stats. Нужны стандартный logs/latest.log от старта игры и результат команды в игре.

## Проверка

CI и точный JAR для версии 0.3.23 будут добавлены после завершения GitHub Actions для финального commit. Runtime-проверка GUI, NVIDIA GPU polling, Windows native probe и полного modpack остаётся обязательной после установки JAR.
