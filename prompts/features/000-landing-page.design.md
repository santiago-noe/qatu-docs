# 000 — Landing: dirección visual "Obra" (marca Qatu)

- **Complementa a:** `000-landing-page.md` (spec funcional). Este archivo **reemplaza la sección "DISEÑO / Sistema visual"** del spec 000. La funcionalidad del spec (búsqueda, categorías, confianza, cómo funciona, Ofrece en Qatu, FAQ, pie legal, sesión) **se mantiene**.
- **Reemplaza a:** las direcciones "Ficha técnica" y "Catálogo" (ambas del 2026-09-26, en el historial de git). Motivo: ya existe una **identidad de marca** (logo, paleta y tipografía) y el diseño debe partir de ella.
- **Referencias:**
  - Manual de marca Qatu: logo "Q + casa + llave", lema "Alquila. Contrata. Construye.", paleta `#FFB703` / `#1F2937`, tipografía Poppins SemiBold.
  - Maqueta de la landing (2026-09-26): cabecera blanca con buscador, barra de aviso, hero con foto, categorías en tarjetas, oficios en fila, franja amarilla "Cómo funciona" y pie oscuro.
- **Archivos de marca en `qatu-app`:** `public/brand/logo-0.png` (logo completo, fondo transparente) y `app/icon.png` (ícono para la pestaña). Pendiente: versión blanca del logo para fondos oscuros, idealmente en SVG.
- **Fecha:** 2026-09-26
- **Estado:** aprobado para implementar.

---

## 1. Concepto
**"Qatu, la obra resuelta en Huamanga."**
Blanco y gris oscuro como base, amarillo de obra como energía. Transmite trabajo, herramientas y confianza sin parecer una ferretería genérica: el gris azulado `#1F2937` del logo (no negro puro) y el ámbar oscuro para textos dan una identidad propia.

Tres rasgos que definen el estilo:
1. **Amarillo como fondo, nunca como texto sobre blanco.** Botones, franjas, círculos de íconos y subrayados en amarillo, siempre con texto oscuro encima.
2. **Gris oscuro como tinta y como botón principal.** Texto, cabecera de acción, pie y botón principal en `#1F2937`.
3. **Tarjetas cálidas.** Fondos crema muy suaves, esquinas redondeadas de 12 px e íconos de línea grandes.

---

## 2. Adaptaciones obligatorias a las reglas de Qatu
La maqueta incluye datos que no existen. El spec 000 prohíbe mostrar cifras, reseñas o promesas inventadas. Por eso:

| En la maqueta | En Qatu |
|---|---|
| Tarjeta flotante "+500 profesionales confían en Qatu" y "4.8/5, basado en 120+ reseñas" | **No se muestra** en el piloto. Se habilita solo con datos reales (reseñas de la feature 014), calculados en el servidor. |
| "Fácil, seguro y **al mejor precio** en Huamanga" | "Fácil, seguro y con **precios claros** en Huamanga". No se afirma "mejor precio". |
| "Equipos verificados · **Seguridad garantizada**" | "Cuentas verificadas · La verificación es un filtro, no una garantía" (docs/05). |
| "Precios justos · Sin costos ocultos" | "Precios claros · Ves el total antes de confirmar". |
| "Soporte local · Estamos en Ayacucho" | Se mantiene (es real: piloto en Huamanga). |
| Paso "3. Recibe el equipo **en tu ubicación**" | "3. Recoge o recibe": el delivery es opcional y depende del arrendador (docs/02). |
| Pie "Recibe ofertas y novedades" con suscripción | Solo si se aprueba la lista de espera: correo + **casilla de consentimiento** (Ley 29733) y texto "novedades del piloto", no "ofertas". Sin aprobación, se muestra "Crea tu cuenta gratis". |
| Íconos de Facebook, Instagram y WhatsApp | Solo las redes que existan; si no hay ninguna, la columna no se muestra. |
| Lema de marca "Compra · Alquila · Contrata" (versión anterior) | Se usa el lema del logo actual, "Alquila. Contrata. Construye." La compra es fase 3. |
| Foto del hero con herramienta amarilla y negra | Válida para el prototipo; antes de publicar, foto propia. Evitar que el conjunto (amarillo + negro + herramienta) se confunda con una marca comercial de herramientas: por eso el gris azulado del logo en lugar de negro. |

---

## 3. Tokens

