# 01 — Producto

> Revisión 2026-09-26. Integra la propuesta v2.0 (`Qatu_Propuesta_Arquitectura.docx`): se recupera su visión
> de ecosistema (comercio, alquileres y servicios) y se ordena por fases para que el piloto valide una cosa bien.

## Visión
Qatu es el lugar confiable de Ayacucho para **alquilar la herramienta que necesitas** y **contratar al
técnico que te la resuelve**. Una misma cuenta, una misma capa de confianza y pagos. A largo plazo,
Qatu crece hacia un ecosistema local de alquileres, servicios y comercio, siempre por etapas y sin
abrir una categoría hasta que la anterior funcione.

> Lema: **«Qatu — el mercado local en el que puedes confiar»**.

## Identidad
«Qatu» (también «qhatu») es la palabra quechua para «mercado» o «plaza de intercambio»: tiene sentido
para Ayacucho, es corto, registrable y comunica el propósito sin añadir la palabra «market».
Antes del lanzamiento hay que validar la disponibilidad de la marca (INDECOPI) y del dominio.

## Problema
La oferta de herramientas, bienes en alquiler y servicios está dispersa entre redes sociales, grupos de
mensajería y negocios físicos. Esto genera búsqueda lenta, precios difíciles de comparar, incertidumbre sobre la
disponibilidad, riesgo de estafas, falta de historial confiable y gestión informal de garantías. En el alquiler
el riesgo es mayor: la operación no termina con el pago, porque el bien se devuelve y su estado es motivo de disputa.

## Qué diferencia a Qatu de un clasificado
| Dimensión | Plataforma de anuncios típica | Qatu |
|---|---|---|
| Alcance | Solo compraventa | Alquiler y servicios; comercio en fases posteriores |
| Confianza | Reseñas básicas | Verificación, garantías documentadas, evidencia fotográfica y disputas con flujo definido |
| Comisiones | Poco transparentes | Visibles antes de confirmar; nunca sobre la garantía |
| IA | Ausente o solo palabras clave | Capa transversal explicable (post-MVP) |
| Pagos | Informales | Gestión comercial separada del procesamiento financiero |
| Geografía | Genérica | Diseñado para Ayacucho y preparado para otras ciudades |

## Fases del producto
| Fase | Contenido | Condición para avanzar |
|---|---|---|
| **1. Piloto (MVP)** | Alquiler de herramientas y equipos + servicios de oficios en Ayacucho | Transacciones semanales sostenidas y baja tasa de disputas |
| **2. Ampliación** | Equipos tecnológicos y eventos; destacados y plan Pro | Oferta suficiente en las categorías del piloto |
| **3. Comercio y espacios** | Marketplace de productos de comercios locales; espacios y locales | Validación de demanda y de logística de envío |
| **4. Alto riesgo** | Inmuebles y vehículos | Revisión legal (arrendamiento, seguros) y política de verificación reforzada |
| **5. IA y escala** | Búsqueda semántica, asistente, generación de publicaciones, nuevas ciudades | Datos reales suficientes |

El modelo de datos lo permite sin reescribir el núcleo: `category.vertical` (docs/03) activa cada vertical por ciudad.

## Verticales del piloto

### A. Alquiler (peer-to-peer y negocio-a-persona, estilo Fat Llama)
Categorías iniciales:
- Construcción: rotomartillo, amoladora, mezcladora, andamios, vibradora de concreto.
- Carpintería y taller: sierra circular, cepilladora, taladro de banco, lijadora.
- Jardín: podadora, desbrozadora, motosierra.
- Limpieza: hidrolavadora, aspiradora industrial, lustradora.
- Pintura: compresora, pistola de pintar, escaleras.
- Eventos y audiovisual (fase 2): proyector, parlantes, toldos, sillas.
Oferentes: personas (herramienta ociosa) y negocios (ferreterías, alquiladoras).

