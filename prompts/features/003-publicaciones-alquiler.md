# 003 — Publicaciones de herramientas en alquiler

## SPECIFY
```
/speckit.specify Los arrendadores (personas o negocios) publican herramientas con toda la información necesaria para alquilar sin ambigüedad (flujo A1 en docs/02).
Historias: (P1) Como arrendador quiero publicar una herramienta con categoría, atributos, fotos, accesorios incluidos, valor de reposición y precios por hora/día/fin de semana/semana/mes. (P1) Quiero que el sistema me sugiera una garantía según categoría y valor, y poder ajustarla dentro de un rango. (P1) Quiero definir recojo (zona pública, dirección exacta privada) y/o delivery con tarifa. (P1) Quiero elegir modo de reserva (por solicitud o inmediata), política de cancelación (Flexible/Moderada/Estricta), nivel mínimo de verificación del arrendatario, duración mínima y máxima y antelación mínima. (P1) Quiero bloquear fechas en un calendario. (P1) Quiero pausar, editar y archivar mis publicaciones. (P2) Como negocio quiero publicar varias unidades del mismo modelo (inventario). (P1) Como moderador quiero revisar primeras publicaciones y de categorías de riesgo.
Reglas: mínimo 3 fotos; la foto del número de serie es privada; editar precios no afecta reservas existentes.
```

## Preguntas guía para /speckit.clarify
- ¿El inventario multi-unidad entra en el MVP o se modela 1 publicación = 1 unidad?
- ¿Rangos permitidos de garantía por categoría?
- ¿El arrendador persona puede ofrecer delivery?

## Decisiones de clarify (2026-09-28)
| Pregunta | Decisión |
|---|---|
| Quién publica en el piloto | **Correo verificado + perfil de arrendador.** Activar el rol `lender` pide celular de contacto (privado, solo se revela tras confirmar una reserva), distrito y aceptar las condiciones de arrendador. Las primeras publicaciones pasan por moderación. Con la feature 021 (DNI) se sube la exigencia. |
| Inventario multi-unidad | **1 publicación = 1 unidad.** Un negocio con varias unidades iguales usa «Duplicar publicación». El modelo deja espacio para inventario después. |
| Garantía sugerida y rango | **% del valor de reposición según el riesgo de la categoría:** 20 % (bajo), 30 % (medio), 50 % (alto), redondeada a S/ 10. El arrendador la ajusta entre 50 % y 150 % de la sugerida. Los porcentajes viven en `platform_settings` (por ciudad y categoría, con historial). |
| Delivery | **Personas y negocios.** Recojo, delivery o ambos (al menos uno). Delivery con tarifa fija y los distritos que cubre. |

## Decisiones técnicas
| Tema | Decisión |
|---|---|
| Dinero | Céntimos enteros en PEN (constitución IV): precios, tarifa de delivery, valor de reposición y garantía. |
| Estados | `BORRADOR → EN_REVISION → PUBLICADA ⇄ PAUSADA → ARCHIVADA`; `EN_REVISION → RECHAZADA` (con motivo; se corrige y vuelve a revisión). Revisión si es la primera publicación del arrendador o la categoría es de riesgo alto. Cambios con `version` (bloqueo optimista) y `audit_log`. |
| Precios | Por día obligatorio; hora, fin de semana, semana y mes opcionales. Editar precios no toca reservas: la reserva guarda su `price_snapshot` (006). |
| Ubicación | Punto exacto privado (`pickup_location`) y punto público desplazado de forma determinista (HMAC del id de la publicación) dentro de `listings.public_radius_m` (por defecto 500 m). La zona sale de `zone_at`. |
| Fotos | Subida directa del navegador a S3 con URL prefirmada (MinIO en local, Cloudflare R2 en producción). Un job de asynq genera 3 tamaños (320, 800 y 1600 px) y quita los metadatos EXIF (ubicación). La foto de la placa o número de serie va en un prefijo privado y solo se sirve con URL firmada de corta duración al dueño y a soporte. |
| Disponibilidad | `availability_blocks` con `tstzrange` y `EXCLUDE USING gist (listing_id WITH =, period WITH &&)`: no hay dos bloqueos que se crucen. |
| Moderación | Cola para `moderator` y `admin` en el panel: aprobar o rechazar con motivo; queda en `audit_log`. |

## Plan de implementación
1. Migración y dominio: perfil de arrendador, publicaciones, fotos, bloqueos; reglas de precios, garantía, estados y ubicación pública (tests de tabla).
2. API: activar el perfil de arrendador; crear, editar, pausar, archivar y duplicar publicaciones; bloqueos de calendario; bloqueo de categorías prohibidas.
3. Fotos: MinIO en docker compose, URL prefirmada, job de procesamiento y foto privada de la placa.
4. Moderación: cola, aprobar y rechazar.
5. qatu-app: activar el perfil, «Mis publicaciones», asistente para publicar (atributos dinámicos del JSON Schema, fotos, precios y garantía, mapa con vista previa pública, reglas, calendario) y la cola de moderación. e2e con axe.

## PLAN (extra) — pegar después de prompts/plan-base.md
```
Tabla tool_listings con version; availability_blocks con period tstzrange y EXCLUDE USING gist (listing_id WITH =, period WITH &&); subida de fotos por URL prefirmada + job de procesamiento de imágenes (sharp, WebP, 3 tamaños).
```
