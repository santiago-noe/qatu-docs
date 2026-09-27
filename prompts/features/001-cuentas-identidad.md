# 001 — Cuentas, acceso y roles

- **Fecha:** 2026-09-27 (reemplaza la versión con OTP como acceso principal)
- **Estado:** borrador para `/speckit.specify`
- **Rama sugerida:** `001-cuentas-acceso` (en `qatu-api`, `qatu-app` y `qatu-docs`)
- **Depende de:** nada. Es la base de todas las features transaccionales.
- **Fuente de negocio:** docs/01 (roles), docs/05 (niveles de verificación, Ley 29733), docs/04 (sesiones en Redis).

## Decisión de alcance
El piloto arranca con **correo y contraseña, y Google**. El acceso por **OTP al celular** llega después como un proveedor más, **sin cambiar la tabla de usuarios ni las sesiones**. Por eso esta feature separa la **cuenta** (la persona) de sus **identidades de acceso** (cómo entra).

| Feature | Contenido |
|---|---|
| **001 (esta)** | Registro e inicio con correo y contraseña, Google, verificación de correo, recuperación de contraseña, sesiones, perfil básico, roles y consentimientos |
| 021 verificación de identidad | DNI, selfie, antecedentes, RUC, cola de moderación y niveles 1, 2, P y N (antes dentro de 001) |
| 022 acceso por celular (OTP) | SMS o WhatsApp como nuevo proveedor de acceso; vincular celular a una cuenta existente |

## SPECIFY
```
/speckit.specify Qatu necesita cuentas para que clientes, arrendadores y proveedores de servicios operen con confianza (docs/01 y docs/05). En el piloto una persona se registra e inicia sesión con su correo y una contraseña, o con su cuenta de Google. Más adelante se agregará el acceso por código al celular, así que una misma cuenta debe poder tener varias formas de acceso (contraseña, Google y, en el futuro, celular) sin duplicarse.

Una cuenta puede tener varios roles: cliente (por defecto), arrendador y proveedor de servicio. Activar un rol de oferta pide completar datos adicionales en su propia feature. Existen roles internos: soporte, moderador y admin, que solo asigna un admin.

Historias:
(P1) Como visitante quiero registrarme con mi correo, mi nombre y una contraseña en menos de un minuto, aceptando los términos y la política de privacidad.
(P1) Como visitante quiero confirmar mi correo con un código de 6 dígitos que me llega por email, y poder pedir uno nuevo si no llegó.
(P1) Como visitante quiero registrarme o entrar con mi cuenta de Google en un solo paso.
(P1) Como usuario quiero iniciar sesión con correo y contraseña y seguir conectado en ese dispositivo.
(P1) Como usuario quiero recuperar mi contraseña con un enlace o código enviado a mi correo.
(P1) Como usuario quiero cerrar sesión en este dispositivo o en todos a la vez.
(P1) Como usuario quiero editar mi perfil básico (nombre, foto, ciudad y distrito) y ver mi nivel de verificación.
(P1) Como usuario que entró con Google quiero poder agregar una contraseña, y como usuario con contraseña quiero vincular Google.
(P1) Como admin quiero asignar o quitar roles internos y suspender cuentas con un motivo.
(P2) Como usuario quiero cambiar mi contraseña estando conectado.
(P2) Como usuario quiero ver mis sesiones activas y cerrar una en particular.
(P2) Como usuario quiero solicitar la eliminación o exportación de mis datos (derechos ARCO).
(P2) Como usuario quiero activar una verificación en dos pasos por correo.

Reglas:
- Una persona es una sola cuenta aunque tenga varias formas de acceso. Si alguien entra con Google usando un correo que ya tiene cuenta verificada, se vincula a esa cuenta; si la cuenta existente no verificó su correo, primero debe entrar con su contraseña (evita que otro se apropie de la cuenta).
- Nivel 0 de verificación = correo verificado (docs/05). El celular verificado se suma cuando exista el acceso por OTP.
- Sin correo verificado se puede navegar, pero no transaccionar.
- Contraseñas con longitud mínima y comparadas contra contraseñas filtradas conocidas [NEEDS CLARIFICATION: política exacta].
- Límite de intentos en inicio de sesión, envío de códigos y recuperación; los mensajes no revelan si un correo existe.
- Cuenta suspendida: no puede transaccionar, pero sí consultar su historial.
- Consentimiento separado y versionado para términos, privacidad y comunicaciones (Ley 29733).
- Cerrar sesión o cambiar la contraseña invalida las sesiones correspondientes de inmediato.
```

## Preguntas guía para /speckit.clarify
- ¿Edad mínima para registrarse (18)? ¿Cómo se declara?
- Política de contraseña: ¿mínimo 8, 10 o 12 caracteres? ¿Se verifica contra contraseñas filtradas?
- ¿Cuánto dura una sesión (7 o 30 días) y se renueva con el uso?
- ¿La recuperación de contraseña usa enlace o código de 6 dígitos?
- ¿Proveedor de correo transaccional (SMTP propio, Resend, SES) y remitente?
- ¿Google exige correo verificado por Google para vincular automáticamente?
- ¿La verificación en dos pasos entra en el piloto o se pospone?
- ¿Qué datos se borran y cuáles se conservan por obligación legal al eliminar una cuenta?

