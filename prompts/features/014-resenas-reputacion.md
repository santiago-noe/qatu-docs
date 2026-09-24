# 014 — Reseñas y reputación

## SPECIFY
```
/speckit.specify Reputación verificable basada solo en transacciones reales (flujo E en docs/02).
Historias: (P1) Como parte de una transacción cerrada quiero calificar a la otra con estrellas por criterio y comentario en 14 días. (P1) Quiero que las reseñas se publiquen solo cuando ambos califiquen o venza el plazo. (P1) Como reseñado quiero responder públicamente. (P1) Como usuario quiero ver promedio, número de reseñas, transacciones completadas, tasa de respuesta y antigüedad. (P2) Como moderador quiero ocultar reseñas que violen normas.
```

## Preguntas guía para /speckit.clarify
- ¿Se muestran reseñas de clientes a los oferentes antes de aceptar?

## PLAN (extra) — pegar después de prompts/plan-base.md
```
reviews con índice único (transaction_ref, author_id); agregados de reputación materializados y recalculados por evento.
```
