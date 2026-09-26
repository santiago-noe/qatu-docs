# Qatu — Documentación y contexto para Spec Kit + Claude Code

Qatu: plataforma de **alquiler de herramientas/equipos** y **contratación de servicios de oficios**
(pintor, jardinero, gasfitero, electricista, carpintero, etc.) en Ayacucho, diseñada para escalar
a otras ciudades del Perú y, por fases, a productos y espacios (ver `docs/01-producto.md`).

Este repo (`qatu-docs`) es uno de tres repos independientes del producto:

| Repo | Contenido |
|---|---|
| `qatu-api` | Backend en Go (hexagonal, Fiber v3, PostgreSQL + PostGIS, Redis) |
| `qatu-app` | Frontend en Next.js 16 (App Router, feature-sliced, shadcn/ui) |
| `qatu-docs` | Este repo: documentación, prompts de Spec Kit, colecciones Bruno y requerimientos |

Los tres viven lado a lado en una carpeta contenedora (`Qatu-Ayacucho/`), cada uno con su propio git.

## Qué hay aquí

| Archivo | Para qué sirve |
|---|---|
| `CLAUDE.md` | Puente entre Spec Kit y Claude Code: repos, comandos y definición de terminado. Claude lo lee en cada sesión. |
| `docs/Qatu_Propuesta_Arquitectura.docx` | Propuesta v2.0 (setiembre 2026), origen de la visión de ecosistema. Las decisiones que se tomaron a partir de ella están en `docs/01` y `docs/04`. |
| `docs/01-producto.md` | Visión, identidad, fases del producto, alcance del piloto, usuarios, modelo de negocio. |
| `docs/02-flujos.md` | Flujos completos de alquiler y servicios (paso a paso, con timeouts, búsqueda en lista y mapa, y ficha con mapa). |
| `docs/03-dominio.md` | Entidades, máquinas de estado, ubicación pública, reglas de dinero, comisiones, garantía. |
| `docs/04-arquitectura.md` | Stack Go + Redis, decisiones frente a la propuesta v2.0, reglas de caché, patrones de escalabilidad, seguridad. |
| `docs/05-confianza-y-legal.md` | Verificación, custodia de garantías, privacidad de ubicación, antidesintermediación, Ley 29733, Libro de Reclamaciones. |
| `prompts/00-constitution.md` | Texto para `/speckit.constitution`. |
| `prompts/plan-base.md` | Bloque técnico que pegas en cada `/speckit.plan`. |
| `prompts/features/NNN-*.md` | Un archivo por feature: prompt de `/speckit.specify`, preguntas para `/speckit.clarify` y bloque de plan. |

## Paso a paso

1. **Instala Spec Kit** (requiere `uv` y Python 3.11+):
   ```bash
   uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
   ```
2. **Inicializa Spec Kit dentro de `qatu-docs`** (aquí viven `.specify/` y `specs/`):
   ```bash
   cd qatu-docs
   specify init . --ai claude
   ```
   Haz commit antes de empezar (`git add . && git commit -m "chore: init spec kit"`).
3. **Abre Claude Code en la carpeta contenedora** (`Qatu-Ayacucho/`) para que vea los tres repos:
   el spec, el plan y las tareas se guardan en `qatu-docs`; el código se escribe en `qatu-api` y `qatu-app`.
4. **Constitución (una sola vez)**: pega el contenido de `prompts/00-constitution.md` después de
   `/speckit.constitution`.
5. **Por cada feature, en orden (000, 001, 002, …)**:
   1. `/speckit.specify` + el bloque "SPECIFY" del archivo de la feature.
   2. `/speckit.clarify` → responde; usa las "Preguntas guía" del archivo como referencia.
   3. `/speckit.plan` + el contenido de `prompts/plan-base.md` + el bloque "PLAN (extra)" de la feature.
   4. `/speckit.tasks`
   5. `/speckit.analyze` (no te lo saltes: detecta contradicciones con la constitución).
   6. `/speckit.implement`
   7. Revisa, corre tests, haz merge de la rama de la feature en cada repo tocado. Recién ahí pasa a la siguiente.
6. Haz `/clear` entre features para no arrastrar contexto viejo; `CLAUDE.md` y `docs/` lo recuperan.

## Orden de features (MVP → escala)

| Fase | Features |
|---|---|
| **F-1 Presencia** | 000 landing-page (2026-09-24) |
| **F0 Fundación** | 001 cuentas-identidad · 002 categorias-ubicacion |
| **F1 Oferta** | 003 publicaciones-alquiler · 004 perfiles-proveedores |
| **F2 Descubrimiento** | 005 busqueda · 020 detalle-publicacion-mapa (después de 003 y 005; comparte el componente de mapa) |
| **F3 Transacción** | 006 reservas-alquiler · 007 entrega-devolucion · 008 servicios-solicitud-cotizacion · 009 ejecucion-servicio |
| **F4 Dinero** | 010 pagos-comisiones-liquidaciones · 011 garantia-deposito |
| **F5 Confianza** | 012 mensajeria · 013 notificaciones · 014 resenas-reputacion · 015 incidencias-disputas-reclamaciones |
| **F6 Operación** | 016 admin-moderacion · 017 analitica-oferentes |
| **F7 IA (post-MVP)** | 018 ia-busqueda-asistente · 019 ia-asistente-publicacion |

**MVP piloto en Ayacucho = F0 a F5 con el adaptador de pago "manual/registrado"** (ver docs/03).
Las pasarelas reales (Mercado Pago split / Culqi) entran en F4 detrás de la misma interfaz.
Las fases de producto posteriores (comercio, espacios, inmuebles y vehículos) están descritas en `docs/01-producto.md`
y no tienen features todavía: se especifican cuando el piloto esté validado.

## Reglas de oro al usar Spec Kit

- En `specify` va el **QUÉ y el PORQUÉ**. La tecnología va solo en `plan`.
- Una feature por rama, en cada repo que toque. No mezcles.
- Si Claude inventa algo que contradice `docs/`, corrígelo en el spec, no en el código.
- Tu sitio WordPress actual puede quedarse como landing + lista de espera mientras construyes esto.
- Commits en Conventional Commits, sin línea de coautoría de Claude.
