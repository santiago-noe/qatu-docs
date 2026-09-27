# 000 — Landing: dirección visual "Ficha técnica"

- **Complementa a:** `000-landing-page.md` (spec funcional). Este archivo **reemplaza la sección "Sistema visual"** del spec 000 y la referencia de Airbnb **solo en lo estético**. La estructura funcional (panel de búsqueda con pestañas, tira de categorías, filas de tarjetas, confianza, cómo funciona, Ofrece en Qatu, FAQ, pie legal) **se mantiene**.
- **Referencia estética:** landing del "Busy Status Bar" (Flipper Devices). Se toma el **lenguaje visual** (fotografía de producto en estudio, gris claro continuo, tipografía grotesca ligera, detalles monoespaciados, un solo acento naranja, diagramas con líneas guía, pie negro). **No** se copian su logo, fotos, textos ni composición exacta.
- **Ubicación:** vive junto al spec en `prompts/features/000-landing-page.design.md`. Al ejecutar `/speckit.specify` para la landing, copiarlo a `specs/<carpeta-de-la-feature>/design.md` para que Spec Kit lo lea con el spec.
- **Fecha:** 2026-09-26
- **Estado:** propuesta pendiente de aprobación; la landing actual de `qatu-app` sigue el sistema visual anterior (Airbnb) hasta que se apruebe.

---

## 1. Concepto

**"Qatu, catálogo técnico de herramientas y oficios."**
Las herramientas se presentan como producto industrial bien diseñado (estilo Braun / Teenage Engineering / Nothing): fondo gris de estudio, luz suave, sombras reales, textos cortos y precisos, etiquetas técnicas en monoespaciada. Transmite **orden, precisión y confianza**, que es lo que un marketplace de oficios necesita comunicar frente a la informalidad.

Tres rasgos que definen el estilo (si falta uno, ya no es este estilo):
1. **La fotografía manda.** Objetos reales aislados sobre gris continuo, con una mano humana interactuando en el hero.
2. **Tipografía sobria.** Grotesca en peso regular (no bold) para títulos grandes; monoespaciada pequeña para etiquetas y datos.
3. **Un solo acento naranja**, usado con avaricia: botón principal, estado activo y pequeños detalles.

---

## 2. Tokens

### Color
| Token | Valor | Uso |
|---|---|---|
| `--bg` | `#EFEFEE` | Fondo general (gris de estudio, continuo con las fotos) |
| `--bg-raised` | `#F6F6F5` | Secciones alternas, bandas |
| `--surface` | `#FFFFFF` | Tarjetas, panel de búsqueda |
| `--ink` | `#111111` | Títulos y texto principal |
| `--ink-2` | `#55524E` | Texto secundario (≥ 4.5:1 sobre `--bg`) |
| `--ink-3` | `#7A7671` | Solo etiquetas ≥ 14 px o decorativo; nunca texto largo |
| `--line` | `#D9D7D3` | Divisores de 1 px, bordes de tarjeta |
| `--accent` | `#C2410C` | Botón principal, foco, pestaña activa (blanco encima = 5,2:1, AA) |
| `--accent-hover` | `#9A3412` | Hover/pressed |
| `--accent-bright` | `#EA580C` | Solo detalles gráficos grandes (puntos, líneas de estado, íconos ≥ 24 px). Nunca con texto blanco encima |
| `--ok` | `#15803D` | Verificado / éxito, en íconos y etiquetas |
| `--footer` | `#0B0B0B` | Pie de página |
| `--footer-ink` | `#E7E5E2` / `#A19D97` | Texto del pie (principal / secundario) |

Regla: el naranja ocupa **menos del 5 %** de la superficie visible. Si una sección tiene dos elementos naranjas, sobra uno.

### Tipografía (dos familias, ambas en `next/font/google`, gratuitas)
- **Geist** (sans grotesca): títulos y texto.
- **Geist Mono**: etiquetas técnicas, datos, números de paso, rótulos de diagramas, texto de chips.

