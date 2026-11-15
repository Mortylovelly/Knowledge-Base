# Big Globe — генерация мира и датапаки

## Кратко

- **Тип проекта:** datapack / конфигурация генерации мира.
- **Основной генератор:** Big Globe.
- **Цель:** большие биомы, высокие горы и согласованная вертикальная генерация.
- **Общий стандарт:** Minecraft Java 1.20.1 + Fabric + Java 17; точную runtime-связку проверять по логу.
- **Репозиторий datapack:** пока не установлен; реальные ZIP обнаружены в Library и перечислены в update 002.
- **Текущее состояние:** самый новый найденный Clean Overworld архив имеет подтверждённую несогласованность traits с новым min_y=-208 и не содержит bedrock override. Если именно он включён, он объясняет ошибку `bound must be greater than origin` и отсутствие core/mantle. Какой ZIP активен в мире пока не подтверждено.

## История и цели

Владелец хочет крупные биомы и высокие горы, без Overworld Skylands, но с полноценной нижней частью мира — molten-rock mantle/core и бедроком. Высота поверхности, нижняя граница измерения и верхняя build limit — независимые настройки и должны проверяться отдельно.

Требования:
1. Сохранять ранее одобренную горизонтальную компоновку биомов, островов и форму гор.
2. Не путать высоту гор с max_y измерения.
3. Сохранять core/mantle, lava level, deep-dark layer и bedrock при изменении min_y.
4. Сохранять совместимость с Distant Horizons; Vertigo нельзя использовать вместе с DH.
5. Для тестов worldgen использовать новый временный мир; старые чанки не перестраиваются datapack-ом.

## Проверенные архивы

| Архив | Big Globe | Диапазон Overworld | Рельеф | Core/bedrock |
|---|---|---|---|---|
| `BigGlobe_Clean_Overworld_1.20.1.zip` | 5.2.0 | -208 … 1024 (max exclusive); height 1232 | стандартный | **ошибка:** traits core/lava остались ниже нового min_y; bedrock override отсутствует |
| `BigGlobe_Tall_Mountains_x4_No_Skylands_1.20.1_v2.zip` | 5.3.2 | -512 … 2000 (max exclusive); height 2512 | ×4 положительная суша | traits core/deep-dark/lava согласованы; bedrock -512/-496 |
| `BigGlobe_Tall_Mountains_x4_No_Skylands_1.20.1.zip` | 5.3.2 | -512 … 2000 | ×4, custom erosion template | core+bedrock имеются; пользовательский template runtime pending |
| `BigGlobe_Tall_Mountains_1500_No_Skylands_1.20.1.zip` | 5.3.2 | -512 … 2000 | ×2.5, target around Y1500 for prior ~Y600 terrain | core shifts есть; bedrock override отсутствует |
| `BigGlobe_Mountains_1000_No_Skylands_1.20.1.zip` | 5.3.2 | -1024 … 2000 | native Big Globe height scale approx 1000, no multiplier | original core traits; bedrock override отсутствует |

Подробное сравнение, точные traits и анализ ошибки: [002 — аудит архивов](updates/002-datapack-height-core-bedrock-audit.md).

## Диагностика ошибки molten_rocks

Лог содержал `java.lang.IllegalArgumentException: bound must be greater than origin` внутри Big Globe Script. Скрипт вызывает `nextInt(min_y, core_max_y)` и `nextInt(lava_y, core_max_y)`.

В Clean ZIP traits равны `min_y=-208`, `core_max_y=-784`, `lava_y=-896`. Первый вызов становится `nextInt(-208, -784)`, где верхняя граница меньше нижней. Это точное объяснение ошибки **если Clean ZIP активен**. C2ME/Distant Horizons в стеке — контекст вызова, не доказанная первопричина.

Clean ZIP не содержит `configured_feature/overworld/bedrock.json`; generator продолжает ссылаться на встроенный `bigglobe:overworld/bedrock`, то есть pack не переместил bedrock к новому дну.

## Структуры Big Globe

В версии 1.20.1 Big Globe 4.11.0 подтверждены giant obelisks и изменённые mega trees. Общая документация перечисляет Mega Dungeons, Geodes, Mega Trees, underground water/lava pockets, bigger desert pyramid, campfires/tents, small/medium/large houses, log cabins, lakes, surface mineshafts, wells, windmills, abandoned cities, obelisks, Nether Pillars, Soul Campfires, Portal Temples, End Fractals и Endelisk. Сверять конкретные доступные типы с версией мода и данными мира; V6 docs may include later changes.

## Совместимость и ограничения

- Clean archive README требует Big Globe 5.2.0; x4 V2 README требует 5.3.2. Не смешивать JSON-структуры без проверки версии.
- Высота мира из ZIP задаётся как max-exclusive: при max_y=2000 последний допустимый блок Y=1999.
- Моды YUNG's Better* и Philip's Ruins также присутствуют в одном из сохранённых списков модпака; не каждое видимое здание считать структурой Big Globe.
- Не удалять C2ME или DH на основании одного stack trace; сначала исправить/проверить traits.

## Текущее состояние

- **Последняя найденная версия datapack:** Clean Overworld (2026-11-15 12:39 в ZIP), но факт его активности в текущем мире не проверен.
- **Вариант с корректными core+bedrock среди архивов:** `BigGlobe_Tall_Mountains_x4_No_Skylands_1.20.1_v2.zip` (цель Big Globe 5.3.2); runtime test в точном модпаке не выполнялся.
- **Исправление игрового мира:** ещё не сделано.
- **Следующий шаг:** получить из текущего мира `/datapack list` и точную версию Big Globe, затем подготовить корректный архив под эту конкретную конфигурацию.

## История

- [001 — Повторяющаяся ошибка Big Globe: недопустимый диапазон случайной высоты](updates/001-bigglobe-invalid-random-range.md)
- [002 — Аудит сохранённых datapack: высота, мантия и бедрок](updates/002-datapack-height-core-bedrock-audit.md)