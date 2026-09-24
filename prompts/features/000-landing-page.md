# 000 — Landing page (página de inicio pública)

- **Fecha:** 2026-09-24
- **Estado:** borrador para `/speckit.specify`
- **Rama sugerida:** `000-landing-page`
- **Repos afectados:** `qatu-app` (principal), `qatu-api` (solo si se aprueba la lista de espera, ver clarify), `qatu-docs` (este spec)
- **Depende de:** ninguna feature. Es la primera pantalla pública y se puede lanzar antes que 001–005.
- **Fuente de negocio:** docs/01-producto.md, docs/05-confianza-y-legal.md

## SPECIFY
```
/speckit.specify Qatu necesita una página de inicio pública que explique en segundos qué es y por qué confiar: el lugar de Ayacucho para alquilar la herramienta que necesitas y contratar al técnico que te la resuelve (ver docs/01). Debe convertir visitantes en usuarios y prepararse para el lanzamiento piloto en Ayacucho (Perú), en español es-PE, moneda PEN.

Historias:
(P1) Como visitante quiero entender en menos de 5 segundos qué ofrece Qatu (alquiler de herramientas y servicios de oficios) y en qué ciudad opera.
(P1) Como visitante quiero ver las dos verticales (alquilar herramientas / contratar un oficio) y entrar a cualquiera con un botón claro.
(P1) Como visitante quiero ver las categorías y oficios principales (construcción, carpintería, jardín, limpieza, pintura; gasfitería, electricidad, cerrajería, etc.) para saber si Qatu tiene lo que busco.
(P1) Como visitante quiero saber cómo funciona en 3 a 4 pasos, tanto para alquilar como para contratar un servicio.
(P1) Como visitante desde el celular con conexión lenta quiero que la página cargue rápido y se lea bien en pantalla pequeña.
(P1) Como visitante quiero ver señales de confianza: cuentas verificadas, garantía, reseñas verificadas y que Qatu es intermediario (sin exagerar: la verificación es un filtro, no una garantía).
(P1) Como visitante quiero ir a iniciar sesión o registrarme desde la cabecera y el llamado a la acción principal.
(P1) Como visitante quiero encontrar en el pie de página los términos y condiciones, la política de privacidad y el enlace al Libro de Reclamaciones.
(P1) Como visitante quiero que, si ya tengo sesión, la página me lleve a mi panel en lugar de mostrarme el registro.
(P2) Como visitante quiero buscar por texto o elegir una categoría desde el inicio (redirige a la búsqueda, feature 005, cuando exista).
(P2) Como persona con una herramienta ociosa o un oficio quiero ver una sección "Ofrece en Qatu" con los beneficios de publicar.
(P2) Como visitante interesado quiero dejar mi correo o celular en una lista de espera mientras el servicio no esté abierto [NEEDS CLARIFICATION].
(P2) Como equipo de Qatu quiero medir visitas y clics en los llamados a la acción.
(P2) Como visitante quiero preguntas frecuentes (garantía, comisiones, cómo se paga, qué pasa si algo sale mal).

Reglas: no se muestran precios, disponibilidad, reseñas ni cifras que no existan (nunca datos inventados; si no hay datos reales, se omite la sección); los textos de comisiones y garantía deben coincidir con docs/01 y docs/03; sin cookies de seguimiento sin consentimiento (Ley 29733); accesibilidad WCAG 2.1 AA; la landing es pública e indexable (SEO) y no requiere sesión; Qatu se presenta como intermediario y no como empleador de proveedores; no se ofrece compraventa, inmuebles ni vehículos (docs/01, fuera de alcance).
```

## Preguntas guía para /speckit.clarify
- ¿Se lanza primero solo con lista de espera o ya con registro/login? (define si hay endpoint en qatu-api)
- ¿La lista de espera captura correo, celular o ambos? ¿Con qué consentimiento y para qué finalidad exacta?
- ¿Hay marca y paleta definidas (logo, colores, tipografía) o se proponen?
- ¿Se muestran categorías fijas o las del catálogo (feature 002)? Para el MVP, ¿lista estática?
- ¿Qué herramienta de analítica se usa (propia, Plausible, GA4)? ¿Hay banner de cookies?
- ¿Se muestra ya el enlace real al Libro de Reclamaciones o un aviso "próximamente"?
- ¿Se conserva el sitio WordPress actual como redirección o se reemplaza por completo?
- ¿Dominio final y URL canónica?
- ¿Se dispone de fotos e ilustraciones propias o se usan de stock libre de derechos?

