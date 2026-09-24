# 02 — Flujos completos

Los tiempos (timeouts, ventanas) son **parámetros configurables** en `platform_settings`, no constantes.

---
## A. ALQUILER DE HERRAMIENTAS

### A1. Publicar herramienta (arrendador)
1. Elige categoría → formulario con atributos de la categoría (marca, modelo, potencia, voltaje…).
2. Fotos (mín. 3, máx. 12) + foto de la placa/número de serie (privada, solo para disputas).
3. Precios: por día (obligatorio); por hora, fin de semana, semana, mes (opcionales).
   Descuentos por duración opcionales.
4. Garantía/depósito sugerido por el sistema según valor declarado y categoría; editable dentro de rangos.
5. Valor de reposición declarado (base para disputas).
6. Accesorios incluidos (checklist que se usará en entrega/devolución).
7. Logística: recojo en punto (dirección exacta privada, zona aproximada pública) y/o delivery con tarifa.
8. Reglas: requisitos del arrendatario (nivel de verificación mínimo), instrucciones de uso,
   política de cancelación (Flexible / Moderada / Estricta), modo de reserva (por solicitud | inmediata).
9. Calendario: bloqueos manuales + antelación mínima + duración mín./máx.
10. Estado inicial: `EN_REVISION` si es el primer anuncio o categoría de riesgo; si no, `PUBLICADA`.

### A2. Buscar y elegir (cliente)
Búsqueda por texto/categoría + ubicación + fechas → solo se muestran herramientas **disponibles** en
ese rango. Detalle muestra: fotos, precio calculado para las fechas, garantía, distancia aproximada,
reputación del arrendador, política de cancelación y **desglose total antes de reservar**.

### A3. Reservar
1. Cliente elige fechas/horas, modalidad (recojo/delivery) y franja de entrega.
2. Sistema valida: disponibilidad (bloqueo transaccional), nivel de verificación, duración mín./máx.
3. Desglose: alquiler + tarifa de servicio + delivery (si aplica) + garantía = total.
4. **Modo por solicitud**: se crea `SOLICITADA`; el arrendador tiene 24 h para aceptar/rechazar; si no,
   `EXPIRADA`. Las fechas quedan en *hold* durante ese tiempo.
   **Modo inmediato**: pasa directo a pago.
5. Tras aceptación, el cliente paga en ≤ 2 h (si no: `EXPIRADA`, libera fechas).
6. Pago OK → `CONFIRMADA`. Se revela dirección exacta y teléfono enmascarado/chat completo.

### A4. Entrega (handover)
1. Recordatorios T-24 h y T-2 h a ambos.
2. En el encuentro, el **arrendador** registra fotos del estado y marca el checklist de accesorios.
3. El **arrendatario** revisa y confirma con un **código de 6 dígitos** que le muestra la app
   (prueba de entrega con fecha, hora y geolocalización aproximada). También puede subir sus fotos.
4. → `EN_CURSO`. Si el arrendador no se presenta: el cliente reporta *no-show* → reembolso total.
   Si el cliente no se presenta en la ventana (ej. 2 h): `NO_SHOW_CLIENTE`, aplica política de cancelación.

### A5. Durante el alquiler
- Extensión: el cliente la solicita; si el calendario lo permite, el arrendador acepta y se cobra la diferencia.
- Recordatorio de devolución T-12 h y T-2 h.
- Reporte de falla durante el uso (con fotos) → abre incidencia sin cerrar la reserva.

### A6. Devolución y cierre
1. Arrendatario entrega; **arrendador** registra fotos y checklist; confirma con código del arrendatario.
2. Ventana de inspección (24 h): arrendador marca "Todo OK" o "Reportar daño/faltante".
3. Sin reporte (o "Todo OK") → garantía `LIBERADA`, reserva `CERRADA`.
4. Con reporte → incidencia (ver flujo D). Garantía `EN_DISPUTA` hasta resolución.
5. **Retraso**: se cobra tarifa de retraso prorrateada por hora/día. Tras 48 h sin devolución ni
   contacto → `NO_DEVUELTA`, soporte interviene, posible aplicación total de garantía y bloqueo de cuenta.
6. Reseñas doble ciego (ver flujo E). Liquidación al arrendador (ver docs/03).

