# 001 — Ошибка подключения к серверу с Tectonic

- **Дата:** 2026-11-07
- **Проект:** SkyLimitless
- **Линия:** Minecraft 1.20.1 Fabric
- **Проблемная версия:** SkyLimitless 0.3.3
- **Tectonic:** 3.0.17
- **Статус:** симптомы подтверждены сообщением пользователя/друга; точная причина на момент записи — сильная гипотеза, подтверждённая анализом исходника, но runtime-проверка после фикса ещё не проведена.

## Контекст

Пользователь сообщил, что он и друг не могли подключиться к dedicated server. Обычный мир запускается/загружается, сервер тоже стартует без ошибок. При подключении игрок почти сразу отключается. По результатам собственного сравнения пользователя проблема проявлялась при совместном использовании Tectonic и SkyLimitless; удаление Copycats не помогло.

## Точная конфигурация из сообщения друга

- Minecraft 1.20.1
- Fabric
- Tectonic 3.0.17
- SkyLimitless 0.3.3
- Dedicated server
- offline-mode
- Сервер запускался нормально.
- Игрок логинился и затем отключался при обработке/отправке сетевых данных.

## Основные строки ошибки

```text
Error receiving packet 40
java.util.NoSuchElementException: No value present
at java.util.Optional.get(Optional.java:143)
at net.minecraft.class_6895.method_40350(class_6895.java:35)
at com.mojang.serialization.Encoder$1.encode(Encoder.java:25)
at com.mojang.serialization.Codec$2.encode(Codec.java:76)
at com.mojang.serialization.codecs.KeyDispatchCodec.encode(KeyDispatchCodec.java:98)
at com.mojang.serialization.MapCodec$MapCodecCodec.encode(MapCodec.java:96)
at net.minecraft.class_2540.method_29172(class_2540.java:147)
...
Internal Exception: io.netty.handler.codec.EncoderException:
java.util.NoSuchElementException: No value present
```

## Первичная интерпретация

Ошибка возникает во время кодирования сетевого пакета/данных, а не на этапе запуска сервера. Упоминание реестров/кодеков и `Optional.get()` указывает на попытку получить значение из пустого Optional при сериализации.

Ключевое подозрение: изменённый `DimensionType` невозможно корректно сопоставить с зарегистрированным ключом при сериализации реестра. Нужно было проверить, не заменяет ли SkyLimitless объект после регистрации.

## Исследование исходника

Была проверена ветка `minecraft-1.20.1-fabric` в Mortylovelly/SkyLimitless:
- `RegistryLoaderMixin` изменяет Overworld `DimensionType` при загрузке реестра до регистрации — через аргументы `MutableRegistry.add(...)`.
- `SkyLimitlessHeightLockMixin` позднее перехватывал `MinecraftServer.createWorlds` и вызывал `RegistryEntry.Reference.setValue(adjusted)`, заменяя уже зарегистрированный объект.
- Обратное соответствие значение → ключ могло при такой замене остаться связанным со старым объектом. Это согласуется со стеком ошибки, но само по себе не доказывает, что Tectonic — единственный триггер.

## Связь с Tectonic

Tectonic меняет генерацию рельефа и в некоторых конфигурациях может затрагивать высотные границы. SkyLimitless изменяет Overworld DimensionType. Когда два мода изменяют совместимые/связанные параметры измерения и registry data, появляется риск порядка применения изменений и сетевой сериализации.

Важно различать:
- параметры формы гор, например vertical scale/elevation boost;
- физические границы измерения minY/height;
- верхние ограничения NoiseSettings генератора.

Конфигурационные значения Tectonic пользователя в разговоре ещё не были предоставлены, поэтому точное пересечение значений `min_y/max_y` не установлено.

## Статус
Проблемный код был исправлен следующими изменениями, описанными в записи 002. Установка и подключение 0.3.4 с Tectonic на реальном сервере должны подтвердить или опровергнуть диагноз.
