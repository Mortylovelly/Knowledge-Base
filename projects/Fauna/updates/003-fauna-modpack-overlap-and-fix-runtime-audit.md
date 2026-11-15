# 003 — Аудит fauna-модов и реальной работы Fix

Дата аудита: 2026-11-15. Цель: сверить сохранённый 292-модовый лог, код активной `main` репозитория Fix и логи фактического спавна.

## 1. Состав модпака

В логе 2026-11-08 (Minecraft 1.20.1, Fabric Loader 0.19.5, 292 мода) подтверждены следующие моды с животными/мобами:

| Mod ID | Название | Версия в логе |
|---|---|---:|
| `alexsmobs` | Alex's Mobs Continued | 2.2.2 |
| `naturalist` | Naturalist | 5.0pre5 |
| `wildlife` | Wildlife | 1.19.2-2.2 |
| `crittersandcompanions` | Critters and Companions | 2.7.1 |
| `faunify` | Faunify | 1.3.3 |
| `friendsandfoes` | Friends&Foes | 3.0.9 |
| `ecologics` | Ecologics | 2.2.7 |
| `hybrid_birds` | Hybrid Birds | 1.3.0 |
| `yungscavebiomes` | YUNG's Cave Biomes | 2.0.5 |

`naturalistdelight 1.0.2-1.20.1` — аддон Naturalist, не отдельный сборник мобов. `cavebiomesdelight` в этом запуске отсутствовал (также подтверждено диагностикой Fix). `burnt` и `woodlands_vanilla` не считать самостоятельными mob-паками.

Все девять основных модов перечислены в загрузившемся 1.20.1 Fabric логе; это подтверждает загрузку в одной сборке, но не доказывает отсутствие конфликтов при фактическом спавне.

## 2. Пересечения мобов

Основные пересекающиеся группы, на которые и были направлены правила Fix:

- Alex's Mobs / Naturalist: медведь, сом, слон, носорог, гремучая змея; в коде Fix отсутствует отдельное правило для аллигатора, хотя у этих модов есть близкое пересечение по этой группе.
- Critters and Companions / Faunify: стрекоза, божья коровка, жук/жук-олень, листовое насекомое, мокрица, палочник, долгоносик.
- Critters and Companions / Naturalist: стрекоза, улитка.
- Naturalist / Hybrid Birds: утка.
- Friends&Foes / Ecologics: крабы.
- Naturalist / Wildlife: несколько птиц и наземных/водных видов; правила должны сверяться именно с установленным Naturalist 5.0pre5, а не переноситься с других версий.
- Critters and Companions / Wildlife: хорёк.
- Faunify / Wildlife: ёж и опоссум.

## 3. Что реально делает текущий код Fix

Исходники в активной main:

- `FixMod.java`: регистрирует built-in resource pack, запускает attribute fix если загружен Faunify, регистрирует fauna spawn deduplication и пишет список загруженных mod IDs.
- `FaunifyAttributeFix.java`: проходит по 22 заданным ID Faunify; если `DefaultAttributeRegistry.hasDefinitionFor(type)` возвращает false, пытается зарегистрировать `MobEntity.createMobAttributes()`.
- `FaunaDeduplicator.java`: регистрирует `BiomeModifications` в фазе `REMOVALS` для всех биомов. Удаляет только естественные spawn entries выбранных сущностей. Не удаляет сам EntityType, spawn egg, командный summon, существующих мобов или уже сгенерированные сущности.
- Встроенные loot-table data для YUNG's Cave Biomes задают Ice Cube дроп льда; второй файл Sand Snapper применим только при наличии YUNG's Cave Biomes И Cave Biomes Delight, которого в логируемом наборе нет.

## 4. Обнаруженные дефекты/ограничения fauna-dedup

### Дефект ID Coconut Crab

В Fix записано:
`removeIfPresent(context, "friendsandfoes", "ecologics", "crab")`

Но реальный registry ID Ecologics — `ecologics:coconut_crab`, а не `ecologics:crab`. Следовательно, текущая строка не находит сущность и не удаляет её spawn entry. Источник: исходник Ecologics `ModEntityTypes.java`:
https://github.com/samedifferent/Ecologics/blob/55e2d9efbef28fb1982ac1089c129b560d9b2d86/common/src/main/java/samebutdifferent/ecologics/registry/ModEntityTypes.java

### Общая ошибка проверки preferred entity

`removeIfPresent` проверяет, что оба мода загружены, затем проверяет только существование entity ID у мода, чей спавн удаляется. Код НЕ проверяет, что предпочитаемый мод действительно зарегистрировал соответствующий вид. Это может удалить единственную доступную версию животного, когда preferred-mod присутствует, но counterpart отсутствует/называется иначе.

