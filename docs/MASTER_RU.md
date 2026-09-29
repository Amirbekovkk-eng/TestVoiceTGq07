# Telegram Q07 RU Assistant — MASTER REVERSE ENGINEERING
Дата фиксации: 2026-09-29

## Идентификация
- APK: SpeechAssistant-q07-ru.apk
- SHA-256: abe17d7bc5890ebca2db05e8de983bbbdea999f782c9d337d8972a558e42e5a4
- Package: com.incall.apps.speechassistant
- VersionName: V16.1912
- VersionCode: 2000000000
- Android min/target SDK: 30/30
- sharedUserId: android.uid.system
- debuggable: true
- ABI: arm64-v8a
- DEX: classes.dex … classes35.dex (36 файлов)

Это отдельный reference/test APK. Package совпадает со stock SpeechAssistant, поэтому обычная установка может заменить stock assistant.

## Архитектура
```
Wake / steering wheel / on-screen mic
              |
              v
        VW / SR / VPR / MSR
              |
              v
       ASR + NLU + rules
              |
              v
     dialogue context/memory
              |
              v
        NlpBean / JSON
              |
              v
       SpeechKeyName route
              |
              v
com.incall.apps.speechadapter
              |
              v
        Q07 / vehicle backend
```

## Ключевые компоненты
- com.stand.asr.GigaAsr
- com.stand.q07.Q07Bridge
- com.stand.q07.Q07CaKey
- com.stand.q07.PiperCaTts
- NlpBean
- SpeechKeyName
- ExternalAdapter

## Runtime surface
Зафиксированы MainActivity, SettingsActivity, DictService, StatusService, UiService, TtsService, SksAccessService, SrService, SpeechClientService, AIMemoryService, DataProvider, SpeechAssistantReceiver, VoiceEnableReceiver.

Actions: SPEECH_WAKEUP, SPEECH_SLEEP, KEY_CLICK, VIEW_CLICK, START_SPEAK, NLI_QUERY, SPEECH_SERVER, CLIENT_STATE, PERFORM_CLICK, boot completion.

Разрешения включают microphone/camera/location/network, foreground service, overlay, WRITE_SECURE_SETTINGS, WRITE_SETTINGS, INJECT_EVENTS, READ_FRAME_BUFFER, BLUETOOTH_PRIVILEGED, package install/delete и custom com.incall.* / com.changan.speech.permission.AI_MEMORY.

## Dispatch
assets/app_action_config.txt маршрутизирует:
mapU|musicX|musicLocal|musicBt|lightShow|weather|telephone|defaultService|cmd|airControl|carControl|StreamHelp
в package com.incall.apps.speechadapter, action com.incall.apps.speechadapter.register_service, mode startService, extra SpeechKeyName.

Отдельно questionID -> com.changan.aiassist.IflytekService с extra params.

Для carControl и airControl зафиксирован registered NLU-client Messenger route; при отсутствии обработки sender-side fallback идёт в defaultService.

## NLU payload
Структурированный JSON содержит service, operation, semantic.slots, direction, data, answer, sid и dialogue context/history.

## Rules
dm_car_ctrl.json = 5 правил.
- seat heating: PLUS|MINUS / MIN|MAX с наследованием history;
- seat ventilation: аналогично;
- windows: windTemp -> OPEN/ACTION и наследование name;
- HUD contextual handling.

dm_air_ctrl.json = 2.
- follow-up climate commands могут копировать предыдущую direction;
- история fan/temperature используется для продолжения.

dm_cmd.json = 58.
Примеры: TAKE_VIDEO_DVR+CLOSE -> CLOSE_VIDEO_DVR; OPEN_VEHICLE_SET+CLOSE -> CLOSE_VEHICLE_SET; OPEN_AIRCONTROL_SET+CLOSE -> CLOSE_AIRCONTROL_SET; OPEN_SYSTEM_SET+CLOSE -> CLOSE_SYSTEM_SET; OPEN_WIFI+CLOSE -> CLOSE_WIFI; OPEN_HOTSPOT...

dm_cmd_ctrl.json = 6.
Контекст fragrance, ambient light, wallpaper/theme/look/image и airflow.

dm_special.json = 7.
Контекст ambient light, wallpaper/theme, fragrance, ambient light, air-control.

## Русский stack
Подтверждены:
- gigaam/model.int8.onnx
- ruaccent.bin
- русские command resources
- GigaAsr / Q07Bridge / Q07CaKey / PiperCaTts

В command corpus отражены HVAC, seats, windows/roof/shades, doors/trunk, lights, mirrors/steering/wipers, screens/HUD, sound/music, driving modes/energy/suspension, comfort/scenarios, connectivity/charging, cameras/vehicle state, apps и phone.

## Zones / batching
Поля: occupantZone, setSoundZone, batchSizeMax, batchResults.
Telegram-инструкция: default zone=speaker; passenger/rear left/rear right/all; заявлено до 10 команд за utterance. Последнее хранить как runtime/test claim, пока не подтверждено literal или runtime trace.

## ASR / NLU / TTS
ASR: локальный русский frontend с GigaAM и GigaAsr.
NLU: rule/semantic normalization + локальные inference components; найдены iFlytek, ONNX Runtime, MNN, SNPE, Sherpa-ONNX. Нельзя считать каждый engine активным в одном runtime path без xref/trace.
TTS: PiperCaTts / com.stand.q07.PiperCaTts.

## Native
Есть native speech/inference stack, включая GigaAM/ONNX-related, iFlytek ASR/NLI/VW/VPR/TTS, MNN/SNPE/Sherpa-ONNX и libnlu_engine.so. Для каждого .so участие в runtime path считать отдельно до подтверждения.

## Что доказано
- APK identity/hash/version/SDK/sharedUserId/debuggable
- 36 DEX
- manifest/runtime surface
- Q07-specific classes
- Russian ASR/model resources
- structured NLU payload
- rule tables и dialogue carry-over
- config-driven dispatch
- register_service + SpeechKeyName
- command taxonomy и zones на уровне corpus/semantic model

## Что не доказано статикой
- receiver implementation установленного com.incall.apps.speechadapter
- конечный Binder/AIDL/HIDL/HAL/MCU chain
- точный MCU message format
- полная runtime последовательность конкретной прошивки
- точное числовое batch limit, если нет literal/xref
- фактическая активация всех bundled inference engines

## Русская инструкция из Telegram
- тестировалось на Q07 1.5; проверить 1.6/1.7 и другие версии;
- установка: adb install -r;
- очистка логов: adb logcat -c;
- фильтр: adb logcat -s Q07Bridge:V GigaAsr:V Q07PiperCaTts:V Q07CaKey:V;
- wake: «Нихао нихао», steering-wheel button, screen microphone;
- default zone=speaker;
- passenger / rear left / rear right / all;
- заявлено до 10 команд за одно utterance;
- широкий русский command corpus по автомобилю, приложениям и телефону.

## Следующий runtime checkpoint
Получить installed com.incall.apps.speechadapter, снять logcat указанным фильтром, затем связать SpeechKeyName -> receiver -> downstream -> vehicle stack.
