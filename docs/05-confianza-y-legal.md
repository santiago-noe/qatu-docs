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

## Seguridad física
- Recomendación de entregas en lugares públicos o en local del negocio.
- Botón "Compartir mi trabajo en curso" (proveedor en casa del cliente) con un contacto de confianza.
- Reporte de seguridad con prioridad máxima y bloqueo preventivo.

## Moderación
- Categorías y herramientas prohibidas (armas, pirotecnia, equipos que requieran licencia no acreditada).
- Revisión de primeras publicaciones y de reportes de usuarios.
- Detección de duplicados y fotos robadas (fase IA).

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
