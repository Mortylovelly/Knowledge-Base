# 002 — Исправление поздней подмены DimensionType в реестре

- **Дата:** 2026-11-07
- **Проект:** SkyLimitless
- **Ветка:** `minecraft-1.20.1-fabric`
- **Исходная версия:** 0.3.3
- **Статус:** изменение внесено; GitHub Actions для кода без version bump прошёл успешно; реальный серверный тест не выполнен.

## Что было неправильно

В старом `SkyLimitlessHeightLockMixin` после загрузки реестров находился Overworld `DimensionType`, создавался новый скорректированный объект, а затем новый объект помещался внутрь `RegistryEntry.Reference` через `RegistryEntryReferenceAccessor.setValue`.

То есть менялся сам объект, на который указывала уже зарегистрированная запись. Реестр мог продолжить хранить обратную связь с первоначальным объектом. При поздней сетевой сериализации значение могло не находить соответствующий registry key.

## Что сделано

1. В `SkyLimitlessHeightLockMixin.java` удалена подмена значения внутри `RegistryEntry.Reference`.
2. Добавлен `DimensionTypeAccessor.java` с Mixin accessors на поля:
   - `minY`
   - `height`
   - `logicalHeight`
3. Существующий `DimensionType` остаётся тем же объектом; меняются только его высотные поля.
4. `skylimitless.mixins.json` обновлён: зарегистрирован `DimensionTypeAccessor`, удалён `RegistryEntryReferenceAccessor`.
5. Файл `RegistryEntryReferenceAccessor.java` удалён, поскольку больше не нужен.

## Изменённые файлы

| Файл | Изменение |
|---|---|
| `src/main/java/com/mortylovelly/skylimitless/mixin/SkyLimitlessHeightLockMixin.java` | Изменение полей исходного DimensionType вместо замены значения в registry reference |
| `src/main/java/com/mortylovelly/skylimitless/mixin/DimensionTypeAccessor.java` | Новый accessor для minY, height, logicalHeight |
| `src/main/resources/skylimitless.mixins.json` | Регистрация нового accessor |
| `src/main/java/com/mortylovelly/skylimitless/mixin/RegistryEntryReferenceAccessor.java` | Удалён |

## Коммитная последовательность и сборки

Во время внесения был ряд промежуточных коммитов:
- `4ecf8ec731bd28a3cba3ff7d16a0306e77997c14` — первая попытка, Actions завершился неуспешно.
- `11f4abe1f4d95be09093300dcbb28fff0c116278` — промежуточный конфиг, Actions завершился неуспешно.
- `67be801ff741594f8d20a9a39406fea1a95a2d43` — accessor fields добавлен, Actions success.
- `4010153bde4e94ddbea18fea77d41e64049fb2b4` — удалён небезопасный старый accessor, Actions success.

Успешный прогон последнего кода на тот момент:
[GitHub Actions run 42385442891](https://github.com/Mortylovelly/SkyLimitless/actions/runs/42385442891) — conclusion success, commit `4010153bde4e94ddbea18fea77d41e64049fb2b4`.

Ранние неуспешные прогоны были промежуточными, не являются итогом исправления.