| Rol | Tamaño (móvil → escritorio) | Peso | Tracking | Ejemplo |
|---|---|---|---|---|
| Display (H1) | 40 → 72 px | 400 | -0.03em | "Alquila la herramienta. Contrata al técnico." |
| H2 sección | 32 → 56 px | 400 | -0.025em | "Así funciona" |
| H3 | 20 → 24 px | 500 | -0.01em | "Gasfitería" |
| Cuerpo | 16 → 17 px, interlineado 1.55 | 400 | 0 | Párrafos (máx. 60 caracteres por línea) |
| Pequeño | 13 → 14 px | 400 | 0 | Descripciones bajo íconos |
| Etiqueta mono | 12 → 13 px, MAYÚSCULAS | 500 | 0.06em | `DISTRITO`, `PASO 01`, `> VERIFICADO CON DNI` |

- **Nunca** títulos en bold 700–800. El peso bajo en tamaño grande es lo que da el aire premium.
- La palabra clave dentro de un párrafo puede ir en 600 (como "**Qatu** es…"), una vez por párrafo como máximo.

> Nota: esto enmienda la regla "una sola familia" del spec 000. Geist y Geist Mono son la misma superfamilia, así que se mantiene la coherencia.

### Espaciado, forma y profundidad
- Escala de 4 px. Separación entre secciones: 96 px móvil, 160 px escritorio. Contenedor máx. 1200 px con 24/48 px de margen lateral.
- **Radios:** botones y campos 8 px (rectangulares, no píldora); tarjetas 16 px; contenedores de foto 24 px.
- **Bordes** de 1 px `--line` en lugar de sombras para separar. Sombra solo en el panel de búsqueda y tarjetas flotantes: `0 1px 2px rgb(0 0 0 / .04), 0 12px 32px -12px rgb(0 0 0 / .12)`.
- Divisores verticales finos entre columnas de la franja de atributos (como una ficha técnica).

### Íconos
`lucide-react`, trazo 1.5, color `--ink` o `--ink-2`, 20–28 px. En la franja de atributos y los diagramas, íconos monocromos. **Sin emojis ni ilustraciones de colores.**

### Snippet para `app/globals.css` (Tailwind v4)
```css
@theme {
  --color-bg: #EFEFEE;
  --color-bg-raised: #F6F6F5;
  --color-surface: #FFFFFF;
  --color-ink: #111111;
  --color-ink-2: #55524E;
  --color-ink-3: #7A7671;
  --color-line: #D9D7D3;
  --color-accent: #C2410C;
  --color-accent-hover: #9A3412;
  --color-accent-bright: #EA580C;
  --color-ok: #15803D;
  --color-footer: #0B0B0B;
  --radius-control: 8px;
  --radius-card: 16px;
  --radius-media: 24px;
  --font-sans: var(--font-geist-sans), ui-sans-serif, system-ui, sans-serif;
  --font-mono: var(--font-geist-mono), ui-monospace, monospace;
}
```

---

## 3. Componentes

### Cabecera
- Fondo transparente sobre `--bg`, se vuelve `--bg` con borde inferior `--line` al hacer scroll (cabecera fija).
- Izquierda: logo Qatu. Centro-izquierda: enlaces de texto 15 px ("Alquilar herramientas", "Contratar servicios", "Cómo funciona"), **subrayado fino** (1 px, offset 4 px) en hover y en la página activa.
- Derecha: "Ofrece en Qatu" (texto), "Iniciar sesión" (texto), "Registrarme" (botón naranja rectangular 8 px).
- Bajo el logo o junto a él, etiqueta mono `AYACUCHO · HUAMANGA` en `--ink-3`.
- Móvil: logo + botón menú (44×44) que abre hoja a pantalla completa con los mismos enlaces en tamaño H3.

### Botones
| Tipo | Estilo |
|---|---|
| Primario | Fondo `--accent`, texto blanco 15 px peso 500, alto 48 px, padding 24 px, radio 8 px. Hover `--accent-hover`. |
| Secundario | Fondo `--surface`, borde 1 px `--ink`, texto `--ink`. |
| Enlace | Texto `--ink` subrayado fino + flecha `→` que se desplaza 2 px en hover. |

Texto de botón en oración ("Buscar", "Publicar herramienta"), no en MAYÚSCULAS salvo que se use la mono.

