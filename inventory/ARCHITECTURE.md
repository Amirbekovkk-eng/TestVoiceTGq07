# Архитектура

```text
Wake input
   |
   +-- steering wheel / screen mic / wake phrase
   v
VW / SR / VPR / MSR
   v
GigaAsr / iFlytek / local speech
   v
NLU + rules + dialogue memory
   v
NlpBean / SpeechKeyName JSON
   v
app_action_config routing
   v
com.incall.apps.speechadapter
   v
registered client / defaultService
   v
Q07 downstream vehicle/application layer
```

Ключевые точки: Q07Bridge, Q07CaKey, GigaAsr, PiperCaTts, SpeechKeyName, NlpBean, ExternalAdapter.
