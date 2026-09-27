# 000 — Landing: dirección visual "Catálogo"

- **Complementa a:** `000-landing-page.md` (spec funcional). Este archivo **reemplaza la sección "DISEÑO / Sistema visual"** del spec 000. La funcionalidad del spec (búsqueda, categorías, confianza, cómo funciona, Ofrece en Qatu, FAQ, pie legal, sesión) **se mantiene**.
- **Reemplaza a:** la dirección "Ficha técnica" (propuesta del 2026-09-26, disponible en el historial de git). Motivo: se prefiere un lenguaje de tienda en línea, más familiar para el usuario de Huamanga y menos dependiente de fotografía de estudio.
- **Referencia estética:** plantilla de e-commerce de moda (captura compartida el 2026-09-26): cabecera blanca con buscador, barra negra de aviso, banner gris con foto sobre un círculo de color y botón negro rectangular, franja de beneficios con íconos de línea, cuadrícula "Nuestros productos" con pestañas, carrusel "También te puede interesar", bloque negro de suscripción y pie blanco en columnas. Se toma la **estructura y el lenguaje visual**, no sus textos, marcas ni fotos.
- **Fecha:** 2026-09-26
- **Estado:** propuesta pendiente de aprobación.

---

## 1. Concepto
**"Qatu, la vitrina de herramientas y oficios de Huamanga."**
Ordenado como una tienda: blanco, negro y gris claro, tarjetas con la foto del objeto sobre fondo gris, títulos centrados con un antetítulo pequeño, y un único color de apoyo en círculos y detalles. Se ve familiar (como las tiendas en línea que ya usa la gente) y deja el protagonismo a las herramientas.

Tres rasgos que definen el estilo:
1. **Blanco y negro como base.** Botones principales negros y rectangulares; texto negro; fondos grises muy claros para agrupar.
2. **Tarjetas de catálogo.** Foto del objeto sobre gris claro, nombre centrado debajo y una etiqueta pequeña en la esquina superior derecha.
3. **Secciones con antetítulo + título centrado** ("Explora por categoría" / "Nuestras categorías").

---

## 2. Adaptaciones obligatorias a las reglas de Qatu
La referencia vende productos con precios, descuentos y calificaciones. Qatu está en piloto y **no muestra datos que no existan** (spec 000). Por eso:

| En la referencia | En Qatu |
|---|---|
| Barra negra "Descuento + envío gratis" | Barra negra con información real: "Piloto en Huamanga, Ayacucho · Registrarte es gratis". Nunca descuentos ni promociones inventadas. |
| Etiqueta de precio "₹129 Onwards" en la tarjeta | Etiqueta del tipo ("Alquiler" / "Servicio"). Cuando existan publicaciones reales (003/005), podrá mostrar "Desde S/ X" calculado de datos reales. |
| Beneficios: envío gratis, devoluciones, soporte 24/7, pagos flexibles | Beneficios reales de Qatu: **Cuentas verificadas**, **Entrega registrada**, **Garantía clara**, **Precios en soles**. Sin "24/7" ni promesas que no se cumplen. |
| "Recommended / You May Also Like" con estrellas y precios | En el MVP: carrusel **"Oficios para tu hogar"** con tarjetas de oficio, sin estrellas ni precios. Las estrellas solo aparecen con reseñas reales (feature 014) y la sección se renombra a "Recomendado" solo cuando haya datos. |
| "Subscribe to our emails" | Bloque negro **"Entérate cuando abramos en tu distrito"**. Solo se activa si se aprueba la lista de espera en clarify (correo, casilla de consentimiento, Ley 29733). Mientras tanto, el bloque invita a **crear una cuenta** con un botón blanco. |
| Íconos de favoritos y carrito en la cabecera | Sin carrito (no hay compra en el piloto). Favoritos se agrega con la feature 005. |
| Moda, marcas, fotos de modelos | Herramientas y técnicos reales de Huamanga; nunca fotos de stock. |

---

## 3. Tokens

### Color
| Token | Valor | Uso |
|---|---|---|
| `--bg` | `#FFFFFF` | Fondo general |
| `--bg-soft` | `#F5F5F5` | Banner del hero, franja de beneficios, fondo de las fotos de tarjeta |
| `--ink` | `#111111` | Texto principal, botones principales, barra de aviso, bloque de suscripción |
| `--ink-2` | `#555555` | Texto secundario (7,5:1 sobre blanco) |
| `--ink-3` | `#6B6B6B` | Antetítulos y textos pequeños (5,3:1 sobre blanco) |
| `--line` | `#E5E5E5` | Bordes y divisores de 1 px |
| `--accent` | `#C2410C` | Terracota de Qatu: pestaña activa, foco, enlaces destacados, detalles pequeños |
| `--accent-soft` | `#F6D9CC` | Círculo detrás de la foto del hero y fondos decorativos |
| `--ok` | `#15803D` | Verificado / éxito |