### Panel de búsqueda (misma función del spec 000, nuevo aspecto)
- Tarjeta `--surface`, radio 16 px, sombra del panel, ancho máx. 880 px, centrada.
- Pestañas encima como texto con subrayado naranja de 2 px en la activa ("Alquilar herramientas" / "Contratar servicios").
- Segmentos separados por divisores verticales de 1 px. Cada segmento: etiqueta mono (`¿QUÉ NECESITAS?`, `DÓNDE`, `CUÁNDO`) + valor 16 px.
- Botón de búsqueda **rectangular** naranja con texto "Buscar" e ícono (no círculo solo con ícono).
- Selectores y calendario propios (shadcn Popover/Command/Calendar con locale `es`), nunca controles nativos.
- Móvil: tarjeta de un campo "¿Qué necesitas?" que al tocarla abre hoja a pantalla completa con los demás campos.

### Franja de atributos (inmediatamente bajo el hero)
Tres columnas separadas por líneas verticales de 1 px, con bordes superior e inferior de 1 px, sobre `--bg`. Cada una: ícono monocromo 28 px + título H3 500 + descripción pequeña `--ink-2`.
Contenido para Qatu (textos reales, sin cifras inventadas):
1. **Cuentas verificadas** — "Validamos DNI y selfie. Los técnicos, además, sus antecedentes."
2. **Entrega registrada** — "Fotos y un código confirman el estado al entregar y devolver."
3. **Garantía clara** — "Ves cuánto dejas de garantía y cuándo vuelve a ti."

Móvil: se apilan con divisores horizontales.

### Tarjetas
- `--surface`, radio 16 px, borde 1 px `--line`, padding 24 px.
- Título H3 con ícono pequeño a la izquierda (como "● Busy Status" de la referencia), descripción `--ink-2` y, opcionalmente, lista de 2–3 viñetas pequeñas.
- Tarjeta de categoría: foto del objeto recortada sobre `--bg-raised` arriba, nombre abajo. Sin precio ni cantidad (regla del spec 000).

### Etiquetas técnicas (detalle firma)
Bloques de texto mono con prefijo `>` en `--ink-2`, usados en listas técnicas:
```
> VERIFICACIÓN CON DNI Y SELFIE
> FOTOS EN ENTREGA Y DEVOLUCIÓN
> CHAT DENTRO DE LA APP
```
Usar en 1–2 lugares como máximo (sección de confianza y "Ofrece en Qatu").

### Diagrama con líneas guía (sección estrella)
Imitación del bloque "Manual controls": una foto grande de una herramienta real, centrada, con **rótulos conectados por líneas de 1 px** que terminan en un punto.
- Para Qatu: **"Qué recibes al alquilar"** sobre la foto de una herramienta con su maletín. Rótulos: `EQUIPO REVISADO`, `ACCESORIOS DEL CHECKLIST`, `FOTOS DEL ESTADO`, `CÓDIGO DE ENTREGA`, `GARANTÍA REGISTRADA`. Cada rótulo: etiqueta mono + una línea de descripción pequeña.
- Implementación: contenedor `relative`, imagen, y un `<svg>` absoluto para las líneas; rótulos posicionados con porcentajes. Posiciones en `content.ts`.
- Móvil (< 768 px): se ocultan las líneas; la foto va arriba y los rótulos pasan a lista numerada debajo (`01`, `02`… en mono).
- Los rótulos son texto HTML real (accesible), no parte de la imagen.

### Secciones "split"
Texto a la izquierda (H2 + párrafo + 3 ítems con ícono pequeño, título 500 y descripción pequeña), foto de ambiente a la derecha que **sangra hasta el borde** del viewport. Se alterna una vez (foto a la izquierda) para dar ritmo. Uso: "Contrata a un técnico" con foto de un técnico trabajando en una casa de Huamanga.

### Sección de imagen completa con título encima
Foto a todo el ancho, fondo claro, con H2 centrado y un párrafo corto en la parte superior de la foto (zona despejada de la imagen). Uso: "Ofrece en Qatu" con una foto de un taller/ferretería ordenado.

