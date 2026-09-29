# Русская инструкция — Q07 RU Voice Assistant

### Установка
```bash
adb install -r SpeechAssistant-q07-ru.apk
```

Package совпадает со stock assistant, поэтому перед установкой нужен backup/rollback plan.

### Логи
```bash
adb logcat -c
adb logcat -s Q07Bridge:V GigaAsr:V Q07PiperCaTts:V Q07CaKey:V
```

### Активация
«Нихао нихао» / кнопка на руле / экранный микрофон.

### Зоны
speaker (default), passenger, rear left, rear right, all.

### Основные области команд
климат; сиденья; окна/roof/shades; двери/багажник; свет; зеркала/руль/дворники; дисплеи/HUD; музыка/звук; driving modes/energy/suspension; comfort/scenarios; connectivity/charging; cameras/DVR/panoramic; vehicle state; apps; phone.

### Multi-command
Исходное описание заявляет до 10 команд за одно utterance. Это runtime/test claim до подтверждения runtime/literal evidence.

### Follow-up
Тестировать history-aware пары: температура -> «сделай побольше», seat heat -> «сильнее», open window -> follow-up, open settings -> «закрой», change theme -> «следующую».
