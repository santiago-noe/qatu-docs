# 001 — Cuentas, roles e identidad

## SPECIFY
```
/speckit.specify Qatu necesita cuentas para que clientes, arrendadores y proveedores de servicios operen con confianza (ver docs/01 y docs/05). Un usuario se registra e inicia sesión con su número de celular peruano mediante un código OTP (email opcional). Una misma cuenta puede tener varios roles: cliente (por defecto), arrendador y proveedor de servicio; activar un rol de oferta pide completar datos adicionales. Existen roles internos: soporte, moderador y admin.
Historias: (P1) Como visitante quiero registrarme con mi celular en menos de un minuto. (P1) Como usuario quiero editar mi perfil (nombre, foto, ciudad, zona) y ver mi nivel de verificación. (P1) Como usuario quiero subir mi DNI y una selfie para alcanzar el nivel 1 y ver el estado de la revisión. (P2) Como proveedor quiero subir mi certificado de antecedentes y certificados de oficio para obtener la insignia de proveedor verificado. (P2) Como negocio quiero registrar mi RUC para tener perfil de negocio. (P1) Como moderador quiero una cola de verificaciones para aprobar o rechazar con motivo. (P2) Como usuario quiero cerrar sesión en todos mis dispositivos y solicitar la eliminación o exportación de mis datos.
Reglas: niveles 0, 1, 2, P y N según docs/05; consentimiento expreso y separado para biometría; los documentos son privados; límites de intentos de OTP; cuenta suspendida no puede transaccionar pero sí consultar su historial.
```

## Preguntas guía para /speckit.clarify
- ¿Canal de OTP: SMS, WhatsApp o ambos con fallback?
- ¿La verificación de DNI será manual al inicio o con proveedor automático?
- ¿Plazo de conservación de imágenes de DNI/selfie tras aprobar?
- ¿Edad mínima para registrarse (18)?

## PLAN (extra) — pegar después de prompts/plan-base.md
```
Incluye tabla users, user_roles, identity_verifications, sessions/refresh_tokens, consents; guards de rol y de nivel de verificación reutilizables por otros módulos; interfaz OtpSender con adaptador de consola para desarrollo.
```