Особенно тщательно перепроверить Naturalist/Wildlife правила против именно Naturalist 5.0pre5. В коде есть `wildlife:capybara` под правилом предпочтения Naturalist; release notes более поздней ветки Naturalist перечисляют добавление Capybara, поэтому нельзя считать это правило корректным без проверки точного registry установленного pre5 JAR.

### Неполное покрытие

Правило Alex's Mobs / Naturalist не покрывает все известные пересечения (включая аллигатора). Список нельзя считать полным балансировщиком для всех мобов девяти модов.

## 5. Что подтвердили реальные логи

### Лог 2026-11-08 — Fix 0.4.3

В логе сначала идут ошибки:
`Entity faunify:<id> has no attributes` для 22 Faunify сущностей.

Позднее в том же startup:
- `Faunify initialized successfully!`
- `[Fix] Compatibility resource pack registered.`
- `[Fix] Faunify entity attributes are already registered.`
- `[Fix] Fauna duplicate-spawn rules registered.`

Вывод:
- Fix реально запускался.
- Built-in resource pack реально регистрировался.
- Faunify attribute fix не доказал исправление 22 ранних сообщений: он запускается из обычного `ModInitializer` после bootstrap/инициализации сущностей. На момент собственной проверки registry уже отвечал, что атрибуты присутствуют; счётчик добавленных атрибутов был нулевой, поэтому лог и говорит `already registered`.
- Сообщение о регистрации dedup callback доказывает регистрацию правила, но не фактическое удаление какого-либо species entry. Для подтверждения результатов нужны per-entity INFO diagnostics или runtime test.

### Лог 2026-11-11 — реальный runtime сбой Faunify + C2ME

В `latest(1).log` около 22:48:30 многократно записано:
`Failed to create mob`
и
`ThreadLocalRandom accessed from a different thread (owner: Server thread, current: C2ME worker #3)`.

Стек указывает на:
`com.pepper.faunify.entity.BeeflyEntity$BeeflyGoToKnownFlowerGoal.<init>(BeeflyEntity.java:973)`
затем `BeeflyEntity.method_5959` / `BeeflyEntity.<init>`.

Это подтверждает конкретную проблему создания Faunify Beefly при асинхронной генерации/спавне на C2ME worker: Faunify обращается к thread-local random, связанному с серверным потоком. Стек не доказывает, что все Faunify мобы неисправны; подтверждён именно Beefly path. Нынешний Fix этого не исправляет: в нём нет патча threading/random или Beefly AI.

## 6. Итоговая оценка «работал ли Fix»

| Часть | Вывод |
|---|---|
| Mod initialization | Да, подтверждено логом |
| Built-in resource pack registration | Да, подтверждено логом |
| Faunify missing attributes | Нет доказательства исправления; проверка происходит поздно и log сообщает no-op |
| Duplicate natural spawn rules | Callback регистрировался, но реальные удалённые сущности в INFO логах не подтверждены; есть заведомо неправильный ID Coconut Crab и опасные правила без проверки preferred entity |
| YUNG Cave Biomes loot | Ice Cube patch зарегистрирован вместе с pack; Sand Snapper patch условен по Cave Biomes Delight, отсутствующему в запуске |
| Faunify + C2ME thread safety | Не исправляется Fix; runtime ошибка Beefly подтверждена в Nov 11 log |
| CI build | Fix 0.4.10 успешно собрался GitHub Actions run #87; это не равняется runtime verification |

Animal-related Java files `FaunaDeduplicator.java` and `FaunifyAttributeFix.java` не менялись с первичных коммитов 2026-09-19, то есть текущая main продолжает содержать эту реализацию.

## 7. Следующие безопасные исправления

1. Заменить Ecologics target ID `crab` на `coconut_crab`.
2. Переписать каждое правило как pair of exact IDs: перед удалением duplicate spawn проверить наличие и preferred entity type, и duplicate entity type. Добавить INFO-лог с точным результатом удаления.
3. Пересобрать Naturalist/Wildlife пары только по фактическому roster Naturalist 5.0pre5; не брать списки из других версий.
4. Исправить Faunify attribute registration на правильном этапе жизненного цикла после проверки Fabric 1.20.1 API/порядка и доказать это свежим startup log.
5. Отдельно A/B проверить Faunify Beefly spawn с C2ME enabled/disabled. Если ошибка исчезает без C2ME, чинить безопасный random/AI path Faunify или исключить только проблемный spawner/вид, не отключать все fauna mods без теста.

Не менять эти файлы, пока владелец явно не попросит перейти к правкам.
