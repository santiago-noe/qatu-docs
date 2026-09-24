# 007 — Entrega, devolución y evidencias

## SPECIFY
```
/speckit.specify La confianza del alquiler depende de registrar el estado del bien al entregar y al devolver (flujos A4, A5 y A6 en docs/02).
Historias: (P1) Como arrendador quiero registrar fotos y checklist de accesorios en la entrega. (P1) Como arrendatario quiero confirmar la recepción con mi código de 6 dígitos y subir mis propias fotos. (P1) Como arrendador quiero registrar la devolución con fotos y checklist y confirmarla con el código del arrendatario. (P1) Como arrendador quiero una ventana de 24 h para marcar 'todo OK' o reportar daño/faltante. (P1) Como sistema quiero enviar recordatorios de entrega y devolución, aplicar tarifa de retraso y marcar NO_DEVUELTA tras el límite. (P1) Como cualquiera de las partes quiero reportar no-show. (P2) Como arrendatario quiero reportar una falla durante el uso.
Reglas: las fotos llevan fecha y hora del servidor; los códigos expiran y tienen límite de intentos; las evidencias no se pueden borrar.
```

## Preguntas guía para /speckit.clarify
- ¿Se exige geolocalización al confirmar la entrega?
- ¿Ventana de tolerancia para no-show?

## PLAN (extra) — pegar después de prompts/plan-base.md
```
handover_records inmutables; fotos con hash SHA-256 guardado; códigos hasheados con expiración; eventos RentalStarted, RentalReturned, DamageReported.
```
