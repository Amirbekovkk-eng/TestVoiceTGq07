# Источники и степень уверенности

## A — прямое статическое evidence
APK hash/package/version/SDK/sharedUserId/debuggable; 36 DEX; manifest surface; Q07Bridge/Q07CaKey/GigaAsr/PiperCaTts; Russian assets/models; app_action_config; dm_* counts; SpeechKeyName payload.

## B — сильный вывод
Telegram APK реализует русский voice frontend, нормализует семантику и передаёт структурированный payload в downstream boundary com.incall.apps.speechadapter.

## C — Telegram runtime/test claims
Q07 1.5; рекомендации проверить 1.6/1.7; «Нихао нихао»; named zones; до 10 команд.

## D — unresolved
Installed adapter receiver, exact downstream vehicle protocol, final HAL/MCU path, per-version compatibility, exact runtime-active optional engines.

Правило: факт, inference и runtime claim не смешивать.