### Color (contrastes calculados con la fórmula WCAG)
| Token | Valor | Uso | Contraste |
|---|---|---|---|
| `--bg` | `#FFFFFF` | Fondo general | — |
| `--bg-soft` | `#F9FAFB` | Secciones alternas (oficios) | — |
| `--cream` | `#FFF7E6` | Fondo de tarjetas de categoría y chips del hero | — |
| `--ink` | `#1F2937` | Texto principal, botón principal, pie | 14,68:1 sobre blanco |
| `--ink-2` | `#4B5563` | Texto secundario | 7,56:1 sobre blanco |
| `--ink-3` | `#6B7280` | Textos pequeños y ayudas | 4,83:1 sobre blanco; 4,63:1 sobre `--bg-soft` |
| `--line` | `#E5E7EB` | Bordes y divisores de 1 px | — |
| `--brand` | `#FFB703` | Amarillo de marca: fondos de botón secundario destacado, franja "Cómo funciona", círculos de íconos, subrayados, foco | **1,75:1 sobre blanco: nunca como texto**. Texto `--ink` encima: 8,41:1 |
| `--brand-soft` | `#FFF1CC` | Círculos detrás de íconos, hover de tarjetas | — |
| `--brand-text` | `#B45309` | Amarillo para **texto** (antetítulos, palabra destacada del título, enlaces) | 5,02:1 sobre blanco; 4,71:1 sobre `--cream` |
| `--ok` | `#15803D` | Verificado / éxito | — |
| `--footer` | `#111827` | Pie de página | — |
| `--footer-ink` | `#D1D5DB` / `#9CA3AF` | Texto del pie (principal / secundario) | 12,04:1 / 6,99:1 sobre el pie |

Reglas:
- El amarillo `#FFB703` **nunca lleva texto blanco** ni se usa como color de texto sobre fondo claro; para texto, `--brand-text`.
- Botón principal: fondo `--ink`, texto blanco. Botón de acento (opcional, uno por pantalla): fondo `--brand`, texto `--ink`. Botón secundario: borde `--line`, texto `--ink`.
- En el pie oscuro, el amarillo sí puede usarse como texto (10,16:1 sobre `#111827`).

### Tipografía
- **Poppins** (`next/font/google`, pesos 400, 500, 600 y 700), reemplaza a Geist. Es la tipografía del manual de marca.
- H1 del hero: 36 → 56 px, peso 700, interlineado 1.05, tracking -0.02em. La última línea en `--brand-text`.
- H2 de sección: 24 → 32 px, peso 600, alineado a la izquierda.
- Antetítulo: 12 px, peso 600, MAYÚSCULAS, tracking 0.06em, `--brand-text`.
- Cuerpo: 15 → 16 px, peso 400, interlineado 1.6, `--ink-2`.
- Tarjetas: título 16 px peso 600; descripción 14 px `--ink-2`.

### Forma y espaciado
- Radios: botones 8 px; chips y tarjetas 12 px; tarjeta flotante 16 px.
- Separación entre secciones: 56 px móvil, 80 px escritorio. Contenedor máx. 1200 px, márgenes 16/48 px (componente `Container`).
- Sombra solo en tarjetas al pasar el cursor: `0 8px 24px -12px rgb(31 41 55 / .18)`.

### Íconos
`lucide-react`, trazo 1.5, `--ink`, 24–40 px. En tarjetas, el ícono grande va sin fondo; en chips y oficios, dentro de un círculo `--brand-soft`.

---

## 4. Estructura de la página

