# Команды и инструменты — Telegram Q07 RU Assistant

## Identity
```bash
sha256sum "SpeechAssistant-q07-ru.apk"
ls -lh "SpeechAssistant-q07-ru.apk"
```

## Manifest
```bash
aapt dump badging SpeechAssistant-q07-ru.apk
aapt dump permissions SpeechAssistant-q07-ru.apk
apkanalyzer manifest print SpeechAssistant-q07-ru.apk
```

## DEX / Smali
Использовался совместимый baksmali.jar; Android SDK smali stack ранее упирался в JCommander binary incompatibility.
```bash
java -jar tools/baksmali.jar d classes.dex -o smali/classes
# повторить для classes2.dex ... classes35.dex
```

## Search
```bash
grep -RniE 'Q07Bridge|Q07CaKey|GigaAsr|PiperCaTts|ExternalAdapter|SpeechKeyName|NlpBean' smali/
grep -Rni 'register_service\|SpeechKeyName\|app_action_config' .
```

## Assets
```bash
cat assets/dm_car_ctrl.json
cat assets/dm_air_ctrl.json
cat assets/dm_cmd.json
cat assets/dm_cmd_ctrl.json
cat assets/dm_special.json
cat assets/key_words.json
cat assets/guide_words.txt
cat assets/module_config.txt
cat assets/app_action_config.txt
```

## Native
```bash
unzip -l SpeechAssistant-q07-ru.apk | grep -E 'lib/.+\.so$'
file path/to/lib*.so
readelf -h path/to/lib*.so
strings -a path/to/lib*.so | less
nm -D path/to/lib*.so | grep -E 'JNI|Java_'
```

## Runtime
```bash
adb install -r SpeechAssistant-q07-ru.apk
adb logcat -c
adb logcat -s Q07Bridge:V GigaAsr:V Q07PiperCaTts:V Q07CaKey:V
```

## Backend
```bash
adb shell pm path com.incall.apps.speechadapter
adb shell dumpsys package com.incall.apps.speechadapter
adb shell service list
adb shell ps -A
adb shell getprop
```

Не устанавливать автоматически и не менять автомобиль без отдельного согласования.
