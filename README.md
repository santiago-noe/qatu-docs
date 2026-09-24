# Qatu — Paquete de contexto para Spec Kit + Claude Code

Qatu (v3): app de **alquiler de herramientas/equipos** y **contratación de servicios de oficios**
(pintor, jardinero, gasfitero, electricista, carpintero, etc.) en Ayacucho, diseñada para escalar
a otras ciudades del Perú.

## Qué hay aquí

| Archivo | Para qué sirve |
|---|---|
| `CLAUDE.md` | Puente entre Spec Kit y Claude Code. Va en la raíz del repo. Claude lo lee en cada sesión. |
| `docs/01-producto.md` | Visión, alcance, usuarios, modelo de negocio, qué NO es Qatu. |
| `docs/02-flujos.md` | Flujos completos de alquiler y servicios (paso a paso, con timeouts y casos borde). |
| `docs/03-dominio.md` | Entidades, máquinas de estado, reglas de dinero, comisiones, garantía. |
| `docs/04-arquitectura.md` | Stack, módulos, patrones de escalabilidad, seguridad, infraestructura. |
| `docs/05-confianza-y-legal.md` | Verificación, disputas, antidesintermediación, Ley 29733, Libro de Reclamaciones. |
| `prompts/00-constitution.md` | Texto para `/speckit.constitution`. |
| `prompts/plan-base.md` | Bloque técnico que pegas en cada `/speckit.plan`. |
| `prompts/features/NNN-*.md` | Un archivo por feature: prompt de `/speckit.specify` + preguntas para `/speckit.clarify`. |

## Paso a paso

1. **Instala Spec Kit** (requiere `uv` y Python 3.11+):
   ```bash
   uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
   specify init qatu --ai claude
   cd qatu
   ```
2. **Copia el contexto al repo**: `docs/` completo a `qatu/docs/` y `CLAUDE.md` a la raíz.
   Haz commit antes de empezar (`git add . && git commit -m "docs: contexto Qatu"`).
3. Abre Claude Code en esa carpeta (`claude`).
4. **Constitución (una sola vez)**: pega el contenido de `prompts/00-constitution.md` después de
   `/speckit.constitution`.
5. **Por cada feature, en orden (001, 002, …)**:
   1. `/speckit.specify` + el bloque "SPECIFY" del archivo de la feature.
   2. `/speckit.clarify` → responde; usa las "Preguntas guía" del archivo como referencia.
   3. `/speckit.plan` + el contenido de `prompts/plan-base.md` + el bloque "PLAN (extra)" de la feature.
   4. `/speckit.tasks`
   5. `/speckit.analyze` (no te lo saltes: detecta contradicciones con la constitución).
   6. `/speckit.implement`
   7. Revisa, corre tests, haz merge de la rama de la feature. Recién ahí pasa a la siguiente.
6. Haz `/clear` entre features para no arrastrar contexto viejo; `CLAUDE.md` y `docs/` lo recuperan.

## Orden de features (MVP → escala)

| Fase | Features |
|---|---|
| **F0 Fundación** | 001 cuentas-identidad · 002 categorias-ubicacion |
| **F1 Oferta** | 003 publicaciones-alquiler · 004 perfiles-proveedores |
| **F2 Descubrimiento** | 005 busqueda |
| **F3 Transacción** | 006 reservas-alquiler · 007 entrega-devolucion · 008 servicios-solicitud-cotizacion · 009 ejecucion-servicio |
| **F4 Dinero** | 010 pagos-comisiones-liquidaciones · 011 garantia-deposito |
| **F5 Confianza** | 012 mensajeria · 013 notificaciones · 014 resenas-reputacion · 015 incidencias-disputas-reclamaciones |
| **F6 Operación** | 016 admin-moderacion · 017 analitica-oferentes |
| **F7 IA (post-MVP)** | 018 ia-busqueda-asistente · 019 ia-asistente-publicacion |

**MVP piloto en Ayacucho = F0 a F5 con el adaptador de pago "manual/registrado"** (ver docs/03).
Las pasarelas reales (Mercado Pago split / Culqi) entran en F4 detrás de la misma interfaz.

## Reglas de oro al usar Spec Kit

- En `specify` va el **QUÉ y el PORQUÉ**. La tecnología va solo en `plan`.
- Una feature por rama. No mezcles.
- Si Claude inventa algo que contradice `docs/`, corrígelo en el spec, no en el código.
- Tu sitio WordPress actual puede quedarse como landing + lista de espera mientras construyes esto.
