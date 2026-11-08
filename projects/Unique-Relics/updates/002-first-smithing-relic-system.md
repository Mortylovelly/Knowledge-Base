# 002 — Первая система ковки Unique Relics без регистрации предметов

Дата: 2026-11-08

## Решение владельца
Отменена первоначальная идея UUID, серийных номеров и глобальной регистрации. Предметы не учитываются в глобальной базе; реликвия — тот же исходный ItemStack с NBT. Отдельные новые типы предметов не создаются.

## Реализация в Mortylovelly/Unique-Relics
Цель: Minecraft 1.20.1 Fabric / Java 17; ветка main.

- Временная ковка через штатный smithing table.
- Обрабатывается только результат smithing-trim recipe, в котором есть настоящий ArmorTrim.
- Сохраняется исходный тип предмета и trim pattern; случайно выбирается trim material.
- Случайный материал кэшируется по сигнатуре входных стеков, чтобы превью не перебрасывало цвет при каждом обновлении.
- NBT unique_relics: template, pattern, material, rarity=rare.
- При взятии output добавляются smith (ник) и forged_at (epoch milliseconds).
- Клиентский tooltip выводит шаблон, цвет/материал, редкость «Редкая», ник кузнеца и дату.
- Шанс ковки пока 100%, если обычный рецепт прошёл; шанс неудачи не задан.

Ключевые файлы: SmithingScreenHandlerMixin.java, ForgingScreenHandlerAccessor.java, UniqueRelicsClient.java, unique_relics.mixins.json, fabric.mod.json, языковые файлы RU/EN и README.

## Сборка
- HEAD 25e0ce5b1b1d82934396077d0b269e59f65bd650.
- [Actions run 42498275619](https://github.com/Mortylovelly/Unique-Relics/actions/runs/42498275619): Build успешно; artifact upload успешно.
- Artifact unique-relics-build, ID 13624283249, SHA-256 5b1c9daebbd80f1c3b9023af9e663e7c244dab7896dbda28e1bb02a123aaabbd.
- Ранние ошибки компиляции по newline и callback signature исправлены до успешной сборки.

## Сторонняя совместимость
Проверен публичный исходник Tool Trims Mod: его smithing trim recipe использует тип minecraft:smithing_trim и тег tooltrims:trimmable_tools. Это подтверждает совпадение уровня рецепта, но не заменяет runtime-тест.

Кандидаты для runtime:
- Tool Trims Mod 1.1.0+mc1.20.1 Fabric/Quilt.
- Elytra Trims 3.9.0 Fabric 1.20.1.

JAR-файлы third-party модов не коммитить в Unique Relics. Для проверки устанавливать их в игровой экземпляр; в мультиплеере Elytra Trims должен быть и на сервере, и на клиенте.

**Игровой runtime-тест не выполнен.** Не утверждать, что ковка или рендеринг сторонних типов уже подтверждены в игре.

## Известные ограничения
- Входы всё ещё соответствуют рецепту smithing trim; произвольное добавление пока не принимается.
- Пока нет процента неудачи.
- Совместимость с инструментами/элитрами не подтверждена runtime.
- Успешная сборка подтверждает только compilation/package.

## Следующий шаг
Проверить в Minecraft 1.20.1 Fabric: ванильную броню, Tool Trims sword/tool, Elytra Trims elytra; проверить preview, взятие результата, NBT persistence после перезахода и то, что наковальня/нетримовые рецепты не изменены.
