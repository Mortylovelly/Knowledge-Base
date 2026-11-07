# SkyLimitless

## Кратко

- **Репозиторий:** [Mortylovelly/SkyLimitless](https://github.com/Mortylovelly/SkyLimitless)
- **Mod ID:** `skylimitless`
- **Назначение:** повышает верхнюю границу строительства/измерения Overworld, сохраняя нижнюю границу `Y=-64`.
- **В проекте существуют отдельные линии:** Fabric 1.20.1 и Fabric 1.21.1.
- **Владелец:** Mortylovelly.
- **Текущее внимание:** линия Minecraft 1.20.1 Fabric, ветка `minecraft-1.20.1-fabric`.
- **Последняя документированная версия 1.20.1:** 0.3.4.
- **Последний подтверждённый build:** commit `740cd272387925fad004a09d9b5fad9fb29390af`, GitHub Actions run [42385539366 — success](https://github.com/Mortylovelly/SkyLimitless/actions/runs/42385539366).
- **Проверка реального dedicated server с Tectonic после фикса:** пока не зафиксирована.

## Линии версий — не смешивать

| Линия | Ветка | Minecraft | Java | Особенности |
|---|---|---|---|---|
| Fabric 1.20.1 | `minecraft-1.20.1-fabric` | 1.20.1 | 17 | Версия 0.3.4; здесь исправляли ошибку входа на сервер с Tectonic |
| Fabric 1.21.1 | `main` | 1.21.1 | 21 | Отдельная линия кода; не переносить в неё патч 1.20.1 автоматически |

## Что делает мод

- Меняет высоту Overworld, сохраняя vanilla min Y `-64`.
- Загружает настройку высоты из `config/skylimitless.properties`.
- Высота применяется при создании/загрузке измерения; после изменения значения через команду требуется перезапуск мира/сервера.
- Верхняя граница округляется вверх до границы секции Minecraft в 16 блоков.
- Самостоятельно не переписывает seed, генератор ландшафта, климат/биомы или шумовые функции.
- В текущем 1.20.1 коде `MountainTerrainScaler.scale()` намеренно отключён (`return;`): отдельное растягивание гор через этот класс сейчас не работает и не должно ошибочно считаться частью Tectonic-интеграции.
- Мод предоставляет пространство над обычной генерацией, но сам по себе не превращает горы Tectonic в более высокие. Для формы гор предназначены настройки Tectonic.

## Команды (1.20.1)

Команды требуют permission level 2 (оператор).

| Команда | Что делает |
|---|---|
| `/skylimitless status` | Показывает запрошенную/эффективную высоту |
| `/skylimitless height <top_y>` | Сохраняет новую запрошенную верхнюю границу |

Пример:

```text
/skylimitless status
/skylimitless height 800
```

После `/skylimitless height <top_y>` нужен полный перезапуск сервера/мира. Мод специально не пытается перестраивать высоту уже работающего мира на лету.

## Конфигурация (ветка 1.20.1, версия 0.3.3 и зафиксированный патч 0.3.4)

Путь: `config/skylimitless.properties`.

Поля:
- `requested_top_y` — запрошенная верхняя граница.
- `authorized_top_y` — значение, записанное командой и используемое как разрешённое.
- Высота округляется вверх до 16-блочной секции.
- Нижняя граница: `-64`.
- В коде 1.20.1: `DEFAULT_REQUESTED_TOP_Y = 2000`.
- В коде 1.20.1: `MIN_REQUESTED_TOP_Y = 320`.
- В коде 1.20.1: `MAX_REQUESTED_TOP_Y = 2032`.
- При max 2032 верхняя размещаемая координата блока — Y=2031.
- Команда не позволяет понижать уже сохранённое значение.

**Внимание: несогласованность документации.** В README ветки 1.20.1 ранее было написано, что default requested top Y равен 500, тогда как фактический `SkyLimitlessConfig.java` у версии 0.3.3 содержит `DEFAULT_REQUESTED_TOP_Y = 2000`. Код следует считать актуальным источником истины до отдельного исправления README.

**Историческая заметка:** в более раннем планировании SkyLimitless упоминались значения default Y=5000 и max Y=10000. Нельзя считать их настройками текущей версии 1.20.1: актуальный просмотренный исходник 0.3.3 ограничивает requested top Y значением 2032. Нужно проверить, к какой версии/линии относились старые значения.

## Среда сборки (Fabric 1.20.1)

Последние просмотренные настройки:
- Minecraft: `1.20.1`
- Yarn: `1.20.1+build.10`
- Fabric Loader: `0.15.11`
- Fabric API: `0.92.11+1.20.1`
- Fabric Loom: `1.9.2`
- Gradle в GitHub Actions: `8.12`
- Java: `17`
- Архив: `skylimitless`
- Maven group: `com.mortylovelly.skylimitless`

Workflow: `.github/workflows/build.yml`, запуск для push/pull request в `minecraft-1.20.1-fabric`. Сборка: `gradle build --no-daemon` (workflow поднимает Gradle 8.12; wrapper на конкретной ветке нужно проверять отдельно).

## Важные Java-файлы и архитектура (1.20.1)

| Путь | Назначение |
|---|---|
| `src/main/java/com/mortylovelly/skylimitless/SkyLimitless.java` | Инициализация, конфиг, команды, логирование |
| `SkyLimitlessConfig.java` | Параметры высоты, чтение/сохранение properties, создание скорректированного DimensionType |
| `SkyLimitlessCommand.java` | Команды `/skylimitless status` и `/skylimitless height` |
| `MountainTerrainScaler.java` | Старый/неактивный эксперимент с растягиванием гор; scale сейчас отключён |
| `mixin/RegistryLoaderMixin.java` | Корректировка Overworld DimensionType при загрузке реестра |
| `mixin/SkyLimitlessHeightLockMixin.java` | Фиксация высоты при создании миров |
| `mixin/DimensionTypeAccessor.java` | Доступ к полям minY/height/logicalHeight существующего DimensionType |
| `src/main/resources/skylimitless.mixins.json` | Список миксинов |
| `src/main/resources/fabric.mod.json` | Metadata и зависимости |

## Совместимость с Tectonic

Известная проблемная комбинация:
- Minecraft 1.20.1 Fabric
- Tectonic 3.0.17
- SkyLimitless 0.3.3
- Dedicated server, offline-mode
- Сервер стартовал, но игрок отключался почти сразу после входа.
- В сообщении: `Error receiving packet 40`, `EncoderException`, `NoSuchElementException: No value present`, вызов `Optional.get()`, стек проходил через сетевое кодирование реестров.

Предполагаемая связь: оба мода могут влиять на параметры высоты Overworld/DimensionType. Ошибка SkyLimitless в старом коде заключалась в поздней подмене значения уже зарегистрированного объекта через `RegistryEntry.Reference.setValue`. Это могло нарушить соответствие значения обратному поиску реестра, нужному при сериализации сетевых данных.

**Статус причины:** очень сильная, основанная на найденном участке кода и стеке, но проверка реального входа после фикса всё ещё необходима. Нельзя обещать, что все возможные конфликты Tectonic устранены.

## Что изменилось в 0.3.4

В ветке `minecraft-1.20.1-fabric`:
- удалена поздняя замена объекта в `RegistryEntry.Reference`;
- добавлен `DimensionTypeAccessor`;
- поля `minY`, `height`, `logicalHeight` меняются у существующего зарегистрированного объекта;
- `DimensionType` не заменяется новым объектом в registry reference;
- удалён более не нужный `RegistryEntryReferenceAccessor`;
- версия повышена до 0.3.4.

Этот фикс не меняет шумовые функции/форму гор Tectonic и не реализует согласование `NoiseSettings` Tectonic. При конфигурации Tectonic с отдельным `min_y/max_y` нужно проверить, совпадают ли высотные границы генерации и DimensionType.

## Установка и тестирование 0.3.4

1. Сделать резервную копию мира и server config.
2. Заменить старый `skylimitless-0.3.3.jar` на `skylimitless-0.3.4.jar` на сервере.
3. Если мод установлен у игроков, использовать одну и ту же версию там, где она требуется.
4. Не удалять мир и не пересоздавать его на первом тесте.
5. Оставить Tectonic 3.0.17 и прежние настройки модпака.
6. Проверить вход владельца и второго игрока на выделенный сервер.
7. Если ошибка повторится — сохранить полный свежий серверный и клиентский лог, точные configs Tectonic/SkyLimitless и время отключения.

Артефакт сборки 0.3.4: [GitHub Actions run 42385539366](https://github.com/Mortylovelly/SkyLimitless/actions/runs/42385539366). ZIP artifact называется `SkyLimitless-1.20.1-740cd272387925fad004a09d9b5fad9fb29390af`.

## Текущее состояние

- **Целевая рабочая линия:** 1.20.1 Fabric, `minecraft-1.20.1-fabric`.
- **Версия в документированной ветке:** 0.3.4.
- **Последний зафиксированный SHA ветки:** `740cd272387925fad004a09d9b5fad9fb29390af`.
- **Последняя сборка:** success, Actions run 42385539366.
- **Серверная проверка с Tectonic 3.0.17:** ожидается от владельца/друга.
- **Открытая задача:** проверить реальные значения `config/tectonic.json` и `config/skylimitless.properties`; убедиться в согласовании высоты DimensionType и NoiseSettings; после подтверждения входа внести результат.
- **Ещё одна открытая задача:** привести README к фактическому default top Y=2000 либо принять отдельное решение поменять default в коде.

## История обновлений

- [001 — Ошибка подключения с Tectonic 3.0.17](updates/001-tectonic-login-packet-error.md)
- [002 — Исправление поздней замены DimensionType в реестре](updates/002-registry-reference-fix.md)
- [003 — Версия 0.3.4 и успешная сборка](updates/003-version-0.3.4-build.md)
