# TestVoiceTGq07

Полный reverse-engineering архив Telegram/Q07 Russian Voice Assistant.

## APK
- `SpeechAssistant-q07-ru.apk`
- SHA-256: `abe17d7bc5890ebca2db05e8de983bbbdea999f782c9d337d8972a558e42e5a4`
- package: `com.incall.apps.speechassistant`
- versionName: `V16.1912`
- versionCode: `2000000000`
- Android: min/target SDK 30/30
- `sharedUserId=android.uid.system`
- `debuggable=true`
- DEX: `classes.dex` … `classes35.dex` (36 DEX)

## Что здесь хранится
- полный анализ архитектуры;
- русская инструкция и команды из Telegram;
- ASR / NLU / TTS;
- Q07Bridge / Q07CaKey / GigaAsr / PiperCaTts;
- NlpBean / SpeechKeyName / ExternalAdapter;
- rule tables и dialogue context;
- dispatch до `com.incall.apps.speechadapter`;
- native/JNI и модели;
- negative findings и unresolved items;
- команды анализа, чтобы не повторять исследование.

## Важно
Этот репозиторий отдельный от stock `SpeechAssistant`. Stock backend рассматривается только как внешний reference boundary; его код и артефакты сюда не копируются.

См. `docs/MASTER_RU.md` и `docs/COMMANDS.md`.
