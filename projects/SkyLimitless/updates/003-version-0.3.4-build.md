# 003 — SkyLimitless 0.3.4: версия исправления и успешная сборка

- **Дата:** 2026-11-07
- **Проект:** SkyLimitless
- **Ветка:** `minecraft-1.20.1-fabric`
- **Версия:** 0.3.4
- **Статус:** сборка подтверждена; dedicated-server runtime тест с Tectonic ещё ожидается.

## Изменение

После исправления registry reference версия повышена с 0.3.3 до 0.3.4, чтобы JAR исправления можно было отличить от версии, вызвавшей проблему.

Изменён файл:
- `gradle.properties`: `mod_version=0.3.4`.

## Commit и сборка

- **Commit SHA:** `740cd272387925fad004a09d9b5fad9fb29390af`.
- **Commit message:** `Release SkyLimitless 0.3.4 compatibility fix`.
- **GitHub Actions:** [run 42385539366](https://github.com/Mortylovelly/SkyLimitless/actions/runs/42385539366).
- **Результат:** `completed / success`.
- **Workflow:** Build SkyLimitless 1.20.1 Fabric.
- **Среда:** Java 17, Gradle 8.12.
- **Artifact:** `SkyLimitless-1.20.1-740cd272387925fad004a09d9b5fad9fb29390af`, архив artifact в Actions.

## Что это подтверждает

Подтверждено, что версия 0.3.4 компилируется и workflow создаёт JAR artifact.

## Что это НЕ подтверждает

Не подтверждено, что:
- сервер с Tectonic 3.0.17 принимает игроков;
- настройки Tectonic `min_y/max_y` полностью согласованы с DimensionType;
- уже существующие миры и чанки проверены на верхних высотах;
- любая другая сборка модов совместима.

## Следующий шаг

Установить JAR 0.3.4 на сервер/клиенты, оставить Tectonic 3.0.17 и исходный мир, проверить подключение двух игроков. Сохранить полные логи и оба конфигурационных файла.
