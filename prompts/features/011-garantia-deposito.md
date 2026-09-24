# 011 — Garantía / depósito

## SPECIFY
```
/speckit.specify Gestionar la garantía del alquiler con estados claros y evidencias (docs/03 Deposit y DepositStrategy; propuesta v2 sección 7).
Historias: (P1) Como arrendatario quiero ver el monto, condiciones y plazo de devolución de la garantía antes de reservar. (P1) Como sistema quiero registrar la garantía como RETENIDA al confirmar y LIBERADA si no hay reporte en la ventana de inspección. (P1) Como arrendador quiero proponer un descuento de la garantía con evidencia; como arrendatario quiero aceptarlo o disputarlo. (P1) Como soporte quiero resolver y aplicar LIBERADA, DEVOLUCION_PARCIAL o APLICADA_POR_DANO. (P2) Como arrendatario de nivel alto quiero alquilar sin garantía en herramientas de bajo riesgo.
Reglas: sin comisión sobre la garantía; el descuento nunca supera la garantía (el excedente va a disputa); estrategia por categoría y usuario.
```

## Preguntas guía para /speckit.clarify
- ¿Estrategia del MVP: RECORDED_OFFLINE?
- ¿El PSP elegido permite preautorización y por cuántos días?

## PLAN (extra) — pegar después de prompts/plan-base.md
```
Puerto DepositStrategy con implementaciones NONE y RECORDED_OFFLINE (CARD_PREAUTH detrás de feature flag); asientos de ledger para cada movimiento.
```
