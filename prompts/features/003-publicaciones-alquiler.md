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

## Estado de implementación (2026-09-29)
Leyenda: ✅ hecho · ⚠️ parcial · ❌ pendiente.

| Parte | qatu-api | qatu-app | Estado |
|---|---|---|---|
| 1. Migración y dominio | ✅ 0006 (perfil, publicaciones, fotos, delivery, calendario con `EXCLUDE`) y 0007 (categorías prohibidas); estados, garantía, punto público, calendario y duplicar con pruebas de tabla | — | ✅ |
| 2. API del arrendador | ✅ `GET/PUT /me/lender` (correo verificado, condiciones versionadas, rol `lender`); `/me/listings` crear, ver, guardar con `version`, enviar, pausar, reanudar, archivar y duplicar; garantía sugerida; calendario; atributos validados con el JSON Schema de la categoría al enviar | ❌ parte 5 | ✅ |
| 3. Fotos | ✅ `/me/listings/{id}/photos`: URL firmada (PUT directo al almacenamiento), confirmar, procesar en segundo plano (asynq), ordenar y quitar; 320, 800 y 1600 px en JPEG, enderezadas según EXIF y sin metadatos; placa en el bucket privado con URL firmada de 5 min | ❌ parte 5 | ✅ |
| 4. Moderación | ✅ `/moderation/listings` (moderator y admin, con segundo paso): cola, aprobar y rechazar con motivo, con `version`; aviso por correo al arrendador; limpieza cada hora de subidas de fotos abandonadas | ❌ parte 5 | ✅ |
| 5. qatu-app y e2e | — | ⚠️ tramos 1 y 2: perfil de arrendador, «Mis publicaciones» con sus acciones y el editor completo por secciones (la herramienta con sus atributos, fotos con portada y placa privada, precios y garantía sugerida, entrega con mapa MapLibre y vista previa del círculo público, delivery por distritos, reglas) con la lista de lo que falta y el envío; e2e con axe en móvil y escritorio. Faltan el calendario y la cola de moderación | ⚠️ |

Decisiones de la parte 2:
- **Permisos por perfil, no por sesión.** Los roles viajan en la sesión (Redis); activar el perfil de arrendador da el rol `lender` en la base, pero las rutas de publicaciones revisan el perfil, así la persona no tiene que volver a iniciar sesión.
- **Formulario completo con versión.** El asistente guarda todo en cada paso (`PUT` con `version`); si otra pestaña cambió la publicación, 409 `version_desactualizada`.
- **Borrador flexible, publicación estricta.** En borrador se guardan atributos incompletos; al enviar (y al editar una publicada) se exige el esquema de la categoría, la garantía en rango, recojo o delivery y 3 fotos listas.
- **Revisión.** Primera publicación del arrendador, categoría de riesgo alto o una rechazada que se corrige.
- **Ubicación.** El punto de recojo debe caer en la ciudad del perfil; el distrito sale de `zone_at`. El punto público se desplaza con `APP__SECURITY__LOCATION_SECRET` (obligatorio en producción).
- **Categoría fija tras publicar.** Para otra categoría se duplica.

Decisiones de la parte 3:
- **Almacenamiento: SeaweedFS en local.** MinIO dejó de publicar imágenes de Docker; SeaweedFS (fijado en 4.48) ofrece la misma API S3. Producción sigue siendo Cloudflare R2: el código usa solo lo que R2 admite (URL firmada de PUT y GET, CORS, bucket público), por eso no hay POST firmado.
- **Dos buckets.** `qatu-public` (fotos procesadas, se leen sin credenciales, caché de un año) y `qatu-private` (originales recién subidos y la placa). El original, que puede traer la ubicación GPS en su EXIF, se borra al procesarlo.
- **JPEG en vez de WebP.** Go no tiene codificador WebP sin C; JPEG calidad 82 pesa poco en 1600 px. Si hace falta WebP, se genera en el borde (transformaciones de Cloudflare) sin cambiar la API.
- **Procesamiento en Go** (sin sharp ni un servicio Node aparte): lee JPEG, PNG y WebP, rechaza imágenes de más de 50 megapíxeles antes de decodificarlas y aplana la transparencia sobre blanco.
- **Worker en el mismo proceso** (`APP__WORKER__ENABLED`); para escalar se corre el mismo binario solo como worker. El ID de la foto es el ID del trabajo: confirmar dos veces no la procesa dos veces.
- **Tamaño real.** La URL firmada de PUT no limita el tamaño: al confirmar se revisa el objeto (máximo 10 MB).

Decisiones de la parte 4:
- **Qué ve moderación.** Datos de la publicación, categoría (riesgo y esquema de atributos), nombre del arrendador y si es su primera publicación, y las fotos públicas. No ve el punto exacto de recojo, la foto de la placa ni datos de contacto (Ley 29733: lo mínimo necesario).
- **Nadie modera lo suyo**, aunque tenga el rol.
- **Bloqueo optimista** también aquí: si dos moderadores deciden la misma publicación, el segundo recibe 409.
- **Aviso por correo** al aprobar o rechazar (con el motivo). Es un aviso: si el correo falla, la decisión ya quedó guardada. La feature 013 (notificaciones) lo llevará a la app.
- **Subidas abandonadas.** Una tarea programada (asynq, cada hora, sin duplicarse entre instancias) borra las fotos pendientes de más de 2 horas y sus originales.

Correcciones encontradas en la parte 5:
- **Atributos heredados.** Los atributos (marca, modelo, potencia, voltaje, energía) están en la categoría raíz y las publicaciones van en el tipo. El esquema efectivo de un tipo es el de su raíz más el propio (el tipo agrega o redefine campos; `required` se une). Se usa al validar al enviar, en moderación y en el catálogo público, que es de donde la app arma el formulario.
- **Migración 0008.** Los datos completos se exigen solo en revisión, publicada y pausada: un borrador incompleto se puede archivar y una rechazada se corrige de a pocos (antes ambas daban error 500).
