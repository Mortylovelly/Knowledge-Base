# 004 — актуальный состав модпака и исправления генерации руд Big Globe

- **Дата:** 19 ноября 2026 года
- **Проект:** Fauna- / Fix — Modpack Compatibility & Balance
- **Линия Minecraft / ветка:** Minecraft 1.20.1 / Fabric / `main`
- **Версия мода:** Fix 0.4.12
- **Статус записи:** код исправлений собран успешно; проверка генерации в игре ещё не выполнена

## Контекст

Владелец передал актуальный список файлов сборки из 95 позиций и попросил сохранить его как основной ориентир для дальнейшей работы. Одновременно исправлялась генерация руд для сочетания Big Globe 5.3.2, Create 6.0.8.1, Create Nuclear 1.4.1 и BetterNether 9.0.13.

**Важно о двух списках:** старый лог от 8 ноября 2026 года содержал 292 загруженных мода и использовался в предыдущем аудите fauna-модов (запись 003). Новый список из 95 позиций передан владельцем 19 ноября и является текущим списком файлов модпака. Не объединять эти два перечня и не считать их разными подсчётами одной и той же установки без дополнительного подтверждения.

## Правило актуальности состава

- Список ниже считается актуальным составом сборки на **19 ноября 2026 года** и остаётся основным ориентиром, пока владелец сам не сообщит об изменениях.
- Владелец может добавлять или удалять отдельные моды. Это не означает автоматическую замену всей сборки или смену Minecraft/loader.
- Обновлять список и дату только после сообщения владельца об изменениях состава.
- Сохранять повторяющиеся строки и исходный порядок: список переписан без автоматической дедупликации.
- Многоточия в названиях оставлены как в переданном списке: недостающие части имени не угадывать.
- Позиция `Fix` передана без номера версии. В исходном репозитории код теперь имеет версию 0.4.12, но точная версия установленного пользователем JAR на момент передачи списка отдельно не подтверждалась.

## Полный список модов — 95 позиций