Reglas: los **botones principales son negros**, no naranjas; el terracota solo marca estado activo, foco y detalles, y el tono suave solo aparece en círculos decorativos detrás de las fotos.

### Tipografía
- **Geist** (ya cargada) para todo. Se abandona Geist Mono.
- H1 del banner: 32 → 48 px, peso 600, en MAYÚSCULAS, tracking 0.01em.
- Subtítulo del banner: 20 → 28 px, peso 400.
- H2 de sección: 26 → 36 px, peso 600, centrado.
- Antetítulo de sección: 13 → 14 px, peso 400, `--ink-3`, centrado, encima del H2.
- Cuerpo: 15 → 16 px, peso 400, interlineado 1.5.
- Tarjetas: nombre 14 → 15 px peso 500 centrado; etiqueta de esquina 11 → 12 px.

### Forma y espaciado
- Radios pequeños: botones **0–4 px** (rectangulares, como la referencia), tarjetas 4 px, banner 4 px.
- Separación entre secciones: 64 px móvil, 96 px escritorio. Contenedor máx. 1200 px, margen lateral 16/48 px.
- Sin sombras en tarjetas; se separan por el fondo gris de la foto. Sombra suave solo en tarjetas del carrusel al pasar el cursor.

### Íconos
`lucide-react`, trazo 1.5, negro, 24 px en la franja de beneficios, 20 px en la cabecera.

---

## 4. Estructura de la página

| # | Sección | Descripción |
|---|---|---|
| 1 | **Cabecera** | Blanca, fija. Logo a la izquierda; enlaces "Alquilar", "Contratar", "Cómo funciona", "Ofrece en Qatu"; **buscador compacto** en el centro ("¿Qué necesitas?") que envía a `/buscar`; a la derecha "Iniciar sesión / Registrarme". Móvil: logo, ícono de búsqueda y menú. |
| 2 | **Barra de aviso** | Franja negra de 40 px, texto blanco centrado 14 px: "Piloto en Huamanga, Ayacucho · Registrarte es gratis". |
| 3 | **Banner (hero)** | Panel `--bg-soft` a lo ancho del contenedor. Izquierda: H1 en mayúsculas, subtítulo y **botón negro** rectangular. Derecha: foto recortada sobre un **círculo** `--accent-soft`. Carrusel de 3 diapositivas con indicadores en forma de líneas bajo el panel: (1) "ALQUILA HERRAMIENTAS · Paga solo los días que usas · Ver herramientas", (2) "CONTRATA UN TÉCNICO · Precio claro antes de confirmar · Buscar técnicos", (3) "OFRECE EN QATU · Gana con tu herramienta o tu oficio · Empezar". Avance manual (flechas y líneas) y automático cada 6 s que se pausa al pasar el cursor, al enfocar y con `prefers-reduced-motion`. |
| 4 | **Panel de búsqueda completo** | Debajo del banner: pestañas "Alquilar herramientas / Contratar servicios" y campos ¿Qué necesitas?, Dónde (distrito, Select de shadcn) y Cuándo (calendario de shadcn cargado bajo demanda), con botón negro "Buscar". Misma función del spec 000. |
| 5 | **Franja de beneficios** | Fondo `--bg-soft`, 4 columnas (2×2 en móvil). Ícono de línea + título 15 px peso 600 + descripción 13 px: Cuentas verificadas · Entrega registrada · Garantía clara · Precios en soles. |
| 6 | **Nuestras categorías** | Antetítulo "Explora por categoría", H2 "Nuestras categorías", pestañas de texto "Herramientas / Oficios" (activa en negro con subrayado). Cuadrícula de 4 columnas en escritorio, 2 en móvil. Tarjeta: foto del objeto sobre `--bg-soft` (proporción 4:5), etiqueta en la esquina superior derecha y nombre centrado debajo. Cada tarjeta lleva a `/buscar` con la categoría. |
| 7 | **Oficios para tu hogar** | Antetítulo "Servicios", H2 con flechas ‹ › a la derecha. Carrusel horizontal de tarjetas: fondo `--bg-soft`, nombre del oficio, descripción corta, botón negro pequeño "Ver técnicos" y el ícono o foto a la derecha. Sin estrellas ni precios. |
| 8 | **Cómo funciona** | Dos columnas (Alquilar / Contratar) con pasos numerados 1-2-3 en círculos negros. |
| 9 | **Confianza** | Texto breve y la aclaración "La verificación es un filtro, no una garantía". |
| 10 | **Preguntas frecuentes** | Acordeón con divisores de 1 px. |
| 11 | **Bloque negro** | Fondo `--ink`, texto blanco centrado. Con lista de espera aprobada: título "Entérate cuando abramos en tu distrito", campo de correo + botón blanco "Avisarme" y casilla de consentimiento. Sin lista de espera: "Crea tu cuenta gratis" + botón blanco "Registrarme". Decoración: dos arcos gruesos en gris oscuro en las esquinas, como la referencia. |
| 12 | **Pie blanco** | Logo y 4 columnas: **Qatu** (Alquilar, Contratar, Cómo funciona), **Nosotros** («Qatu» significa mercado en quechua, Ofrece en Qatu), **Ayuda y políticas** (Preguntas frecuentes, Términos, Privacidad, **Libro de Reclamaciones**), **Síguenos** (solo redes que existan). Línea final con © año. |

