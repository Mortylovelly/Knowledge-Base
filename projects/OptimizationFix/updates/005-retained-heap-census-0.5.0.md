# 005 — Optimization Fix 0.5.0: Retained Heap Class Census

Дата: 28.11.2026  
Minecraft: 1.20.1 Fabric / Java 17  
Repo: https://github.com/Mortylovelly/Fix-test  
Branch: main  
Commit: `d154e76da67afbfe2506ec9c71e62e1884124276`

## Что добавлено

- Новая команда `/optimizationfix profile retained`, default 60 seconds, допустимо 15–90 секунд.
- В retained mode к предыдущим allocation/GC событиям добавляется JFR `jdk.ObjectCountAfterGC`.
- Анализирует число экземпляров и totalSize по классам после реального GC.
- Держит последний GC id snapshot, не суммируя память одного и того же live heap между несколькими GC.
- Ограничивает агрегацию 4096 классами, показывает top классов по total bytes и instance count.
- Данные анализируются в worker thread; сохраняются .jfr + .txt, сводка попадает в custom RAM WARN log.
- Добавлены тесты нового census aggregator и наличия обоих JFR events на Java 17.

## Границы и безопасность

- Retained mode не вызывает System.gc() и не инициирует heap dump.
- Если GC за короткое окно не случился или JVM не предоставила event, отчёт честно указывает отсутствие class census.
- JFR ObjectCountAfterGC относится ко всему JVM процессу — Minecraft/vanilla, Fabric, все моды и библиотеки. Событие не распределяет байты по Mod ID и не показывает, какой field/reference удерживает объект.
- Census после GC — point-in-time class histogram. Большой класс count/size не доказывает утечку; для leak diagnosis нужно наблюдать рост live set в стабильной нагрузке и исследовать ссылки.
- Detailed GC event может добавлять CPU/pause overhead. Поэтому retained mode выключен по умолчанию, ограничен 90 секундами и не предназначен для сравнения FPS/TPS.
- OldObjectSample, GC root paths и heap dump не включены автоматически; это отдельный, более тяжёлый следующий этап.

## Артефакты и CI

- GitHub Actions run #14 / ID 46178610272: success.
- Commit: `d154e76da67afbfe2506ec9c71e62e1884124276`.
- ZIP artifact ID 15276058901: `optimization-fix-0.5.0-artifact.zip`, 91,687 bytes, SHA256:
  `d950c3c52a8d15b750a881f70ee176e31513253e00e5e1401a21ed3c654f7a44`.
- Main JAR `optimization-fix-0.5.0.jar`, 66,123 bytes, SHA256:
  `401728fde5006fad012cb763a278bead3afffd961ffeee1b7edb8494c449dbf4`.
- Sources JAR `optimization-fix-0.5.0-sources.jar`, 34,843 bytes, SHA256:
  `30ba7aba1f5701e1237b072281a11eec33cfe5777b7253f11e6a2efa2071aff0`.
- Артефакт скачан и локально проверен: main JAR содержит метаданные optimizationfix 0.5.0 / Minecraft 1.20.1 / Java >=17 / Fabric API >=0.92.12+1.20.1; классы JFR profiler/ClassCensusSnapshot присутствуют.
- CI/JUnit tests successful. In-game run with the full user modpack and actual ObjectCountAfterGC event capture remain pending.

## Первое retained профилирование

1. Установить 0.5.0 на тестовый профиль Minecraft 1.20.1 Fabric и удалить прежний Optimization Fix JAR.
2. Запустить мир, дождаться прогрузки, выполнить `/optimizationfix stats`.
3. Выполнить `/optimizationfix profile retained 60`.
4. В течение минуты играть в одном сценарии. Не запускать вручную сборку мусора.
5. Дождаться автозавершения или выполнить `/optimizationfix profile stop`.
6. Найти .jfr и .txt в `logs/optimizationfix/profiles`.
7. Отправить .jfr, .txt, `logs/latest.log` и `logs/minecraft-ai-agent/custom/latest.log` для анализа.
8. Проверить наличие строки `ObjectCountAfterGC` и значение latestGcId. Если census пустой, это не означает отсутствие живых объектов — могло не быть подходящего GC в профиле.
9. Для сравнения повторить в сопоставимых сценариях/сессиях; не делать вывод об утечке по единственному snapshot.