1. alexsmobs-2.3.0+fabric+1.20.1
2. alternate-current-mc1.20-1.9.0 (1)
3. animalgarden_owl-1.1.1-fabric-1.20.1-0.9...
4. architectury-9.2.14-fabric (1)
5. architectury-9.2.14-fabric
6. BadOptimizations-2.4.1-1.20.1
7. bclib-3.0.18
8. BCLib-20.0.16-fabric
9. bellsandwhistles-0.4.5+1.20.1-FABRIC
10. better-nether-9.0.13
11. Big Globe-5.3.2-MC1.20.1
12. brewinandchewin-3.0.6+1.20.1
13. c2me-fabric-mc1.20.1-0.2.0+alpha.11.18...
14. Chunky-1.3.146 (2)
15. cloth-config-11.1.136-fabric
16. codxlib-1.6.1-fabric-1.20.1 (1)
17. copycats-3.0.8+mc.1.20.1-fabric
18. cosycritters-0.1.0+1.20.1-fabric
19. create_trimmed
20. createcontraptionterminals-fabric-1.20-1....
21. createdeco-2.1.1-1.20.1-fabric
22. create-fabric-6.0.8.1+build.1744-mc1.20.1
23. createframed-1.7.3+1.20.1
24. create-man-of-many-planes-1.0
25. createnuclear-1.4.1-fabric
26. CreateNumismatics-1.1.0+fabric-mc1.20.1
27. createthreadedtrains-fabric-1.20.1-1.0.0
28. crittersandcompanions-fabric-1.20.1-2.7....
29. cultural-delights-fabric-1.0.5+1.20.1
30. DistantHorizons-3.2.0-b-1.20.1-fabric-for...
31. ends_delight-refabricated-1.20.1-2.5
32. entityculling-fabric-1.12.1-mc1.20.1
33. Explorify v1.6.6.mod
34. fabric-api-0.92.12%2B1.20.1
35. fabric-api-0.92.12+1.20.1
36. fabric-language-kotlin-1.14.2+kotlin.2.4....
37. fabric-language-kotlin-1.14.2+kotlin.2.4.21
38. FarmersDelight-1.20.1-2.5.7+refabricated
39. FarmersRespite-Fabric-3.0.2
40. ferritecore-6.0.1-fabric
41. Fix
42. fzy_config-0.7.7+1.20.1
43. geckolib-fabric-1.20.1-4.8.4 (1)
44. hapi-fabric-1.20.1-1.1.3
45. hybrid_aquatic-fabric-1.20.1-1.7.4
46. hybrid_birds-fabric-1.20.1-1.3.0 (1)
47. immersive_aircraft-1.5.0+1.20.1-fabric
48. indium-1.0.36+mc1.20.1
49. interiors-1.20.1-fabric-0.6.0
50. iris-1.7.6+mc1.20.1 (1)
51. krypton-0.2.3 (1)
52. lithium-fabric-mc1.20.1-0.11.4
53. man_of_many_planes-0.2.1+1.20.1-fabric
54. memoryleakfix-fabric-1.17+-1.1.5 (1)
55. minecraft-ai-agent-0.3.18
56. modernfix-fabric-5.25.2+mc1.20.1
57. moreculling-1.20.1-0.24.6 (1)
58. moredelight-26.05.07-1.20-fabric
59. naturalist-2.0.9-fabric-1.20.1
60. NethersDelight-Refabricated-1.20.1-4.1.1
61. noisium-fabric-2.3.0+mc1.20-1.20.1
62. obe-1.20.1-1.2.8
63. oceansdelight-fdrf-fabric-1.0.2-1.20
64. particle_core-0.3.3+1.20.1
65. particular-1.20.1-Fabric-1.6.1
66. Patchouli-1.20.1-85-FABRIC (1)
67. powergrid-mc1.20.1-0.5.4.1
68. resourcefulconfig-fabric-1.20.1-2.1.3
69. resourcefullib-fabric-1.20.1-2.1.29
70. seasonaldaycycle-1.1.0-mc1.20.1
71. servercore-fabric-1.5.2+1.20.1 (1)
72. sodium-extra-0.5.9+mc1.20.1
73. sodium-fabric-0.5.12-beta.2+mc1.20.1
74. sodium-fabric-0.5.13+mc1.20.1
75. sound-physics-remastered-fabric-1.20.1-...
76. spark-1.10.53-fabric
77. starlight-1.1.2+fabric.dbc156f
78. Steam_Rails-1.7.3+fabric-mc1.20.1
79. stormiespirits-fabric-1.20.1-3.3.1
80. structure_layout_optimizer-fabric-1.0.12
81. TerraBlender-fabric-1.20.1-3.0.1.11
82. toms_storage_fabric-1.20-1.7.1
83. vmp-fabric-mc1.20.1-0.2.0+beta.7.102-al...
84. yet_another_config_lib_v3-3.6.6+1.20.1-f...
85. YungsApi-1.20-Fabric-4.0.6
86. YungsBetterCaves-1.20.1-Fabric-2.0.7
87. YungsBetterDesertTemples-1.20-Fabric-3....
88. YungsBetterDungeons-1.20-Fabric-4.0.4
89. YungsBetterJungleTemples-1.20-Fabric-2....
90. YungsBetterMineshafts-1.20-Fabric-4.0.4
91. YungsBetterNetherFortresses-1.20-Fabric...
92. YungsBetterOceanMonuments-1.20-Fabric...
93. YungsBetterStrongholds-1.20-Fabric-4.0.3
94. YungsBetterWitchHuts-1.20-Fabric-3.0.3
95. YungsCaveBiomes-1.20.1-Fabric-2.0.5

## Аудит и исправления генерации руд

### Подтверждённые особенности исходников

- Big Globe 5.3.2 `bigglobe:ore` делает одну попытку размещения на каждую вертикальную секцию чанка 16×16×16. Это не эквивалент vanilla count placement; частоты нужно пересчитывать с учётом количества секций и высоты измерения.
- Big Globe Nether в этой версии имеет высоту 0..1024. Исходные вертикальные границы руд BetterNether, рассчитанные под стандартную высоту Nether 128, были масштабированы относительно новой высоты.
- BetterNether 9.0.13 исходные параметры руд сверялись с исходным кодом ветки 1.20.
- Create Fabric zinc в исходниках имеет отдельные count/size параметры. Существующая Big Globe конфигурация zinc в этой работе не менялась.
- В Create Nuclear 1.4.1 в проверенных источниках нет отдельных placed ore features для Thorium/Nitrate: не добавлять такие руды без подтверждённых регистраций/данных.

