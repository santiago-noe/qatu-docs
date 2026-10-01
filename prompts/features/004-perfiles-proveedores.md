# 004 — Perfiles de proveedores de servicios

## SPECIFY
```
/speckit.specify Los proveedores de oficios necesitan un perfil que genere confianza y permita reservarlos o pedirles cotización (flujos B y C en docs/02).
Historias: (P1) Como proveedor quiero crear mi perfil con oficios, años de experiencia, descripción, fotos de trabajos anteriores (portafolio) y zonas de cobertura. (P1) Quiero definir tarifa por hora con mínimo de horas y/o paquetes de precio fijo por oficio. (P1) Quiero definir mi disponibilidad semanal y bloquear días. (P1) Quiero indicar los días de garantía de mi trabajo. (P2) Quiero indicar si acepto trabajos urgentes el mismo día. (P1) Como cliente quiero ver el perfil con insignias de verificación, reseñas, tasa de respuesta y trabajos completados.
Reglas: solo usuarios con nivel P pueden aparecer como proveedores para servicios en el hogar; perfil incompleto no aparece en búsquedas.
```

## Preguntas guía para /speckit.clarify
- ¿Un proveedor puede ser una empresa con varios técnicos (equipo) en el MVP?
- ¿Se permite 'precio a cotizar' sin tarifa publicada?

## Decisiones de clarify (2026-09-30)
| Pregunta | Decisión |
|---|---|
| Nivel P sin la feature 021 | **La primera aprobación de moderación es la revisión manual del nivel P.** El proveedor sube su certificado de antecedentes como archivo privado (tramo 2); el moderador lo revisa y, al aprobar, el perfil queda verificado (`verified_at`), publicado y con el rol `provider`. Cuando llegue la 021 esa revisión se mueve allí sin cambiar el perfil. |
| Empresa con varios técnicos | **No en el MVP.** Un perfil = una persona, con nombre comercial opcional. |
| Precio a cotizar sin tarifa | **Sí, por oficio.** Un oficio sin tarifa por hora ni paquetes es «a cotizar»: solo recibe solicitudes de cotización (008) y no admite reserva directa. |

## Decisiones técnicas
| Tema | Decisión |
|---|---|
| Activación | `POST /me/provider` con correo verificado y condiciones de proveedor (consentimiento `provider_terms` versionado). Celular privado hasta confirmar un trabajo. El perfil queda en borrador y se guarda incompleto. |
| Estados | El mismo ciclo de moderación que una publicación, sin archivar: `BORRADOR → EN_REVISION → PUBLICADO ⇄ PAUSADO`; `EN_REVISION → RECHAZADO` (con motivo). Hasta la primera aprobación siempre va a revisión. La base exige `verified_at` para estar publicado o pausado. Cambios con `version` y `audit_log` (sin el celular). |
| Completo para mostrarse | Descripción de 30+ caracteres, al menos un oficio vigente en la ciudad, un distrito de cobertura y una franja del horario. Publicado o pausado no se puede guardar incompleto. |
| Oficios y precios | Hasta 5 oficios (categorías raíz de la vertical `service`, activas en la ciudad). Tarifa por hora opcional con mínimo de 1 a 8 horas; hasta 10 paquetes por oficio con precio fijo y duración de 30 min a 8 h en medias horas. Céntimos enteros. Los paquetes conservan su ID al editar (un trabajo de la 009 apuntará a ellos y guardará su copia del precio). |
| Horario semanal | Franjas en la hora local de la ciudad, en medias horas (`"08:00"`–`"17:30"`, ISO 8601 para el día: 1 = lunes), hasta 3 por día y sin cruces (`EXCLUDE USING gist`). |
| Días bloqueados | `provider_blocks` con `tstzrange` y `EXCLUDE`, como el calendario de herramientas; `reason` `manual` o `job` (009). |
| Garantía y urgencias | Días de garantía del trabajo de 0 a 90 (15 por defecto, docs/02); acepta urgentes del mismo día (sí/no). |
| Métricas del perfil | Tasa de respuesta, trabajos completados y reseñas salen de la 008, 009 y 014. Hasta tener datos, el perfil muestra «Nuevo en Qatu»: nunca cifras inventadas. |

## Plan de implementación
1. API: perfil, oficios con tarifa y paquetes, cobertura, horario semanal y días bloqueados; estados y envío a revisión.
2. API: portafolio y certificado de antecedentes privado; moderación de perfiles (aprobar = nivel P y rol `provider`); perfil público de solo lectura.
3. qatu-app: «Ofrecer servicios» con el editor por secciones (datos, oficios y precios, cobertura en el mapa, horario y bloqueos, portafolio, garantía y urgencias, enviar).
4. qatu-app: perfil público y perfiles en la cola de moderación. e2e con axe.

## Estado de implementación (2026-09-30)
Leyenda: ✅ hecho · ⚠️ parcial · ❌ pendiente.

| Tramo | qatu-api | qatu-app | Estado |
|---|---|---|---|
| 1. Perfil y precios | ✅ migración 0009 (perfil, oficios, paquetes, cobertura, horario con `EXCLUDE`, bloqueos); `GET/POST/PUT /me/provider`, enviar, pausar, reanudar y días bloqueados; OpenAPI y Bruno | ❌ tramo 3 | ✅ |
| 2. Archivos y revisión | ❌ | ❌ tramo 4 | ❌ |
| 3. «Ofrecer servicios» | — | ❌ | ❌ |
| 4. Perfil público y moderación | — | ❌ | ❌ |

## PLAN (extra) — pegar después de prompts/plan-base.md
```
provider_profiles, provider_trades, service_packages, provider_coverage_zones, weekly_availability; métricas derivadas (tasa de respuesta, tiempo medio) calculadas por eventos.
```
