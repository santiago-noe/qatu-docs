# 05 — Confianza, seguridad y marco legal

## Niveles de verificación
| Nivel | Requisitos | Habilita |
|---|---|---|
| 0 | Teléfono verificado por OTP | Navegar, chatear, guardar favoritos |
| 1 | DNI (foto anverso/reverso) + selfie con prueba de vida; validación de datos de DNI | Alquilar herramientas de riesgo bajo/medio, contratar servicios |
| 2 | Nivel 1 + historial (≥ 3 transacciones sin incidencias) o comprobante de domicilio | Alquilar herramientas de riesgo alto, reserva inmediata |
| P (proveedor) | Nivel 1 + certificado de antecedentes (revisión manual) + referencias o certificados de oficio (ej. SENCICO) opcionales → insignia | Ofrecer servicios en el hogar |
| N (negocio) | RUC activo | Perfil de negocio, factura |
La verificación es un filtro, no una garantía: se comunica así en la UI y en los términos.

## Antidesintermediación (evitar que se vayan por fuera)
- Teléfono y dirección exacta ocultos hasta confirmar la transacción.
- Detección de números, correos y "yapéame al…" en el chat antes de la confirmación → aviso, no bloqueo duro.
- Valor por quedarse: registro de evidencias, mediación en disputas, reseñas verificadas, garantía
  de trabajo, historial y recordatorios. Sin esto, la comisión se percibe como impuesto.

## Ubicación y privacidad en el mapa
- El mapa público muestra solo una **zona aproximada** (círculo) y el distrito o barrio; nunca el punto exacto ni la
  dirección (docs/03, "Ubicación y mapa").
- La dirección exacta y el teléfono se revelan únicamente tras confirmar la transacción, y su acceso queda en `audit_log`.
- La ubicación es dato personal: se recoge solo la necesaria, con consentimiento por finalidad, y no se usa para rastrear
  al usuario. Si el usuario deniega su ubicación, la búsqueda funciona con el distrito elegido.

## Garantías y custodia
- **MVP (`RECORDED_OFFLINE`)**: Qatu registra el monto, las condiciones, el estado y las evidencias (fotos y checklist de
  entrega y devolución) y actúa como registro y mediador. **No custodia el dinero**: se entrega y se devuelve entre las partes.
- **Fase avanzada**: pre-autorización en tarjeta o custodia mediante un proveedor autorizado, solo tras revisión legal,
  contractual, financiera y técnica.
- La evidencia manda: una evaluación automática de daños (visión por computadora, fase IA) **nunca es la única base**
  para aplicar una garantía; toda decisión considera las evidencias y las condiciones acordadas.
- La comisión no se calcula sobre la garantía, ni el dinero de la garantía se mezcla con el del alquiler o el servicio.

## Seguridad física
- Recomendación de entregas en lugares públicos o en local del negocio.
- Botón "Compartir mi trabajo en curso" (proveedor en casa del cliente) con un contacto de confianza.
- Reporte de seguridad con prioridad máxima y bloqueo preventivo.

## Moderación
- Categorías y herramientas prohibidas (armas, pirotecnia, equipos que requieran licencia no acreditada).
- Revisión de primeras publicaciones y de reportes de usuarios.
- Detección de duplicados y fotos robadas (fase IA).
- **Verticales de alto riesgo** (inmuebles y vehículos, fase 4 de docs/01): no se habilitan sin revisión legal previa
  (contratos de arrendamiento, seguros, responsabilidad) y con verificación reforzada de oferentes y usuarios.
- Registro de auditoría y señales de fraude (cuentas nuevas con muchas solicitudes, fotos repetidas, cambios de contacto).

## Marco legal peruano (validar con abogado antes del lanzamiento)
- **Ley 29733 y su reglamento D.S. 016-2024-JUS (vigente desde marzo 2025)**: consentimiento
  informado por finalidad, política de privacidad, derechos ARCO y portabilidad, registro del banco de
  datos, notificación de incidentes de seguridad en plazo corto (48 h). **La biometría (selfie de
  verificación) es dato sensible → consentimiento expreso separado**. Minimizar: guardar solo el resultado
  de la verificación cuando sea posible.
- **Código de Protección y Defensa del Consumidor (Ley 29571)**: información clara del precio total
  antes de pagar, condiciones de cancelación visibles.
- **Libro de Reclamaciones virtual** (INDECOPI): enlace visible, correlativo, copia al consumidor,
  respuesta dentro del plazo legal, conservación de registros.
- **SUNAT**: Qatu emite comprobante electrónico por su comisión/tarifa de servicio. Los oferentes son
  responsables de sus propias obligaciones tributarias.
- **Términos y condiciones**: Qatu es intermediario; los proveedores son independientes; reglas de
  garantía, disputas, cancelación y conductas prohibidas.
- **Pagos**: no custodiar fondos de terceros sin estructura autorizada (usar split del PSP o registro offline).
- **Ley 31814 (IA)**: transparencia cuando la IA recomienda o genera contenido.
- **Marca**: búsqueda de antecedentes del nombre «Qatu» en INDECOPI y verificación del dominio antes del lanzamiento.
- **Compras de productos (fase 3)**: derecho de retracto y devoluciones según el Código de Protección y Defensa del Consumidor,
  y comprobantes por la venta cuando corresponda; se revisa antes de activar el vertical.
- **Inmuebles y vehículos (fase 4)**: requieren asesoría legal específica antes de su diseño.