### B. Servicios de oficios (híbrido TaskRabbit + Thumbtack)
Oficios iniciales: gasfitería, electricidad, pintura, jardinería, carpintería, cerrajería,
albañilería menor, limpieza, instalación de electrodomésticos, armado de muebles.
Dos modos de contratación:
1. **Reserva directa**: el proveedor publica tarifa por hora (con mínimo de horas) o paquetes de precio
   fijo ("Cambio de caño: S/ 40"). Para trabajos chicos y rápidos.
2. **Solicitud de cotización**: el cliente describe el trabajo con fotos; hasta N proveedores
   compatibles cotizan; el cliente elige. Para trabajos grandes o de alcance incierto.

## Por qué juntos
Son complementarios: quien alquila una hidrolavadora puede contratar a alguien que la use; un pintor
puede alquilar una escalera o una compresora. Cross-sell natural y mayor frecuencia de uso.

## Experiencia de descubrimiento
- Búsqueda por texto, categoría, zona y fechas; solo aparecen las herramientas disponibles en ese rango.
- Resultados en **lista y en mapa**. Al abrir una publicación se ve la ficha completa (fotos, precio para las fechas,
  garantía, reputación) y un **mapa con la ubicación aproximada**, al estilo Airbnb. La dirección exacta y el
  teléfono se revelan solo tras confirmar la transacción (docs/05).
- El detalle técnico está en docs/04 (sección "Detalle de publicación con mapa").

## Usuarios y roles (una cuenta puede tener varios)
| Rol | Hace |
|---|---|
| Cliente | Alquila, contrata, califica. |
| Arrendador | Publica herramientas, acepta reservas, entrega y recibe. Persona o negocio. |
| Proveedor de servicio | Publica oficios/paquetes, cotiza, ejecuta trabajos. |
| Soporte | Media incidencias y disputas. |
| Moderador | Revisa verificaciones, publicaciones y reportes. |
| Admin | Configura categorías, comisiones, ciudades y políticas. |

## Modelo de negocio
- **Comisión por transacción** (fuente principal). Configurable por vertical, categoría y ciudad.
  Valores iniciales de referencia, a validar en piloto: alquiler 10% al arrendador + 5% tarifa de servicio al cliente;
  servicios 10% al proveedor + 5% tarifa de servicio al cliente. **No se fijan porcentajes definitivos** hasta conocer
  el margen real de los oferentes, el costo de procesar pagos, el soporte y cuánto está dispuesto a pagar el mercado.
  **Nunca** se cobra comisión sobre la garantía/depósito ni sobre reembolso de materiales.
- Principios: tarifas visibles antes de confirmar, sin cargos ocultos, comisión separada de impuestos y de costos de
  terceros, publicación básica gratuita, y no depender solo de comisiones desde el inicio.
- Destacados (visibilidad pagada) — fase 2.
- Plan Pro para negocios y proveedores (más publicaciones, estadísticas) — fase 2.
- Costo de visita técnica opcional en cotizaciones (lo cobra el proveedor; Qatu comisiona).

## Métricas norte
- Transacciones completadas por semana (liquidez).
- % de solicitudes que terminan en transacción (tasa de match).
- Tiempo medio de respuesta de oferentes.
- Incidencias y disputas por cada 100 transacciones.
- Recurrencia de clientes a 60 días.
- Complementarias: usuarios y oferentes activos, publicaciones activas, tasa de cancelación, tiempo de resolución de
  reclamos y satisfacción.

## Fuera del piloto (no descartado)
Compraventa de productos, espacios, inmuebles y vehículos entran en fases posteriores (ver "Fases del producto").
Quedan fuera para siempre del alcance actual: empleo formal y delivery propio. Qatu no emplea a los proveedores
(son independientes) y no custodia dinero por sí misma (ver docs/03).

## Plataformas
- Web responsive / PWA primero (uso esperado mayoritariamente móvil, conexiones lentas).
- App nativa (React Native/Expo) cuando la tracción lo justifique, sobre la misma API.