### Pie de página
- Fondo `--footer`, esquinas superiores de 24 px (se "levanta" sobre la página), padding 64 px.
- Izquierda: logo Qatu en blanco + "«Qatu» significa mercado en quechua." + © año.
- Centro: dos columnas de enlaces 15 px (Qatu: Alquilar, Contratar, Cómo funciona / Legal: Términos, Privacidad, **Libro de Reclamaciones** destacado con borde).
- Derecha: íconos de redes (solo las que existan) y datos de contacto/RUC en texto pequeño `--footer-ink` secundario.

---

## 4. Orden de la página con este estilo

| # | Sección | Estilo aplicado |
|---|---|---|
| 1 | Cabecera | Enlaces de texto, botón rectangular |
| 2 | **Hero** | Foto de estudio centrada: una mano tomando/usando una herramienta (ej. rotomartillo) sobre gris continuo. Debajo, H1 centrado en 2 líneas, párrafo corto alineado a la izquierda en columna estrecha (como la referencia) y el **panel de búsqueda**. Etiqueta mono `PILOTO EN HUAMANGA, AYACUCHO` sobre el H1. |
| 3 | Franja de atributos | 3 columnas con divisores |
| 4 | Tira de categorías | Íconos monocromos + etiqueta, activa con subrayado naranja; scroll horizontal |
| 5 | "Herramientas para tu obra" | H2 centrado + fila horizontal de tarjetas de categoría con fotos de objeto aislado |
| 6 | **"Qué recibes al alquilar"** | Diagrama con líneas guía |
| 7 | "Contrata a un técnico" | Split: texto + foto de técnico trabajando |
| 8 | "Oficios para tu hogar" | Fila de tarjetas de oficio (ícono grande sobre `--bg-raised`) |
| 9 | Cómo funciona | Dos columnas (Alquilar / Contratar), pasos con número mono `01 02 03`, divisores horizontales entre pasos |
| 10 | Confianza | Tarjeta blanca grande con etiquetas técnicas `>` y el texto "La verificación es un filtro, no una garantía" |
| 11 | Ofrece en Qatu | Imagen a todo el ancho con título encima + 2 botones |
| 12 | Preguntas frecuentes | Acordeón con divisores de 1 px, pregunta 18 px peso 500, ícono `+` que rota a `×` |
| 13 | Pie negro | Columnas + Libro de Reclamaciones |

Presupuesto de "above the fold": en 360×640 deben verse cabecera, foto del hero (máx. 34 vh), H1 y el campo compacto de búsqueda. En 1280×800, cabecera, foto, H1 y el panel completo.

---

## 5. Fotografía (lo que hace o deshace el estilo)

El spec 000 prohíbe fotos inventadas o de stock no respaldable. Este estilo **depende** de fotografía, así que se usan **fotos propias** con esta receta:

**Set casero de estudio (costo casi cero)**
- Fondo: cartulina o vinil blanco/gris claro en curva (de la pared al piso, sin esquina) para el "infinito".
- Luz: ventana grande con luz indirecta, de lado; una cartulina blanca del lado opuesto como rebote. Nada de flash.
- Cámara: celular en modo foto normal (no retrato), a la altura del objeto o ligeramente arriba, lente 2x si hay para evitar deformación.
- Edición: exposición pareja, fondo llevado a `#EFEFEE`, sombra de contacto conservada. Mismo balance de blancos en todas.

**Lista de tomas (mínimo viable, 12 fotos)**
1. Hero: mano sosteniendo o presionando el gatillo de un rotomartillo, objeto centrado, mucho aire alrededor. Horizontal 16:9 y versión 4:5 para móvil.
2. 5 herramientas aisladas (rotomartillo, hidrolavadora, escalera, amoladora, podadora), mismo ángulo 3/4, misma distancia. Cuadradas 1:1.
3. Herramienta abierta en su maletín con accesorios ordenados (para el diagrama). Horizontal.
4. Técnico real trabajando (gasfitero o electricista) en una casa de Huamanga, luz natural, con su permiso firmado. Horizontal y vertical.
5. Taller o ferretería local ordenado (para "Ofrece en Qatu"). Horizontal con zona superior despejada para el título.
6. Manos entregando una herramienta a otras manos (entrega), sobre fondo neutro.

**Formato:** AVIF/WebP con `next/image`, `sizes` correctos, hero con `priority`, el resto `loading="lazy"`. Peso objetivo: hero < 180 KB, tarjetas < 60 KB.

