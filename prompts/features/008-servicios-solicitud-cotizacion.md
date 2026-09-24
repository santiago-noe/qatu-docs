# 008 — Solicitud de servicio y cotizaciones

## SPECIFY
```
/speckit.specify Los clientes contratan servicios de dos formas: reserva directa a un proveedor o solicitud de cotización a varios (flujos B y C en docs/02).
Historias: (P1) Como cliente quiero reservar directamente un paquete o tarifa por hora eligiendo fecha, franja, dirección, descripción y fotos. (P1) Como proveedor quiero aceptar o rechazar reservas directas dentro del plazo. (P1) Como cliente quiero publicar una solicitud de cotización con fotos, zona, fechas preferidas, urgencia y presupuesto referencial opcional. (P1) Como sistema quiero enviar la solicitud a hasta N proveedores compatibles por oficio, cobertura, disponibilidad y reputación, rotando de forma justa. (P1) Como proveedor quiero cotizar (monto, alcance, materiales incluidos o no, duración, validez), pedir visita técnica con costo opcional, o declinar. (P1) Como cliente quiero comparar cotizaciones y aceptar una, lo que crea el trabajo y cierra las demás.
Reglas: la dirección exacta se revela solo al proveedor seleccionado; una cotización aceptada genera snapshot de precio; expiración automática de solicitudes y cotizaciones.
```

## Preguntas guía para /speckit.clarify
- ¿N máximo de proveedores notificados?
- ¿La visita técnica pagada se descuenta siempre del trabajo?
- ¿Plazo de expiración de solicitudes?

## PLAN (extra) — pegar después de prompts/plan-base.md
```
service_requests, quotes, matching como caso de uso con estrategia intercambiable (MatchingPolicy) y tests; eventos ServiceRequested, QuoteSubmitted, QuoteAccepted; jobs de expiración.
```
