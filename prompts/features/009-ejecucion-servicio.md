# 009 — Ejecución del servicio

## SPECIFY
```
/speckit.specify Seguimiento del trabajo desde la programación hasta el cierre (flujo B pasos 4-10, máquina ServiceJob en docs/03).
Historias: (P1) Como proveedor quiero marcar 'en camino' con hora estimada. (P1) Como proveedor quiero iniciar el trabajo con el código que me dicta el cliente. (P1) Como proveedor quiero proponer órdenes de cambio (monto, motivo, fotos) que el cliente aprueba o rechaza en la app. (P1) Como proveedor quiero registrar gastos de materiales con foto del comprobante. (P1) Como proveedor quiero finalizar con fotos del resultado y horas reales. (P1) Como cliente quiero confirmar el trabajo o reportar un problema en 48 h; si no respondo se autoconfirma. (P2) Como cliente quiero pedir una visita de corrección dentro de la garantía del trabajo. (P2) Como proveedor quiero compartir mi trabajo en curso con un contacto de confianza.
Reglas: horas reales cobradas no superan lo estimado más órdenes de cambio aprobadas; materiales sin comisión.
```

## Preguntas guía para /speckit.clarify
- ¿Días por defecto de garantía del trabajo?
- ¿Qué pasa si el cliente no aprueba una orden de cambio: se cierra lo hecho?

## PLAN (extra) — pegar después de prompts/plan-base.md
```
service_jobs, change_orders, material_expenses; eventos JobScheduled, JobStarted, ChangeOrderApproved, JobCompleted, JobConfirmed.
```
