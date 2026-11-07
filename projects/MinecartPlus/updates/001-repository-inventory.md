# 001 — Первичная инвентаризация по сохранённому контексту

- **Дата:** 2026-11-07
- **Статус:** частичная память; текущий код нужно перепроверить.

## Что достоверно известно
MinecartPlus — Fabric 1.21.1 / Java 21. В проекте использовались Gradle 8.12, Loader 0.19.3, Fabric API 0.116.15+1.21.1, GeckoLib 4.9.2, MixinExtras 0.5.4. Известны Magic Traction I–III с коэффициентами 1.75 / 2.40 / 3.10 и attachments `traction_level` и `tailwind_level`.

## Ограничение
Точное текущее устройство регистрации enchantments, attachments, DataComponent/NBT, рецептов, команд, entity mixins и workflow не восстановлено. Не делать выводов о полном функционале до чтения GitHub.

## Следующий шаг
Прочитать дерево файлов, build.gradle, gradle.properties, fabric.mod.json, все Java-пакеты и последние GitHub Actions. Затем обновить PROJECT.md и создать следующую запись с инвентаризацией.