## PLAN (extra) — pegar después de prompts/plan-base.md
```
ARQUITECTURA

qatu-app (Next.js 16, App Router):
- Ruta: app/(public)/page.tsx, renderizada estática (SSG) con revalidación ISR opcional; sin llamadas al backend en tiempo de render en el MVP. Metadatos, Open Graph, sitemap.ts, robots.ts y JSON-LD (Organization, WebSite) para SEO.
- Feature-sliced: features/public/landing/{components,lib}. Componentes de sección independientes: hero, verticals (alquilar / contratar), categories, how-it-works, trust, offer-cta (publicar), faq, footer-legal. Contenido textual y de categorías en features/public/landing/lib/content.ts (datos tipados, es-PE), sin hardcodear texto dentro de los componentes.
- Layout compartido: components/layout/{site-header,site-footer}. Componentes de UI con shadcn (button, accordion, card, input).
- proxy.ts: si existe cookie de sesión válida en "/" redirige a /dashboard (regla "solo invitado" ya definida); la landing no se cachea para usuarios con sesión.
- Enlaces de CTA a /auth/signin y /auth/... (rutas ya creadas); /terminos, /privacidad y /libro-de-reclamaciones bajo app/(public) en features/public/legal.
- Lista de espera (solo si se aprueba en clarify): formulario en features/public/landing/components/waitlist-form.tsx con Server Action o route handler app/api/waitlist/route.ts (BFF) que reenvía a POST /api/v1/waitlist; validación en cliente y servidor; Turnstile.
- Imágenes con next/image en WebP/AVIF con tamaños responsivos; fuente con next/font; JS inicial < 200 KB; LCP < 2,5 s en 4G lento (constitución XI). Los únicos componentes cliente son el acordeón de FAQ, el menú móvil y el formulario.
- Analítica: wrapper en components/provider/analytics-provider.tsx que solo carga tras consentimiento.

qatu-api (Go, solo si hay lista de espera):
- Entidad domain.WaitlistEntry; port.WaitlistRepository; service.WaitlistService (validar, normalizar celular peruano/correo, deduplicar, registrar consentimiento y fecha); handler/waitlist_handler.go; postgres/waitlist_repository.go; ruta pública POST /api/v1/waitlist en router.go; rate limiting por IP con Redis; verificación Turnstile; respuesta idéntica exista o no el registro (sin enumeración).
- Migración: migrations/0001_init.up.sql/.down.sql con tabla waitlist_entries (id, contact, contact_type, city_id, consent_at, consent_text_version, source, created_at) y UNIQUE sobre contacto normalizado. Si no hay lista de espera, 0001_init queda para la feature 001.
- Sin lista de espera no se toca qatu-api en esta feature.

CONTRATOS
- POST /api/v1/waitlist  body {contact, contactType: "email"|"phone", consent: true, source?, turnstileToken}  -> 202 {status:"ok"} | 400 validación | 429 rate limit. Documentado en la colección Bruno de qatu-docs (bruno/Waitlist/).

PARÁMETROS (platform_settings, por ciudad): ciudad activa del piloto, mensajes de estado (lista de espera o registro abierto) y feature flag "registration_open" para alternar el CTA principal.

PRUEBAS
- Unitarias (bun test): lib/content.ts (todas las categorías con slug único y etiquetas es-PE), validadores del formulario.
- Componentes: cada sección renderiza y el CTA apunta a la ruta correcta; el acordeón de FAQ se opera con teclado.
- E2E (Playwright, viewports 360x640 y 1280x800): carga de "/", CTAs navegan a signin/registro, enlaces de pie de página y Libro de Reclamaciones existen y responden 200, usuario con cookie de sesión es redirigido a /dashboard, envío de lista de espera (éxito, correo inválido, sin consentimiento, rate limit).
- Accesibilidad: axe-core sin violaciones serias en Playwright; contraste AA; navegación por teclado; foco visible.
- Rendimiento: Lighthouse CI en móvil con umbrales Performance ≥ 90, Accesibilidad ≥ 95, SEO ≥ 95; LCP < 2,5 s, JS inicial < 200 KB.
- SEO: metadatos, sitemap.xml, robots.txt y JSON-LD válidos.
- Backend (si aplica): unitarios de WaitlistService (normalización, duplicado, consentimiento), integración con Postgres y Redis reales (Testcontainers) para POST /api/v1/waitlist, prueba de rate limiting.

DEFINICIÓN DE TERMINADO
Tests y lint en verde, umbrales de Lighthouse cumplidos, textos revisados contra docs/01 y docs/05, sin datos inventados, .env.example actualizado, migración reversible (si aplica), colección Bruno y sección "Estado de implementación" actualizadas en qatu-docs.
```

## Guía para /speckit.tasks
Ordenar así: 1) contenido y diseño (content.ts, tokens de marca), 2) layout y componentes de sección, 3) SEO y legal, 4) integración de sesión (proxy.ts), 5) lista de espera (api + BFF + formulario), 6) analítica con consentimiento, 7) pruebas e2e, accesibilidad y Lighthouse, 8) Dockerfile/gateway y despliegue. Marcar en paralelo `[P]` las secciones independientes.
