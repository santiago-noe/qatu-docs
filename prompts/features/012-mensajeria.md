# 012 — Mensajería en la app

## SPECIFY
```
/speckit.specify Las partes necesitan coordinar sin exponer datos personales antes de tiempo (docs/05 antidesintermediación).
Historias: (P1) Como cliente quiero escribir al arrendador o proveedor desde una publicación o transacción. (P1) Quiero enviar fotos y ubicación aproximada. (P1) Quiero ver estado de entregado/leído. (P1) Como sistema quiero detectar teléfonos, correos o pedidos de pago por fuera antes de confirmar y mostrar un aviso. (P1) Quiero reportar y bloquear a un usuario. (P2) Respuestas rápidas para oferentes.
Reglas: el chat queda como evidencia en disputas; los mensajes no se pueden editar tras 5 minutos ni borrar para la otra parte.
```

## Preguntas guía para /speckit.clarify
- ¿Se permite llamada enmascarada o solo chat en el MVP?

## PLAN (extra) — pegar después de prompts/plan-base.md
```
conversations, messages; Socket.IO con adaptador Redis y fallback a polling; detector de contacto como función pura testeada.
```
