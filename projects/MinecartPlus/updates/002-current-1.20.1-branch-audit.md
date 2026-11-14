# 002 — Подтверждена отдельная линия MinecartPlus для 1.20.1

- **Дата:** 2026-11-14
- **Проект:** MinecartPlus
- **Линия Minecraft / ветка:** Minecraft 1.20.1 / Fabric, `minecraft-1.20.1-fabric`
- **Версия мода:** `1.0.3-mc1.20.1`
- **Статус записи:** подтверждено по текущему HEAD, metadata, build files и Actions; runtime не проверен

## Контекст

Старый паспорт описывал MinecartPlus как Fabric 1.21.1 / Java 21. В репозитории теперь явно существует отдельная ветка `minecraft-1.20.1-fabric`. Проверялась только она. `main` не открывалась и не использовалась.

## Исследование

Подтверждённые настройки ветки:

- `gradle.properties`: Minecraft 1.20.1, Yarn `1.20.1+build.10`, Fabric Loader `0.17.2`, Fabric API `0.92.11+1.20.1`, `mod_version=1.0.3-mc1.20.1`.
- `build.gradle`: Fabric Loom 1.9.2, Java source/target/release 17, GeckoLib Fabric 1.20.1 `4.8.4`.
- Gradle wrapper: 8.12.
- `fabric.mod.json`: Mod ID `minecartmagic`, Minecraft `~1.20.1`, Java `>=17`, GeckoLib `>=4.8.4`.
- Workflow использует JDK 17 и `./gradlew build --no-daemon`.

README в этой ветке всё ещё называет Minecraft 1.21.1; это устаревшее описание и оно противоречит фактическим build files и metadata.

## Подтверждённая реализация

- Зарегистрированы зачарования `traction` и `tailwind`, максимальный уровень — III.
- Magic Traction I/II/III умножает максимальную скорость вагонетки на 1.75 / 2.40 / 3.10.
- Tailwind ускоряет лодку при движении вперёд по воде; коэффициенты 1.08 / 1.14 / 1.20, предел скорости 0.55 / 0.65 / 0.75.
- В 1.20.1 `MinecartMagicAttachments.init()` намеренно ничего не регистрирует. Уровни синхронизируются через DataTracker; command tags используются как fallback/persistence.
- При размещении зачарованной вагонетки на рельсе уровень тяги переносится на созданную сущность.
- В дереве присутствуют самоходные лодки/вагонетки и их экраны; их полный runtime-путь отдельно не тестировался.

## Команды и конфигурация

В активном Java entrypoint и дереве исходников не найдено регистрации slash-команд. Отдельного конфигурационного класса/файла в этой ветке не обнаружено.

## Коммиты и сборки

- **HEAD:** `a1d031844a63b86c5748fad9cf1536116062d7b3`
- **Commit:** `Fix advanced minecart orientation using original main implementation` (2026-09-13).
- **Actions push run:** [34774665071](https://github.com/Mortylovelly/MinecartPlus/actions/runs/34774665071), conclusion `success`, точный SHA совпадает с HEAD.
- **Artifact:** `minecartmagic-1.20.1`, ID `10322843551`, присутствует и не истёк на момент проверки.

## Проверка в игре/на сервере

Runtime-тест этой версии в Minecraft 1.20.1 не подтверждён доступной документацией. Успешный Actions build подтверждает только сборку и загрузку artifact.

## Риски и ограничения

- Не путать 1.20.1 DataTracker-порт с историческим контекстом attachments для другой линии.
- README всё ещё указывает 1.21.1.
- `main` не проверялась; не считать её рабочей 1.20.1 веткой.

## Следующий шаг

Проверить JAR 1.0.3-mc1.20.1 в клиенте и на dedicated server: зачарования, перенос уровня при установке вагонетки, синхронизацию DataTracker, Tailwind и самоходные транспортные средства.
