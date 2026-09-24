# 010 — Pagos, comisiones y liquidaciones

## SPECIFY
```
/speckit.specify Cobrar, dividir y liquidar el dinero de forma correcta, trazable y sin que Qatu custodie fondos de terceros (docs/03 sección Dinero).
Historias: (P1) Como cliente quiero pagar con Yape, tarjeta u otros medios soportados por el proveedor de pagos. (P1) Como piloto quiero un modo 'registrado' donde el pago se hace fuera (Yape/efectivo) y ambas partes lo confirman en la app. (P1) Como oferente quiero conectar mi cuenta del proveedor de pagos para recibir mi parte automáticamente. (P1) Como oferente quiero ver mis ingresos, comisiones y liquidaciones pendientes y pagadas. (P1) Como sistema quiero reembolsar total o parcialmente según cancelaciones y resoluciones. (P1) Como admin quiero conciliar pagos con el proveedor y ver ingresos de Qatu. (P2) Como Qatu quiero facturar mensualmente la comisión a oferentes en modo registrado.
Reglas: ledger de doble partida inmutable; webhooks idempotentes; liquidación retenida si hay disputa abierta; montos en céntimos.
```

## Preguntas guía para /speckit.clarify
- ¿Proveedor de pagos inicial: Mercado Pago split o Culqi?
- ¿Plazo de liquidación T+N?
- ¿Quién asume la comisión de la pasarela?

## PLAN (extra) — pegar después de prompts/plan-base.md
```
Puerto PaymentGateway con adaptadores ManualRecorded, MercadoPagoSplit (OAuth por oferente) y Fake para tests; ledger_entries con verificación de balance por journal; tabla processed_webhooks; job de conciliación diario.
```
