# Fauna- — Fix: Modpack Compatibility & Balance

## Кратко
- **Репозиторий:** [Mortylovelly/Fauna-](https://github.com/Mortylovelly/Fauna-)
- **Название проекта в сохранённом контексте:** Fix — Modpack Compatibility & Balance.
- **Project root name:** `Fix`.
- **Отображаемое имя мода:** `Fix - Modpack Compatibility & Balance`.
- **Mod ID:** `fix`.
- **Целевая версия:** Minecraft 1.20.1 / Fabric / Java 17.
- **Рабочая ветка, проверенная при последнем осмотре:** `main` (в gradle.properties и fabric.mod.json указана именно линия 1.20.1).

## ВАЖНО: где находится логика биомов
**Исправление распределения биомов находится именно в этом проекте Fauna- (мод Fix), а не в Vertex / seasonaldaycycle.** Если пользователь говорит о нашем фикс-моде, который меняет распределение биомов, начинать поиск нужно с репозитория `Mortylovelly/Fauna-`.

Основные исходники:
- `src/main/java/com/mortylovelly/fix/biome/BiomeDistributionFix.java` — алгоритм изменения распределения биомов.
- `src/main/java/com/mortylovelly/fix/mixin/ChunkSectionBiomeMixin.java` — подключает алгоритм после заполнения биомной палитры.
- `src/main/java/com/mortylovelly/fix/FixMod.java` — вызывает диагностический вывод параметров и регистрирует встроенный compatibility resource pack.
- `src/main/resources/resourcepacks/compat_fixes/data/yungscavebiomes/` — отдельные compatibility loot-table данные для YUNG's Cave Biomes; это не основной алгоритм распределения биомов.

### Как работает биомный фикс в просмотренном исходном коде
- `BiomeDistributionFix.ENABLED = true`.
- `ChunkSectionBiomeMixin` с priority 900 внедряется в `ChunkSection.populateBiomes` на `TAIL` (require=1). Комментарий в исходнике говорит, что pass выполняется после заполнения биомной палитры Noisium, чтобы не конфликтовать с его overwrite этого метода.
- Алгоритм использует исходный `BiomeSupplier` и выбирает детерминированные большие области по sample-центрам с jitter; расчёты ведутся в quart-координатах (1 quart = 4 блока).
- Обычная сетка: шаг 3500 quart = номинально 14 000 блоков; оценочная ширина регионов с jitter — около 8 400–19 600 блоков.
- Независимая сетка гор: шаг 6000 quart = номинально 24 000 блоков; оценочная ширина — около 14 400–33 600 блоков.
- Для гор используется детерминированный шанс сохранения 35%. Если центр горного региона не попадает в горный биом, алгоритм может проверить ближайшие центры обычной сетки.
- Распознавание горных биомов идёт по частям biome path: peak, mountain, alpine, highland, jagged, windswept, slope, meadow и grove.
- Защищённые биомы не заменяются: среди явных категорий — реки, океаны, пещеры, deep dark, Нижний мир и Край. Внутри обычных регионов для защищённых ячеек сохраняется исходная биомная ячейка.
- В исходниках предусмотрены диагностические сообщения `[Fix][BiomeDiag]`, `[Fix][BiomeHook] ACTIVE`, `[Fix][BiomeProbe]` и периодическая статистика `[Fix][BiomeStats]`.

Это описание подтверждает реализацию в коде, но само по себе не доказывает её работу в текущем игровом мире. Для подтверждения runtime нужно проверить лог игры: в частности, сообщение `[Fix][BiomeHook] ACTIVE`, затем статистику и реальные результаты генерации. Не утверждать, что runtime-тест уже пройден, пока он не выполнен.

## Остальная механика
- Точечная совместимость и стабилизация модпака.
- Дедупликация повторяющихся natural-spawn записей мобов без удаления самих сущностей.
- Исправления недостающих атрибутов Faunify при наличии этого мода.
- Встроенный compatibility resource pack включается с типом `ALWAYS_ENABLED`.
- Для YUNG's Cave Biomes присутствуют отдельные compatibility loot-table данные.

## Сохранённые параметры Gradle
Из `gradle.properties`, просмотренного в репозитории:
- `minecraft_version=1.20.1`
- `yarn_mappings=1.20.1+build.10`
- `loader_version=0.19.5`
- `loom_version=1.17.21`
- `mod_version=0.4.1`
- `maven_group=com.mortylovelly.fix`
- `archives_base_name=fix`
- `fabric_api_version=0.92.12+1.20.1`

В `build.gradle`:
- plugin `net.fabricmc.fabric-loom-remap` с версией из `loom_version`;
- `JavaCompile.options.release = 17`;
- source/target compatibility Java 17;
- processResources expand version;
- LICENSE rename в JAR.

**Важно:** последнее просмотренное значение Loom 1.17.21 не следует механически переносить в другие проекты. Wrapper в корне при последнем просмотре не обнаружен. Перед сборкой нужно заново проверить актуальные Gradle wrapper / workflow / совместимость Gradle и Loom.

## Workflow
В ранее просмотренном `.github/workflows/build.yml`:
- checkout@v4;
- setup-java@v4 (в workflow был указан Java 21, несмотря на target 17 в build.gradle — потенциальное несоответствие);
- gradle/actions/setup-gradle@v4;
- Gradle в workflow указывался как 9.5;
- запуск `gradle build --no-daemon`;
- upload artifact.

Перед следующей сборкой проверить актуальный workflow и согласованность Java/Gradle/Loom. Также заметить: README на текущем просмотренном main описывает Fix для 1.20.1, но пишет версию 0.2.0, тогда как gradle.properties указывает 0.4.1; считать Gradle-файл источником версии сборки до отдельной сверки.

## Правила работы
- Работать только с Minecraft 1.20.1 Fabric / Java 17, пока пользователь явно не разрешит миграцию.
- Перед изменениями читать текущие полные исходники, проверять HEAD, workflow и Actions.
- Не смешивать проект Fix из `Fauna-` с проектом Vertex из `seasonaldaycycle`.
- Не менять Tectonic, SkyLimitless world height, Better Caves или конфигурации C2ME/VMP/ServerCore без прямого запроса.
- Разделять подтверждённые исходниками факты, гипотезы и runtime-проверки.

## Текущее состояние
Это технический паспорт по последнему осмотру ветки `main` репозитория Fauna-. Биомная реализация обнаружена и описана выше. Последняя сборка, свежий HEAD и игровая runtime-проверка в рамках этого уточнения не проверялись.
