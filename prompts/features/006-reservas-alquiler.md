# 006 — Reservas de alquiler

## SPECIFY
```
/speckit.specify El corazón del alquiler: solicitar, aceptar, pagar y confirmar una reserva sin doble reserva (flujo A3 y A7 en docs/02, máquina RentalBooking en docs/03).
Historias: (P1) Como cliente quiero elegir fechas y modalidad y ver el desglose total antes de reservar. (P1) Como arrendador quiero aceptar o rechazar solicitudes dentro del plazo. (P1) Como cliente quiero pagar tras la aceptación y recibir confirmación con los datos de entrega. (P1) Como cliente o arrendador quiero cancelar según la política y ver cuánto se reembolsa antes de confirmar. (P2) Como cliente quiero solicitar una extensión. (P1) Como sistema quiero expirar solicitudes y pagos pendientes y liberar las fechas.
Reglas: el hold de fechas durante la solicitud también bloquea el calendario; snapshot inmutable del precio; el precio elige la combinación más barata de tarifas; reservas inmediatas solo para nivel 2.
```

## Preguntas guía para /speckit.clarify
- ¿Duración del hold y del plazo de pago?
- ¿Penalización al arrendador que cancela (reputación, bloqueo tras N)?
- ¿Tarifa de retraso: por hora o por día?

## PLAN (extra) — pegar después de prompts/plan-base.md
```
Máquina de estados pura en domain con tests exhaustivos de transiciones; jobs BullMQ de expiración idempotentes; integración con payments vía interfaz (en esta feature usar un PaymentGateway fake); eventos RentalRequested, RentalAccepted, RentalConfirmed, RentalCancelled.
```