| # | Sección | Descripción |
|---|---|---|
| 1 | **Cabecera** | Blanca, fija. Logo `logo-0` a la izquierda; enlaces "Alquilar", "Contratar", "Cómo funciona", "Ofrece en Qatu"; buscador "Buscar herramienta o servicio…" (GET a `/buscar`); a la derecha "Iniciar sesión" (texto) y "Registrarme" (botón `--ink`). Móvil: logo, búsqueda y menú. |
| 2 | **Barra de aviso** | Franja `--footer` de 32 px, texto blanco con ícono de ubicación en `--brand`: "Piloto en Huamanga, Ayacucho · Regístrate gratis". |
| 3 | **Hero** | Fondo blanco. Izquierda: antetítulo "ALQUILER DE HERRAMIENTAS Y SERVICIOS"; H1 de tres líneas con la última en `--brand-text`; párrafo; botones "Alquilar ahora →" (`--ink`) y "Cómo funciona" (borde); tres chips `--cream` con ícono en círculo `--brand-soft`: Cuentas verificadas, Precios claros, Soporte local. Derecha: foto `hero-1.webp` fundida con el fondo. Sin tarjeta de cifras. |
| 4 | **Buscador** | Panel de búsqueda completo (pestañas Alquilar / Contratar, qué, dónde, cuándo) con botón `--ink`. Destino de "Explora" y del ícono de búsqueda móvil (`#buscar`). |
| 5 | **Categorías** | Antetítulo "CATEGORÍAS", H2 "Encuentra lo que necesitas", subtítulo. Cuadrícula de 5 tarjetas (2 en móvil, desplazable): fondo `--cream`, ícono grande, nombre, descripción corta y botón pequeño "Ver equipos →". Sin precios ni "más alquiladas". |
| 6 | **Oficios** | Fondo `--bg-soft`. Izquierda: antetítulo "SERVICIOS", H2 "Oficios para tu hogar o negocio", párrafo y botón "Contratar ahora →". Derecha: fila de oficios con ícono en círculo `--brand-soft`, nombre y dos palabras de descripción, separados por divisores; al final "Ver todos". Desplazable en móvil. |
| 7 | **Cómo funciona** | Franja `--brand` a todo el ancho. Título "¿CÓMO FUNCIONA QATU?" y 4 pasos con ícono de línea, separados por divisores: 1. Busca · 2. Reserva · 3. Recoge o recibe · 4. Usa y devuelve. Texto `--ink`. |
| 8 | **Confianza** | Breve: cómo verificamos, cómo se registra la entrega, y "La verificación es un filtro, no una garantía". |
| 9 | **Preguntas frecuentes** | Acordeón con divisores de 1 px. |
| 10 | **Pie** | Fondo `--footer`. Logo (versión blanca cuando exista) y frase "Conectamos herramientas y personas para construir un mejor Ayacucho"; columnas Enlaces, Empresa, Ayuda (con **Libro de Reclamaciones**); bloque de novedades según la regla de la sección 2; redes solo si existen. Línea final "© año Qatu". |

Above the fold: en 360×640 deben verse cabecera, barra de aviso, antetítulo, H1 y botón principal. En 1280×800, cabecera, barra y el hero completo con foto.

---

## 5. Fotografía
- Fotos propias antes de publicar: herramientas en uso y técnicos reales de Huamanga con permiso firmado.
- Formato WebP/AVIF con `next/image`; la foto del hero con `priority` (es el LCP).
- Mientras no haya fotos para tarjetas y oficios se usan íconos de `lucide-react`; nunca stock.

---

## 6. Movimiento
- Hover de tarjeta: sube 2 px y aparece la sombra (200 ms). Flecha de los botones se desplaza 2 px.
- Sin carruseles automáticos ni parallax. Todo desactivado con `prefers-reduced-motion: reduce`.
- `motion` no se usa; se retira la dependencia.

---

## 7. Qué NO hacer
- Texto amarillo `#FFB703` sobre blanco o texto blanco sobre amarillo.
- Negro puro como color de marca; usar `#1F2937`.
- Cifras, estrellas, "más alquiladas", "mejor precio", "garantizado", ofertas o testimonios sin datos reales.
- Carrito o compra (no hay compra en el piloto).
- Fotos de stock; copiar marcas o logos de terceros.

---

## 8. Accesibilidad y rendimiento (sin cambios respecto al spec 000)
- Contraste AA en todo texto (ver tabla de tokens). Foco visible: anillo de 2 px `--brand` con separación de 2 px y borde interior `--ink` para que se vea sobre fondos claros.
- Objetivos táctiles ≥ 44 px.
- Lighthouse móvil: Performance ≥ 90, Accesibilidad ≥ 95, SEO ≥ 95; LCP < 2,5 s; JS inicial < 200 KB.

---

## 9. Checklist de aceptación visual
- [ ] Logo `logo-0` en la cabecera e ícono en la pestaña.
- [ ] Poppins en toda la página.
- [ ] Ningún texto amarillo claro sobre blanco; antetítulos y palabra destacada en `--brand-text`.
- [ ] Botón principal gris oscuro; amarillo solo con texto oscuro.
- [ ] Hero sin tarjeta de cifras ni frases de "mejor precio" o "garantizado".
- [ ] Franja amarilla "Cómo funciona" con 4 pasos.
- [ ] Pie oscuro con Libro de Reclamaciones y sin redes inexistentes.
- [ ] Revisado en 360×640 y 1440×900.

---

## 10. Prompt para Claude Code
```
Lee el spec de la landing (funcionalidad) y su design.md (dirección visual "Obra"). design.md manda sobre lo estético; las reglas de datos del spec (nada inventado) prevalecen sobre la maqueta.

1. Implementa sección por sección en el orden de la tabla 4, cada una como componente en features/public/landing/components, con los textos en lib/content.ts y el ancho con el componente Container.
2. Reutiliza componentes (Logo, Container, Button de shadcn, SectionHeading); no dupliques secciones ni estilos.
3. Revisa cada sección en 360x640 y 1440x900 contra el checklist de la sección 9.
4. No agregues dependencias nuevas; retira motion si no se usa.
```
