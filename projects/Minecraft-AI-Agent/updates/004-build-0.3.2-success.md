# 004 — Успешная сборка Minecraft AI Agent 0.3.2

- **Дата:** 2026-11-09
- **Проект:** Minecraft AI Agent
- **Линия / ветка:** Minecraft 1.20.1 Fabric / `minecraft-1.20.1-fabric`
- **Версия мода:** 0.3.2
- **Статус записи:** подтверждено — GitHub Actions build и artifact успешны; runtime не проверен

## Контекст

После публикации функции persistent log archive и COPY в commit `42b7a0f2e19b79b5a3065d3e444b367e6f3c52ea` первая попытка Actions run завершилась failure. GitHub вернул ноль jobs и ноль artifacts; Gradle output отсутствовал. Это не позволяло утверждать, что компиляция сломана, но и не подтверждало сборку.

## Симптом или задача

Нужно было получить реальный успешный build 0.3.2 и JAR, содержащий архив логов и кнопку COPY.

## Исследование

Проверено:
- run `42672344378`: `completed / failure`, GitHub API показывал 0 jobs, 0 artifacts;
- failed jobs rerun endpoint отклонил повторный запуск, так как jobs в run отсутствовали;
- workflow `.github/workflows/build.yml` уже работал на push и pull_request, но не имел manual dispatch trigger.

## Изменения

В workflow добавлен trigger `workflow_dispatch`. Существующие push / pull_request triggers сохранены. Это даёт возможность вручную перезапускать build из GitHub Actions UI. Исходники persistent log archive/COPY из feature commit не изменялись.

| Файл | Что изменено | Зачем |
|---|---|---|
| `.github/workflows/build.yml` | Добавлен `workflow_dispatch:` | Дать возможность ручного запуска workflow |

## Коммиты и сборки

- Feature commit с исходниками 0.3.2: `42b7a0f2e19b79b5a3065d3e444b367e6f3c52ea`.
- Build commit / exact HEAD: `d11654d0b8995211ee23281ec876721a30b94c40` (workflow trigger update, исходники функции включены в тот же tree).
- GitHub Actions run: [42676207842](https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/42676207842).
- SHA запуска: `d11654d0b8995211ee23281ec876721a30b94c40`.
- Результат сборки: `completed / success`.
- Job `build`: success.
- Шаги `Set up JDK 17`, `Build`, `Upload mod jar`: success.
- Artifact: `minecraft-ai-agent-1.20.1`, ID `13699458967`, 216171 bytes.
- В artifact ZIP содержатся `minecraft-ai-agent-0.3.2.jar` (155219 bytes) и `minecraft-ai-agent-0.3.2-sources.jar` (70741 bytes).
- SHA256 основного JAR: `1783d584b769f1edba0c6d8e0f2c12b861fea0a71ef50534800488b65426efbf`.

## Проверка артефакта

Основной JAR скачан из Actions artifact и проверен локально:
- `fabric.mod.json` содержит version `0.3.2`;
- Minecraft dependency — `1.20.1`;
- Java dependency — `>=17`;
- entrypoints сохранены;
- в JAR присутствуют `AiLogFile.class`, `AiLogClient.class`, `AiLogOverlay.class`, `AiAgentLog.class`.

Это подтверждает наличие новых скомпилированных классов в JAR, но не является runtime-тестом Minecraft.

## Проверка в игре/на сервере

Не проверено. Владелец ещё не сообщил результат установки/запуска. Нужно проверить singleplayer/integrated server и dedicated Fabric server отдельно.

## Инструкции владельцу

Установить один и тот же JAR `minecraft-ai-agent-0.3.2.jar` в `mods` клиента и выделенного Fabric 1.20.1 сервера. Для просмотра серверного потока логов на dedicated server нужен OP level 2. Старый JAR 0.3.1 заменить, а не держать одновременно две версии одного мода.

## Риски и ограничения

- Новый архив содержит только события, дошедшие до клиентской панели, а не полный vanilla `latest.log` и не каждый сторонний клиентский logger.
- Функция persistent archive, button COPY, migration и rotation ещё не проверены в живой игре.
- Успешный build не доказывает корректность clipboard UI и файловой ротации в runtime.

## Открытые вопросы и следующий шаг

Проверить, что создаётся `.minecraft/logs/minecraft-ai-agent/latest.log`, кнопка COPY копирует архив целиком, а данные остаются после перезапуска. Затем по присланным логам исправить возможные runtime-проблемы.

## Связанные записи

- [003 — Persistent log archive и кнопка COPY](003-persistent-log-archive-and-copy.md)