### A7. Cancelaciones (alquiler)
| Política | Cliente cancela | Reembolso |
|---|---|---|
| Flexible | ≥ 24 h antes | 100% alquiler |
| Moderada | ≥ 72 h antes | 100%; después 50% |
| Estricta | ≥ 7 días | 100%; después 0% (salvo arrendador re-alquile) |
La tarifa de servicio de Qatu se reembolsa solo si cancela el arrendador o hay no-show del arrendador.
Cancelación por arrendador: reembolso total al cliente + penalización de reputación (y bloqueo tras N).

---
## B. SERVICIOS — RESERVA DIRECTA

1. Cliente elige oficio → ve proveedores que cubren su zona, con tarifa/paquetes, disponibilidad, reputación.
2. Elige paquete o tarifa por hora (horas estimadas), fecha y franja, dirección, descripción + fotos.
3. Solicitud → proveedor tiene X h (ej. 4 h; 1 h si es "urgente hoy") para aceptar. Si no, `EXPIRADO`
   y se sugieren otros proveedores.
4. Aceptado → pago del monto estimado → `PROGRAMADO`.
5. Día del servicio: proveedor marca `EN_CAMINO` (ETA); al llegar, el cliente le dicta el **código de
   inicio** → `EN_PROGRESO`.
6. **Cambios de alcance**: si aparece trabajo extra o materiales, el proveedor crea una **orden de
   cambio** (monto + motivo + fotos). Solo se ejecuta si el cliente la aprueba en la app (y paga la diferencia).
7. **Materiales**: línea separada. O los trae el cliente, o el proveedor los compra y adjunta foto del
   comprobante (reembolso sin comisión).
8. Proveedor marca `FINALIZADO` con fotos del resultado y horas reales (si es por hora; tope = estimado
   + órdenes de cambio aprobadas).
9. Cliente confirma o reporta problema en 48 h; si no hace nada → auto-confirmado.
10. `CERRADO` → reseñas → liquidación. **Garantía del trabajo** (ej. 15 días): el cliente puede pedir
    una visita de corrección sin costo dentro de ese plazo.

## C. SERVICIOS — SOLICITUD DE COTIZACIÓN

1. Cliente publica solicitud: oficio, descripción, fotos, dirección (zona pública, exacta privada),
   fechas preferidas, presupuesto referencial (opcional), urgencia.
2. **Matching**: se notifica a hasta N (ej. 5) proveedores compatibles por oficio, cobertura de zona,
   disponibilidad, reputación y tasa de respuesta (rotación justa para nuevos proveedores).
3. Cada proveedor puede: **cotizar** (monto, alcance, qué incluye, materiales incluidos o no, duración,
   validez de la oferta), **pedir visita técnica** (con costo opcional descontable del trabajo) o
   **declinar**. Chat permitido con contacto enmascarado.
4. La solicitud expira a los X días o cuando el cliente acepta una cotización.
5. Al aceptar: las demás cotizaciones pasan a `NO_SELECCIONADA` → se crea un **Trabajo** que sigue el
   flujo B desde el paso 4. Trabajos grandes pueden dividirse en **hitos** (pago por hito, fase 2).

---
## D. INCIDENCIAS Y DISPUTAS (común)
1. Cualquiera de las partes abre incidencia desde la transacción: tipo (daño, faltante, no-show,
   trabajo mal hecho, cobro indebido, seguridad), descripción, monto reclamado, evidencias.
2. La contraparte tiene 48 h para responder: aceptar, contraofertar o rechazar con evidencia.
3. Acuerdo → se ejecuta (descuento de garantía, reembolso parcial, visita de corrección).
4. Sin acuerdo → **mediación de Soporte**: revisa evidencias (fotos de entrega vs devolución, chat,
   códigos, geolocalización) y decide dentro de las reglas publicadas. Decisión registrada y motivada.
5. Incidencias de **seguridad** (agresión, robo) saltan la negociación: van directo a Soporte, con
   bloqueo preventivo de cuentas.
6. Aparte: **Libro de Reclamaciones virtual** para reclamos de consumidores contra Qatu (INDECOPI).

## E. RESEÑAS (común)
- Tras el cierre, ambas partes tienen 14 días para calificar (1–5 + criterios + comentario).
- **Doble ciego**: ninguna reseña se publica hasta que ambos califiquen o venza el plazo.
- Criterios alquiler: estado de la herramienta, puntualidad, comunicación / cuidado, puntualidad (cliente).
- Criterios servicio: calidad, puntualidad, limpieza, precio justo / trato, claridad (cliente).
- Solo transacciones completadas pueden reseñarse. Derecho a respuesta pública del reseñado.
