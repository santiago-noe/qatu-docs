# 013 — Notificaciones

## SPECIFY
```
/speckit.specify Avisos oportunos para que las transacciones avancen (recordatorios de docs/02).
Historias: (P1) Como usuario quiero recibir notificaciones in-app, push y WhatsApp/SMS de los eventos importantes (nueva solicitud, aceptación, pago, recordatorios, mensajes, disputas). (P1) Quiero elegir canales por tipo de aviso. (P1) Como sistema quiero plantillas en español con variables y registro de envíos. 
Reglas: avisos críticos (pago, disputa, seguridad) no se pueden desactivar del todo; respetar horario silencioso salvo urgencias.
```

## Preguntas guía para /speckit.clarify
- ¿Proveedor de WhatsApp/SMS?
- ¿Horario silencioso por defecto?

## PLAN (extra) — pegar después de prompts/plan-base.md
```
Consumidor de eventos del outbox; puerto NotificationChannel (InApp, WebPush, WhatsApp, SMS, Email) con adaptador de consola; cola con reintentos y deduplicación.
```
