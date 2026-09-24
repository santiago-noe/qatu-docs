# 01 — Producto

## Visión
Qatu es el lugar confiable de Ayacucho para **alquilar la herramienta que necesitas** y **contratar al
técnico que te la resuelve**. Dos verticales, una misma cuenta, una misma capa de confianza y pagos.

> Cambio respecto a la propuesta v2: se **elimina la compraventa de productos**, los inmuebles y los
> vehículos. Alcance cerrado a (1) alquiler de herramientas y equipos y (2) servicios de oficios.

## Por qué juntos
Son complementarios: quien alquila una hidrolavadora puede contratar a alguien que la use; un pintor
puede alquilar una escalera o una compresora. Cross-sell natural y mayor frecuencia de uso.

## Verticales

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
  Valores iniciales a validar en piloto: alquiler 10% al arrendador + 5% tarifa de servicio al cliente;
  servicios 10% al proveedor + 5% tarifa de servicio al cliente.
  **Nunca** se cobra comisión sobre la garantía/depósito ni sobre reembolso de materiales.
- Destacados (visibilidad pagada) — fase 2.
- Plan Pro para negocios/proveedores (más publicaciones, estadísticas) — fase 2.
- Costo de visita técnica opcional en cotizaciones (lo cobra el proveedor; Qatu comisiona).

## Métricas norte
- Transacciones completadas por semana (liquidez).
- % de solicitudes que terminan en transacción (tasa de match).
- Tiempo medio de respuesta de oferentes.
- Incidencias y disputas por cada 100 transacciones.
- Recurrencia de clientes a 60 días.

## Fuera de alcance
Compraventa de productos, inmuebles, vehículos, empleo formal, delivery. Qatu no emplea a los
proveedores (son independientes) y no custodia dinero por sí misma (ver docs/03).

## Plataformas
- Web responsive / PWA primero (uso esperado mayoritariamente móvil, conexiones lentas).
- App nativa (React Native/Expo) cuando la tracción lo justifique, sobre la misma API.