**Mientras no haya fotos:** marcadores de posición en `--bg-raised` con borde discontinuo 1 px, ícono de cámara y la leyenda de la toma que falta (ej. "FOTO 02 · Hidrolavadora aislada 1:1"). Nunca stock de relleno.

---

## 6. Movimiento

Sobrio y mecánico, como el propio producto:
- Aparición al hacer scroll: opacidad 0→1 y desplazamiento 16 px, 400 ms, `cubic-bezier(.2,.7,.2,1)`, una vez por elemento. Stagger de 60 ms en filas de tarjetas.
- Hero: la foto entra con un leve zoom 1.02→1 (600 ms). Nada de parallax pesado.
- Pestañas del buscador: subrayado que se desliza entre pestañas (Motion `layoutId`).
- Diagrama: las líneas guía se "dibujan" (stroke-dashoffset) al entrar en pantalla, 500 ms, luego aparecen los rótulos.
- Hover de tarjetas: la foto sube 4 px y la sombra se intensifica un poco; 200 ms.
- Botón primario: hover solo cambia color; sin rebotes.
- Todo lo anterior desactivado con `prefers-reduced-motion: reduce` (se muestra el estado final).
- Librería: `motion` (motion.dev). Sin GSAP ni librerías adicionales.

---

## 7. Qué NO hacer
- Titulares en negrita pesada, degradados, glassmorphism, fondos con blobs, sombras grandes difusas.
- Botones píldora, botones solo-ícono sin texto para acciones principales.
- Más de un color de acento o naranja en textos largos.
- Emojis, ilustraciones de colores, fotos de stock, fotos con fondos distintos entre sí.
- Controles nativos de fecha/select.
- Precios, cantidades de publicaciones, calificaciones o testimonios inventados (regla del spec 000).
- Copiar textos, logos o fotos de la referencia.

---

## 8. Accesibilidad y rendimiento (sin cambios respecto al spec 000)
- Contraste AA: texto largo solo en `--ink`/`--ink-2`; naranja con texto blanco solo `--accent`.
- Foco visible: anillo 2 px `--accent` con offset 2 px.
- Objetivos táctiles ≥ 44 px. Rótulos del diagrama como texto real.
- Lighthouse móvil: Performance ≥ 90, Accesibilidad ≥ 95, SEO ≥ 95; LCP < 2,5 s (la foto del hero es el LCP: optimizarla primero); JS inicial < 200 KB.

---

## 9. Checklist de aceptación visual
- [ ] Fondo gris continuo y fotos con el mismo fondo (la foto "se funde" con la página).
- [ ] Títulos en Geist 400 con tracking negativo; etiquetas en Geist Mono mayúsculas.
- [ ] Naranja solo en: botón primario, pestaña/categoría activa, foco y detalles gráficos.
- [ ] Franja de 3 atributos con divisores verticales bajo el hero.
- [ ] Sección de diagrama con líneas guía funcionando en escritorio y como lista en móvil.
- [ ] Pie negro con esquinas superiores redondeadas y Libro de Reclamaciones visible.
- [ ] Capturas en 360×640 y 1440×900 revisadas contra este documento.

---

## 10. Prompt para Claude Code

```
Lee el spec de la landing (funcionalidad) y su design.md (dirección visual "Ficha técnica"). design.md manda sobre cualquier decisión estética del spec; la funcionalidad del spec no cambia.

1. Antes de escribir código, muéstrame: los tokens que vas a poner en globals.css, cómo cargarás Geist y Geist Mono con next/font, y un wireframe ASCII de cada sección en móvil (360) y escritorio (1440). Espera mi aprobación.
2. Implementa sección por sección en el orden de la tabla 4, cada una como componente en features/public/landing/components, con los textos en lib/content.ts.
3. Usa marcadores de posición de foto según la sección 5 mientras no existan las imágenes; deja las rutas en content.ts para reemplazarlas.
4. Al terminar cada sección, toma capturas con Playwright en 360x640 y 1440x900, compáralas con el checklist de la sección 9 y corrige antes de pasar a la siguiente.
5. No agregues dependencias fuera de motion, lucide-react y los componentes de shadcn ya definidos.
```
