# 015 — Incidencias, disputas y Libro de Reclamaciones

## SPECIFY
```
/speckit.specify Resolver problemas con reglas claras y cumplir la normativa de consumidor (flujo D en docs/02, docs/05).
Historias: (P1) Como parte quiero abrir una incidencia desde la transacción con tipo, descripción, monto y evidencias. (P1) Como contraparte quiero responder en 48 h aceptando, contraofertando o rechazando. (P1) Como soporte quiero una bandeja priorizada con toda la evidencia de la transacción (fotos de entrega y devolución, chat, códigos, pagos) y registrar una resolución motivada que se ejecute automáticamente (reembolso, garantía, visita de corrección). (P1) Incidencias de seguridad con bloqueo preventivo. (P1) Como consumidor quiero registrar un reclamo o queja en el Libro de Reclamaciones virtual, recibir copia y respuesta en plazo.
Reglas: una disputa abierta congela la liquidación; toda resolución queda auditada.
```

## Preguntas guía para /speckit.clarify
- ¿Plazo interno de resolución de disputas?
- ¿Montos máximos que soporte puede aplicar sin aprobación de admin?

## PLAN (extra) — pegar después de prompts/plan-base.md
```
disputes, dispute_messages, complaints con correlativo por año; SLA con jobs; la resolución emite eventos consumidos por payments y deposits.
```
