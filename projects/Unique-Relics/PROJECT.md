# Unique Relics

## Кратко
- Репозиторий: [Mortylovelly/Unique-Relics](https://github.com/Mortylovelly/Unique-Relics)
- Рабочая ветка: main (единственная ветка, проверена 2026-11-08).
- Minecraft 1.20.1 / Fabric / Java 17.
- Mod ID: unique_relics.
- Java package: com.mortylovelly.uniquerelics.
- Статус: реализована первая игровая функция — ковка Unique Relic на базе обычного результата smithing trim.
- Последний проверенный HEAD: 25e0ce5b1b1d82934396077d0b269e59f65bd650 (2026-11-08).
- Последняя сборка: [GitHub Actions run 42498275619](https://github.com/Mortylovelly/Unique-Relics/actions/runs/42498275619); Build и Upload artifact завершились успешно.
- Артефакт: unique-relics-build, ID 13624283249.
- Игровой runtime-тест ещё НЕ выполнен. Успешная компиляция не доказывает, что Mixin работает при запуске Minecraft или что сторонние trim-моды совместимы.

## Решение владельца: без регистрации предметов
Владелец отказался от первоначальной системы глобальной регистрации:
- не генерировать UUID и последовательные номера;
- не вести глобальную базу, реестр или историю предметов на сервере;
- не создавать отдельный item type для каждой реликвии;
- предмет остаётся исходным предметом (меч остаётся мечом, шлем остаётся шлемом);
- отличия Unique Relic записываются в NBT самого ItemStack.
Это решение заменяет раннюю концепцию из записи 001-project-concept-and-skeleton.md; раннюю запись не переписывать.

## Первая реализация ковки (код в main на HEAD 25e0ce5)
Временная система использует обычный кузнечный стол и существующие smithing-trim рецепты.

### Поведение
- Ванильный или совместимый рецепт сначала формирует нормальный результат с ArmorTrim.
- SmithingScreenHandlerMixin работает только с кузнечным столом, а не с наковальней.
- Если результат содержит настоящий ArmorTrim, мод сохраняет выбранный pattern и случайно выбирает другой зарегистрированный trim material (если есть выбор).
- Цвет/материал кэшируется по сигнатуре входных предметов, чтобы превью не перебрасывало его при каждом обновлении.
- Выход остаётся исходным item ID; свойства и NBT обычного результата ванильного рецепта должны сохраняться.
- В NBT output добавляется compound unique_relics с полями template, pattern, material, rarity=rare.
- Когда игрок забирает результат, добавляются smith (ник игрока) и forged_at (Unix timestamp в миллисекундах).
- Клиентский tooltip отображает название шаблона, название материала/цвета, «Редкая», ник кузнеца и дату/время в локальной временной зоне клиента.
- Шанс успеха пока 100%, если обычный smithing-trim рецепт сработал. Пользователь ещё не задавал процент вероятности неудачи.

### Ограничения
- Входы должны соответствовать существующему smithing-trim рецепту: шаблон, base и допустимый material. Не любое произвольное добавление пока подходит.
- Ванильные armor trims работают без дополнительных trim-модов.
- Для мечей/инструментов/трезубца нужна реализация рецептов и визуализации от соответствующего мода.
- Для элитры нужен Elytra Trims или аналог, использующий smithing trim / ArmorTrim.
- Third-party JAR-файлы не добавлять в репозиторий Unique Relics и не включать как compile-time зависимости. Для проверки моды устанавливать в игровой экземпляр на клиент и сервер (если сервер отдельный).

## Проверенные сторонние проекты-кандидаты
- Tool Trims Mod 1.1.0+mc1.20.1 (Fabric/Quilt): [Modrinth](https://modrinth.com/mod/tool-trims-mod/version/1.1.0%2Bmc1.20.1), [source](https://github.com/ThePotatoArchivist/ToolTrimsMod). Использует minecraft:smithing_trim с тегом tooltrims:trimmable_tools, требует Fabric API. Проверено на уровне публичных исходников/рецепта, не runtime.
- Elytra Trims 3.9.0 for Minecraft 1.20.1 Fabric: [Modrinth](https://modrinth.com/mod/elytra-trims/version/OMQIFNas). Для 1.20.1 требует Fabric Language Kotlin и Fabric API; в описании указаны возможные дополнительные YACL/ModMenu. Для мультиплеера мод нужен на сервере и клиенте, чтобы применять и видеть trim.
- Совместимость этих версий с Unique Relics ещё нужно подтвердить в игре.

## Файлы реализации
- src/main/java/com/mortylovelly/uniquerelics/UniqueRelics.java — main entrypoint.
- src/main/java/com/mortylovelly/uniquerelics/UniqueRelicsClient.java — NBT tooltip.
- src/main/java/com/mortylovelly/uniquerelics/mixin/SmithingScreenHandlerMixin.java — preview randomization + forge metadata.
- src/main/java/com/mortylovelly/uniquerelics/mixin/ForgingScreenHandlerAccessor.java — доступ к input/output инвентарям.
- src/main/resources/unique_relics.mixins.json — регистрация mixins.
- src/main/resources/fabric.mod.json — main/client entrypoints и mixin config.
- src/main/resources/assets/unique_relics/lang/ru_ru.json и en_us.json — tooltip translations.

## Техническое окружение
- Minecraft 1.20.1; Yarn 1.20.1+build.10.
- Fabric Loader 0.19.5; Fabric API 0.92.12+1.20.1.
- Java 17; Gradle Wrapper 8.12; Fabric Loom 1.9.2.
- mod_version в gradle.properties пока 0.1.0 (version bump нужен перед выпуском).
- Workflow build.yml использует Java 17, ./gradlew build --no-daemon, artifact unique-relics-build.

## Результат текущего этапа
- HEAD: 25e0ce5b1b1d82934396077d0b269e59f65bd650.
- Actions run 42498275619: Build успешно; Upload artifact успешно.
- Artifact unique-relics-build, ID 13624283249, размер 16,138 bytes, SHA-256 5b1c9daebbd80f1c3b9023af9e663e7c244dab7896dbda28e1bb02a123aaabbd.
- Ранние CI-ошибки в Java newline и callback signature исправлены до успешного run.
- После проверки Yarn 1.20.1 инъекции расположены в SmithingScreenHandlerMixin, а общие input/output поля берутся через ForgingScreenHandlerAccessor.
- Сборка проверяет compilation/package only, не реальный игровой runtime.

## Следующие шаги
1. Запустить игру на Minecraft 1.20.1 Fabric Java 17 с новой сборкой.
2. Проверить ванильный броневой trim.
3. Проверить Tool Trims Mod 1.1.0+mc1.20.1: меч, инструмент, трезубец.
4. Проверить Elytra Trims 3.9.0: элитра.
5. Проверить превью, взятие результата, сохранение NBT после перезахода, а также отсутствие изменений в наковальне и нетримовых рецептах.
6. После тестирования обсудить вероятность неудачной ковки, если владелец всё ещё хочет шанс.
