# 015 — Minecraft AI Agent 0.3.17: актуальная сборка и проверка версии

- **Дата:** 2026-11-14
- **Проект:** Minecraft AI Agent
- **Линия Minecraft / ветка:** Minecraft 1.20.1 / Fabric, `minecraft-1.20.1-fabric`
- **Версия мода:** `0.3.17`
- **Статус записи:** подтверждено по GitHub API, build files, metadata, исходникам и Actions; runtime не проверен

## Контекст

Паспорт и update 014 фиксировали версию 0.3.12. Текущая активная ветка обновилась до 0.3.17. Исторический slug `adastra-create-addon` перенаправляется GitHub как Moved Permanently; актуальный canonical repository — [Mortylovelly/Minecraft-AI-Agent](https://github.com/Mortylovelly/Minecraft-AI-Agent). Проверялась только ветка 1.20.1.

## Версии и сборка

- `gradle.properties`: Minecraft 1.20.1, Yarn `1.20.1+build.10`, Fabric Loader `0.17.2`, Fabric API `0.92.11+1.20.1`, mod version `0.3.17`.
- `build.gradle`: Fabric Loom 1.9.2; Java source/target/release 17.
- Gradle wrapper: 8.12.
- `fabric.mod.json`: Mod ID `minecraft_ai_agent`, Minecraft 1.20.1, Java >=17.
- Workflow: JDK 17, `./gradlew build --no-daemon`, artifact `minecraft-ai-agent-1.20.1`.
- README.txt также указывает версию 0.3.17.

## Команда и конфигурация

- Зарегистрирована одна slash-команда: `/aiagent status`.
- Клиентская конфигурация: `config/minecraft_ai_agent.json` (provider и API keys).
- Локальный chat archive: `config/minecraft_ai_agent_game_chat.jsonl`.
- Logs HUD archive: `logs/minecraft-ai-agent/latest.log` и ротируемые файлы `.1`, `.2`.
- AI tools, включая `set_time` и `run_minecraft_command`, являются function tools агента, а не отдельными slash-командами.

## Обнаруженное несоответствие

В текущем `MinecraftAiAgent.java` ответ команды `/aiagent status` жёстко содержит строку `Minecraft AI Agent 0.3.3`, хотя metadata/build version и README уже 0.3.17. Это подтверждённое устаревшее сообщение; исходный код в рамках аудита не изменялся.

## Коммит и сборка

- **HEAD:** `b15dd07889f732e2b195e166d8f1c98e1ba4e76b`
- **Commit:** `Update README version to 0.3.17` (2026-11-11).
- **Actions:** [run 43091757637](https://github.com/Mortylovelly/Minecraft-AI-Agent/actions/runs/43091757637), conclusion `success`, exact HEAD SHA.
- **Artifact:** `minecraft-ai-agent-1.20.1`, ID `13890200194`, присутствует и не истёк.

## Проверка в игре/на сервере

Runtime-тест именно версии 0.3.17 не подтверждён. Успешный Actions build подтверждает сборку и upload artifact, но не работу UI, log bridge, AI tools, NPC или dedicated-server поведения.

## Следующий шаг

В будущей кодовой задаче обновить текст `/aiagent status` так, чтобы он не отставал от `mod_version`, затем отдельно протестировать 0.3.17 в клиенте и на dedicated server.
