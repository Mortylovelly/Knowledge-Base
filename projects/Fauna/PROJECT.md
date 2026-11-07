# Fauna- — Fix: Modpack Compatibility & Balance

## Кратко
- **Репозиторий:** [Mortylovelly/Fauna-](https://github.com/Mortylovelly/Fauna-)
- **Название проекта в сохранённом контексте:** Fix — Modpack Compatibility & Balance
- **Проектный root name:** Fix (по ранее просмотренному settings.gradle).
- **Mod ID:** `fix` (по ранее просмотренному fabric.mod.json).
- **Целевая линия:** Fabric 1.20.1 / Java 17 (по ранее просмотренным Gradle-файлам).

## Сохранённые параметры Gradle
Из ранее прочитанного `gradle.properties`:
- `minecraft_version=1.20.1`
- `yarn_mappings=1.20.1+build.10`
- `loader_version=0.19.5`
- `loom_version=1.17.21`
- `mod_version=0.4.1`
- `maven_group=com.mortylovelly.fix`
- `archives_base_name=fix`
- `fabric_api_version=0.92.12+1.20.1`

Ранее просмотренный build.gradle использовал:
- plugin `net.fabricmc.fabric-loom-remap` с версией из `loom_version`;
- Minecraft, Yarn mappings, Fabric Loader и Fabric API dependencies;
- `JavaCompile.options.release = 17`;
- source/target compatibility Java 17;
- processResources expand version;
- LICENSE rename в JAR.

**Важно:** последнее просмотренное значение Loom 1.17.21 не следует механически переносить в другие проекты. Wrapper в этом репозитории при прошлом просмотре не был найден. Перед сборкой нужно проверить актуальные Gradle wrapper / workflow / совместимость Gradle и Loom.

## Workflow
В ранее просмотренном `.github/workflows/build.yml`:
- checkout@v4;
- setup-java@v4 (в том workflow был указан Java 21, несмотря на target 17 в build.gradle — потенциальное несоответствие);
- gradle/actions/setup-gradle@v4;
- Gradle в workflow указывался как 9.5;
- запуск `gradle build --no-daemon`;
- upload artifact.

Нужно заново проверить актуальный workflow до изменений. При необходимости согласовать Java 17 и версию Gradle с используемым Loom.

## Остальная механика
Подробное описание конкретных функций, команд, конфигов, миксинов и проблем из прошлых разговоров в доступном контексте не сохранилось. Не выдумывать; извлечь из исходного кода при отдельной инвентаризации.

## Текущее состояние
Это начальный технический паспорт на основе ранее просмотренных файлов. Текущий HEAD, последняя версия, точные файлы, последние actions и игровые функции ещё требуют проверки.
