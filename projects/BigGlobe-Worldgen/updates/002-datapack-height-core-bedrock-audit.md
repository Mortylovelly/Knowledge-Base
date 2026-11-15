# 002 — Аудит сохранённых Big Globe datapack: высота, мантия и бедрок

- **Дата:** 2026-11-15.
- **Проект:** Big Globe — генерация мира и datapacks.
- **Версия / ветка Minecraft:** архивы предназначены для Minecraft 1.20.1 Fabric.
- **Целевые версии Big Globe в архивах:** Clean Overworld — 5.2.0; предыдущие Mountain/Tall Mountains — 5.3.2.
- **Статус:** подтверждено по содержимому пяти архивов; какой именно архив был активен в мире владельца, без `/datapack list` пока не подтверждено.

## Контекст и исправление прошлой записи

После записи 001 выполнен поиск в Library. Найдены и материализованы пять настоящих архивов datapack, созданных для Big Globe. Поэтому формулировка записи 001 «исходный datapack не найден» теперь устарела: исходные архивы обнаружены в Library, но репозиторий с их исходными файлами и история commits по-прежнему не установлен. Текущая запись дополняет 001, не переписывая историю задним числом.

## Архивы, которые проверены

| Архив | Целевая Big Globe | Границы Overworld | Изменение высоты рельефа | Мантия / бедрок |
|---|---|---|---|---|
| `BigGlobe_Clean_Overworld_1.20.1.zip` | 5.2.0 | min -208, max-exclusive 1024; height 1232; highest block 1023 | высота суши не умножается; сохраняются обычные горы | Ошибка traits: core/lava остались в старых координатах ниже измерения; отдельного bedrock override нет |
| `BigGlobe_Tall_Mountains_x4_No_Skylands_1.20.1_v2.zip` | 5.3.2 | min -512, max-exclusive 2000; height 2512; highest block 1999 | положительная высота суши ×4; подводные высоты не умножаются | core/deep-dark/lava сдвинуты вверх; bedrock override full_y=-512, empty_y=-496 |
| `BigGlobe_Tall_Mountains_x4_No_Skylands_1.20.1.zip` | 5.3.2 | min -512, max-exclusive 2000; height 2512; highest block 1999 | ×4 через собственный `tall_dual_sine_wave_erosion` template | core traits сдвинуты; bedrock override есть (-512/-496); добавляет пользовательский template |
| `BigGlobe_Tall_Mountains_1500_No_Skylands_1.20.1.zip` | 5.3.2 | min -512, max-exclusive 2000; height 2512; highest block 1999 | ×2.5 над уровнем моря; целевая оценка: ранее ~Y600 → около Y1500, не жёсткий максимум | core/deep-dark/lava traits сдвинуты; отдельного bedrock override в архиве нет |
| `BigGlobe_Mountains_1000_No_Skylands_1.20.1.zip` | 5.3.2 | min -1024, max-exclusive 2000; height 3024; highest block 1999 | формула рельефа не умножается; native Big Globe ~1000-block terrain amplitude — это целевой масштаб, не hard cap | исходные core/lava traits; отдельного bedrock override в архиве нет |

Имена файлов и время внутри ZIP показывают, что `BigGlobe_Clean_Overworld_1.20.1.zip` — самый новый найденный архив (2026-11-15 12:39), а x4 V2 — предыдущий вариант (2026-11-14 22:28). Сам факт наличия архива в Library не доказывает, что он включён в текущем мире.

## Подтверждённая ошибка в Clean Overworld

README архива Clean указывает Big Globe 5.2.0, min_y=-208, max_y=1024, total height 1232. Фактический `dimension_type/overworld.json` и generator height в `world_preset/bigglobe.json` согласованы по этим границам.

Но `worldgen/bigglobe_world_trait_impl/overworld.json` сохраняет старые значения:

    bigglobe:core_min_y = -912
    bigglobe:core_max_y = -784
    bigglobe:deep_dark_min_y = -752
    bigglobe:deep_dark_max_y = -624
    bigglobe:lava_y = -896
    bigglobe:min_y = -208
    bigglobe:max_y = 1024

Это внутренне противоречивые высоты: core и deep-dark/lava находятся ниже нового min_y. В частности, скрипт из пользовательского лога вызывает `random.nextInt(min_y, core_max_y)`, то есть фактически `nextInt(-208, -784)`. Для Java `RandomGenerator.nextInt(origin, bound)` верхняя граница должна быть строго больше origin. Здесь bound=-784, origin=-208, поэтому возникает именно `IllegalArgumentException: bound must be greater than origin`.