### Что изменено в Fix 0.4.12

Исходный репозиторий: [Mortylovelly/Fauna-](https://github.com/Mortylovelly/Fauna-), ветка `main`.

- Исправлены BetterNether Cincinnasite, Ruby common/large/rare/soul, Nether Lapis и Nether Redstone: диапазоны Y и параметры частоты/размера приведены к Nether высотой Big Globe.
- Для Cincinnasite и Nether Lapis добавлены дополнительные configured features с независимыми seed/попытками, поскольку Big Globe делает одну попытку на секцию.
- Исправлены biome tags Ruby Large и Ruby Soul: вместо неподходящего Ashen Wastes используется Big Globe `bigglobe:valley_of_souls`; для Soul Ruby также сохранён BetterNether `betternether:soul_plain`.
- Create Nuclear Lead разбит на две независимые попытки с шансом 0.625 каждая, чтобы приблизить исходную частоту count 10 в системе Big Globe.
- Добавлена Big Globe feature для Create Nuclear Autunite; она является приближением исходной striated-ore генерации, а не точным переносом layered feature.
- Существующая Uranium и Zinc конфигурация в этом изменении не переписывалась.

### Важное ограничение Autunite

Исходная Create Nuclear striated-ore конфигурация включает stone и deepslate replaceables. В текущем Fix 0.4.12 mapping Autunite покрывает `#bigglobe:stone_ore_replaceables`, но не подтверждено покрытие deepslate. Это нужно проверить/исправить отдельным изменением. Также частота Autunite — приближение, не доказательство полного совпадения исходной layered-генерации.

## Исходный проект и версия

Проверенные параметры текущего репозитория Fix:
- Minecraft 1.20.1
- Fabric Loader 0.19.5
- Fabric API 0.92.12+1.20.1
- Yarn 1.20.1+build.10
- Loom 1.17.21
- Gradle в GitHub Actions: 9.5.0
- JDK в GitHub Actions: 21 (Loom runtime)
- Java source/target/release: 17
- Fix source version: 0.4.12

## Коммит, сборка и артефакт

- **Коммит:** [ecadeb503f1e3d1eaaa3b2f6a472ae861088437d](https://github.com/Mortylovelly/Fauna-/commit/ecadeb503f1e3d1eaaa3b2f6a472ae861088437d)
- **GitHub Actions:** [Build Fix, run #93 / ID 44410159862](https://github.com/Mortylovelly/Fauna-/actions/runs/44410159862)
- **SHA запуска:** `ecadeb503f1e3d1eaaa3b2f6a472ae861088437d` (совпадает с HEAD `main`)
- **Результат:** `completed / success`; шаг Gradle Build и upload remapped jar успешны.
- **Artifact:** `fix-build`, ID `14475569247`, файл присутствует, не истёк на момент записи.

Успешный Gradle build не означает, что руды уже проверены в созданном мире. Проверка генерации в игре и сравнение реальной частоты руд ещё не выполнялись.

## Открытые задачи и ограничения

1. Проверить и при необходимости добавить Autunite replacement для deepslate ore replaceables.
2. Проверить фактическую частоту/распределение руд в новом мире Big Globe 5.3.2 (Overworld и Nether) и сравнить с исходными диапазонами.
3. Проверить Ruby Soul, Ruby Large и остальные BetterNether руды в соответствующих биомах, особенно Valley of Souls и BetterNether Soul Plain.
4. Не считать проверку в игре или серверную совместимость подтверждённой на основании одной успешной сборки.
5. Не менять состав 95-позиционной сборки без сообщения владельца; допускаются только явно сообщённые добавления/удаления отдельных модов.
6. Отдельные fauna/runtime проблемы из update 003 остаются отдельной задачей и не считаются исправленными изменениями генерации руд.

## Связанные записи

- [003 — Аудит fauna-модов и реальной работы Fix](003-fauna-modpack-overlap-and-fix-runtime-audit.md)