## PLAN (extra) — pegar después de prompts/plan-base.md
```
ESCALABILIDAD DEL ACCESO (requisito central)
- La cuenta y el acceso están separados. users guarda a la persona; auth_identities guarda cada forma de entrar. Agregar OTP (feature 022) = un nuevo valor de provider y un nuevo adaptador, sin tocar users, sessions ni los demás servicios.
- Estrategia por proveedor: port.AuthProvider con implementaciones password (argon2id), google (OAuth 2.0 con PKCE) y, en 022, phone_otp. Todas terminan en el mismo SessionService.CreateSession(user).
- Las sesiones no dependen del proveedor: se guardan en Redis con el user_id y los roles; el proveedor usado queda solo como dato de auditoría.

MODELO DE DATOS (migración 0002_accounts, reversible)
- users: id uuid PK, email citext UNIQUE NULL, email_verified_at, phone text UNIQUE NULL (E.164, se usa en 022), phone_verified_at, name, avatar_url, city_id NULL, zone_id NULL, status (active | suspended | deleted), suspended_reason, verification_level smallint DEFAULT 0, created_at, updated_at, version (bloqueo optimista). CHECK: email o phone no nulo.
- auth_identities: id uuid PK, user_id FK, provider text (password | google | phone_otp), provider_subject text (correo normalizado, sub de Google, celular E.164), secret_hash text NULL (solo password), created_at, last_used_at. UNIQUE (provider, provider_subject); UNIQUE (user_id, provider).
- user_roles: user_id, role (client | lender | provider | support | moderator | admin), granted_by, granted_at. PK (user_id, role).
- consents: id, user_id, purpose (terms | privacy | marketing), version, granted_at, revoked_at, ip.
- audit_log: se crea aquí si no existe (actor, action, entity, entity_id, before, after, at, ip): alta, verificación, cambio de contraseña, vinculación, cambios de rol y suspensiones.

REDIS
- session:{id} -> user_id, roles, provider, created_at, expires_at (TTL = duración de la sesión, renovable). user_sessions:{user_id} (set) para "cerrar todas".
- code:email_verify:{user_id} y code:password_reset:{hash} -> código hasheado, intentos, TTL corto.
- ratelimit:{acción}:{ip|usuario} con ventana deslizante.
- oauth_state:{state} -> verificador PKCE y destino, TTL 10 minutos.

qatu-api (Go, hexagonal)
- domain: User, AuthIdentity, Role, Consent, Session; reglas de estado de la cuenta y de vinculación.
- port: UserRepository, IdentityRepository, RoleRepository, ConsentRepository, AuditLog, PasswordHasher, SessionStore, CodeStore, RateLimiter, Mailer, OAuthProvider, CaptchaVerifier, Clock, IDGenerator.
- service: auth_service (registro y login con contraseña), oauth_service (Google), email_verification_service, password_reset_service, session_service, account_service (perfil, vinculación, ARCO), admin_user_service (roles, suspensión).
- adapter: argon2/, googleoauth/, smtp/ (plantillas de verificación y recuperación), turnstile/, redisclient/ (session_store, code_store, rate_limiter), postgres/ (*_repository.go), handler/auth_handler.go, handler/me_handler.go, handler/admin_user_handler.go, middleware/session_auth.go, middleware/require_role.go.
- Endpoints /api/v1: POST auth/register · POST auth/login · POST auth/logout · POST auth/logout-all · POST auth/email/verify · POST auth/email/resend · POST auth/password/forgot · POST auth/password/reset · GET auth/google/start · GET auth/google/callback · GET me · PATCH me · POST me/password · POST me/identities/google · GET me/sessions · DELETE me/sessions/{id} · admin: PATCH users/{id}/roles · PATCH users/{id}/status.
- Cookie de sesión httpOnly, Secure, SameSite=Lax; id opaco de 256 bits; rotación al iniciar sesión y al cambiar privilegios.

qatu-app (Next.js)
- features/auth/{signin,signup,verify-email,recovery-account,set-password,change-password}/{components,lib}; formularios con validación en cliente y servidor; Turnstile en registro y recuperación.
- BFF en app/api/auth/*: reenvía a qatu-api y fija la cookie de sesión en el dominio de la app; el navegador nunca ve el backend.
- proxy.ts ya protege /dashboard y redirige a invitados; se ajustan los nombres de cookie a los reales.

PARÁMETROS (platform_settings o configuración): duración de sesión, TTL y reintentos de códigos, límites por minuto, longitud mínima de contraseña.

PRUEBAS
- Dominio y servicios (go test con tablas): registro, correo duplicado, login correcto e incorrecto, límite de intentos, verificación de correo (código válido, vencido, agotado), recuperación, vinculación Google (correo verificado vs no verificado), suspensión, roles.
- Prueba de extensibilidad: un proveedor falso "fake_otp" crea sesión con el mismo SessionService sin cambios en users ni sessions.
- Integración con Postgres y Redis reales (Testcontainers): repositorios, unicidad de identidades, sesiones y cierre de todas.
- Seguridad: los mensajes de error no revelan si el correo existe; la cookie tiene los atributos correctos; cambiar contraseña invalida las demás sesiones.
- E2E (Playwright): registro, verificación de correo con un buzón de pruebas (Mailpit), inicio y cierre de sesión, recuperación, ruta protegida.

DEFINICIÓN DE TERMINADO
Tests en verde en ambos repos, migración 0002 reversible, OpenAPI y colección Bruno (bruno/Auth) actualizados, .env.example con las variables nuevas (SMTP, Google, Turnstile), y la sección "Estado de implementación" al día.
```

## Guía para /speckit.tasks
Orden: 1) migración 0002 y dominio, 2) ports y servicio de sesiones, 3) contraseña (registro, login, logout), 4) verificación de correo y Mailer, 5) recuperación, 6) Google, 7) perfil, roles y suspensión, 8) rate limiting y Turnstile, 9) BFF y pantallas de qatu-app, 10) pruebas de integración y e2e. Marcar `[P]` lo que sea independiente entre qatu-api y qatu-app una vez fijado el contrato.
