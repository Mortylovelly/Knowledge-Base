# MinecartPlus

- **Репозиторий:** [Mortylovelly/MinecartPlus](https://github.com/Mortylovelly/MinecartPlus)
- **Активная целевая линия:** Minecraft 1.20.1 Fabric / Java 17.
- **Подтверждённая ветка:** `minecraft-1.20.1-fabric`. Ветка `main` не проверялась; не выбирать её автоматически.
- **Mod ID:** `minecartmagic`.
- **Версия в активной ветке:** `1.0.3-mc1.20.1`.
- **Последний проверенный HEAD:** `a1d031844a63b86c5748fad9cf1536116062d7b3`.
- **Последний Actions build:** [run 34774665071](https://github.com/Mortylovelly/MinecartPlus/actions/runs/34774665071), success для exact HEAD.
- **Artifact:** `minecartmagic-1.20.1`, ID `10322843551`.
- **Runtime:** подтверждённый игровой/dedicated-server тест этой версии не найден.

## Линия 1.20.1 — среда сборки

| Компонент | Значение |
|---|---|
| Minecraft | 1.20.1 |
| Yarn | 1.20.1+build.10 |
| Fabric Loader | 0.17.2 |
| Fabric API | 0.92.11+1.20.1 |
| Java source/target | 17 |
| Fabric Loom | 1.9.2 |
| Gradle wrapper | 8.12 |
| GeckoLib | geckolib-fabric-1.20.1:4.8.4 |
| mod_version | 1.0.3-mc1.20.1 |

`build.gradle` не подключает MixinExtras. `README.md` в этой ветке всё ещё говорит про 1.21.1 и должен быть исправлен при следующей задаче в кодовом репозитории; фактические `gradle.properties` и `fabric.mod.json` подтверждают 1.20.1.

## Подтверждённые механики

- Регистрируются зачарования `traction` и `tailwind`, максимум III.
- Magic Traction I / II / III увеличивает максимальную скорость вагонетки в 1.75 / 2.40 / 3.10 раза.
- Tailwind применяется к лодке при движении вперёд по воде: множители 1.08 / 1.14 / 1.20, максимальная горизонтальная скорость 0.55 / 0.65 / 0.75.
- Зачарованная вагонетка переносит уровень тяги из предмета в созданную сущность при установке на рельс.
- В 1.20.1 `MinecartMagicAttachments.init()` — no-op. Для уровня тяги/попутного ветра используются DataTracker values и command-tag fallback; не переносить сюда attachment-архитектуру другой версии.
- В дереве есть `AdvancedMinecartEntity` и `SelfPropellingBoatEntity`, а также соответствующие items/screens; полноценное поведение требует runtime-теста.

## Команды и конфигурация

В активном entrypoint/дереве исходников не обнаружена регистрация slash-команд. Отдельные config-классы и config-файлы не обнаружены.

## Ключевые исходники

- `MinecartMagicMod.java` — регистрация attachments compatibility holder, entities, enchantments, items, placement, screens и networking.
- `LegacyEnchantments.java` — регистрация `traction` и `tailwind`.
- `ModEnchantments.java` — чтение уровней, DataTracker и command tags.
- `MinecartMaxSpeedMixin.java` — множители тяги.
- `MinecartTractionDataMixin.java` — синхронизация уровня через DataTracker.
- `BoatTailwindSpeedMixin.java` — ускорение лодок.
- `MinecartPlacementHandler.java` — размещение зачарованной вагонетки на рельсе.
- `minecartmagic.mixins.json` — mixin list.

## GitHub Actions

Workflow `.github/workflows/build.yml` использует JDK 17 и `./gradlew build --no-daemon`, artifact name `minecartmagic-1.20.1`.

Последний проверенный commit:
- SHA: `a1d031844a63b86c5748fad9cf1536116062d7b3`
- Message: `Fix advanced minecart orientation using original main implementation`
- Actions push run [34774665071](https://github.com/Mortylovelly/MinecartPlus/actions/runs/34774665071): `completed / success`, SHA совпадает с HEAD.
- Artifact ID: `10322843551`, присутствует, не истёк на момент аудита.

## Текущее состояние

- Активная рабочая линия для дальнейших задач — `minecraft-1.20.1-fabric`.
- Сборка текущего HEAD проходит.
- README в ветке содержит устаревшее упоминание 1.21.1.
- Runtime-проверка зачарований, синхронизации DataTracker и self-propelling vehicles не подтверждена.
- Старый паспорт и update 001 описывали 1.21.1 по сохранённому контексту; update 002 фиксирует фактическую отдельную линию 1.20.1.

## История

- [001 — Первичная инвентаризация по сохранённому контексту](updates/001-repository-inventory.md)
- [002 — Подтверждена отдельная линия MinecartPlus для 1.20.1](updates/002-current-1.20.1-branch-audit.md)