**Вывод подтверждён по числам в ZIP:** если в проблемном мире включён Clean Overworld, его ошибочные traits полностью объясняют строку `bound must be greater than origin` и исчезновение доступной нижней core/mantle зоны. Это не доказательство сбоя C2ME или Distant Horizons: они лишь видны в стеке вызова генерации.

В том же ZIP нет `data/bigglobe/worldgen/configured_feature/overworld/bedrock.json`. При этом generator продолжает вызывать `bigglobe:overworld/bedrock` в rock_replacers. Следовательно, пак не переопределяет исходную bedrock feature под новую нижнюю границу -208. Это согласуется с жалобой владельца на отсутствие бедрока. Точное поведение встроенного default bedrock placement для установленной сборки следует дополнительно подтвердить игровым тестом.

Clean Overworld также не содержит высотного множителя: max_y=1024 и обычная формула рельефа остаётся без ×2.5/×4. Его цель — убрать Skylands и поверхностные rocks/flint/sticks, а не создавать горы до Y1500.

## Рабочий вариант с мантией и бедроком среди найденных архивов

`BigGlobe_Tall_Mountains_x4_No_Skylands_1.20.1_v2.zip` содержит согласованные нижние traits:

    min_y = -512
    core_min_y = -400
    core_max_y = -272
    lava_y = -384
    deep_dark_min_y = -240
    deep_dark_max_y = -112
    max_y = 2000

Он также содержит core layer (`the_core.json`) с блоками `bigglobe:molten_rock_8` … `molten_rock_1` и bedrock configured feature с `full_y=-512`, `empty_y=-496`. Его высота измерения — 2512 блоков; верхняя граница 2000 является исключающей, поэтому последний размещаемый блок — Y=1999.

V2 восстанавливает встроенный Big Globe `dual_sine_wave_erosion` template и умножает положительную высоту суши на 4; океанические отрицательные высоты не умножаются. README архивa прямо указывает, что JSON/ZIP проверены, но запуск на точной сборке владельца не выполнялся.

Вариант `Tall_Mountains_x4` (без V2) тоже содержит bedrock и core, но добавляет пользовательский script template `tall_dual_sine_wave_erosion`. V2 был создан позже и отказался от этого пользовательского template. Для теста предпочтительнее V2, но только если фактическая версия Big Globe совместима с целью 5.3.2.

## Что Big Globe генерирует сам по себе

В Big Globe 1.20.1 версии 4.11.0 и последующих changelog прямо подтверждает очень редкие гигантские обелиски и изменение размера/частоты mega trees; список функций в документации upstream описывает также дома, log cabins, windmills, abandoned cities, mega dungeons, geodes, lakes, surface mineshafts и другие структуры. Эти постройки не создаются нашими height datapack: их создаёт сам Big Globe, если конкретная версия и feature/structure dispatchers их включают.

Для структуры списка и кратких описаний используйте upstream docs: https://github.com/Builderb0y/BigGlobe/blob/V6/List%20of%20features.md . Версии V6 не следует считать идентичными 1.20.1 во всех деталях; для обелисков/mega trees наличие в линии 1.20.1 подтверждается release 4.11.0: https://modrinth.com/mod/big-globe/version/50LhEBLw .

## Изменения

| Файл | Что изменено |
|---|---|
| `projects/BigGlobe-Worldgen/PROJECT.md` | Добавлены конкретные архивы и точные высоты; исправлено состояние исследования |
| `projects/BigGlobe-Worldgen/updates/README.md` | Добавлена запись 002 |
| `projects/BigGlobe-Worldgen/updates/002-datapack-height-core-bedrock-audit.md` | Сравнение пяти архивов и доказанная причина Clean-пака |

Игровые ZIP, моды и сохранение мира не изменялись.

## Что ещё неизвестно

- Какой архив в данный момент включён в конкретном сохранении; нужен вывод `/datapack list`.
- Какая exact версия Big Globe запущена в том мире; Clean ZIP требует 5.2.0, x4 V2 требует 5.3.2.
- Визуальная высота фактических гор по seed и максимальный размер структур; README содержит проектные цели, а не измерения мира.
- Точное поведение встроенной bedrock feature после смены min_y без её явного override.
- Часть видимых построек может принадлежать другим установленным модам. В сохранённом списке модов есть YUNG's Better* structures и Philip's Ruins, поэтому не каждое здание автоматически Big Globe.

## Следующий шаг

Перед выпуском исправленного ZIP проверить фактический активный datapack и Big Globe version. Не смешивать Clean (5.2.0, -208…1024) и Tall Mountains x4 V2 (5.3.2, -512…2000) — это разные целевые конфигурации. После выбора одного варианта исправить только нужные traits/bedrock/core, сохранить ранее согласованную высоту гор и проверить генерацию в disposable new world.