Above the fold: en 360×640 deben verse cabecera, barra de aviso, banner con título y botón. En 1280×800, cabecera, barra, banner completo e inicio del panel de búsqueda.

---

## 5. Fotografía
- Fotos propias: herramientas recortadas (fondo transparente o gris `#F5F5F5`) para las tarjetas y el banner; técnicos reales con permiso firmado para el carrusel de oficios.
- Formato AVIF/WebP con `next/image`; la foto de la primera diapositiva con `priority` (es el LCP).
- **Mientras no haya fotos:** marcador `--bg-soft` con borde discontinuo, ícono de cámara y la leyenda de la toma que falta (ej. "FOTO 03 · Hidrolavadora recortada 4:5"). En tarjetas de oficio puede usarse el ícono de lucide grande en lugar de foto. Nunca stock.

Tomas mínimas: 3 fotos de banner (herramienta en uso, técnico trabajando, taller ordenado), 8 herramientas recortadas en el mismo ángulo, 6 técnicos o manos trabajando para los oficios.

---

## 6. Movimiento
- Banner: transición de diapositivas por desvanecimiento de 400 ms; indicador activo que se alarga.
- Carrusel de oficios: desplazamiento con `scroll-snap` y flechas; sin librerías de carrusel.
- Hover de tarjeta: la foto hace zoom 1.03 (200 ms).
- Todo desactivado con `prefers-reduced-motion: reduce`.
- `motion` solo si hace falta para el banner; si CSS alcanza, se retira la dependencia (se decide al implementar).

---

## 7. Qué NO hacer
- Precios, descuentos, "desde S/", estrellas, cantidades o testimonios que no salgan de datos reales.
- Carrito, envíos o devoluciones de productos (no hay compra en el piloto).
- Botones redondeados tipo píldora; más de un color de apoyo; degradados.
- Fotos de stock o de modelos; copiar textos, logos o fotos de la referencia.

---

## 8. Accesibilidad y rendimiento (sin cambios respecto al spec 000)
- Contraste AA en todo texto; foco visible con anillo `--accent` de 2 px.
- Carrusel accesible: botones con `aria-label`, región con `aria-roledescription="carrusel"`, pausa automática al enfocar y control de pausa visible.
- Objetivos táctiles ≥ 44 px.
- Lighthouse móvil: Performance ≥ 90, Accesibilidad ≥ 95, SEO ≥ 95; LCP < 2,5 s; JS inicial < 200 KB.

---

## 9. Checklist de aceptación visual
- [ ] Barra negra de aviso con texto real, sin promociones.
- [ ] Banner gris con foto (o marcador) sobre círculo terracota suave y botón negro rectangular; indicadores de línea.
- [ ] Franja de 4 beneficios con íconos de línea.
- [ ] Cuadrícula de categorías con pestañas, tarjetas sobre gris y etiqueta en la esquina (sin precio).
- [ ] Carrusel de oficios con flechas, sin estrellas ni precios.
- [ ] Bloque negro final y pie blanco en 4 columnas con Libro de Reclamaciones.
- [ ] Capturas en 360×640 y 1440×900 revisadas contra este documento.

---

## 10. Prompt para Claude Code
```
Lee el spec de la landing (funcionalidad) y su design.md (dirección visual "Catálogo"). design.md manda sobre lo estético; la funcionalidad del spec no cambia y sus reglas de datos (nada inventado) prevalecen sobre la referencia.

1. Implementa sección por sección en el orden de la tabla 4, cada una como componente en features/public/landing/components, con los textos en lib/content.ts.
2. Usa marcadores de foto según la sección 5 mientras no existan las imágenes.
3. Al terminar cada sección, revisa en 360x640 y 1440x900 contra el checklist de la sección 9.
4. No agregues dependencias nuevas; usa lucide-react y los componentes de shadcn ya instalados.
```
