# 002 — Catálogo de categorías, oficios y ubicación

## SPECIFY
```
/speckit.specify Qatu organiza la oferta en categorías de herramientas (vertical alquiler) y oficios (vertical servicios), y todo lo ubica por ciudad y zona para poder escalar a otras ciudades (docs/01, docs/04).
Historias: (P1) Como admin quiero crear un árbol de categorías de herramientas con atributos propios por categoría (ej. potencia, voltaje) y nivel de riesgo. (P1) Como admin quiero gestionar la lista de oficios. (P1) Como admin quiero registrar ciudades y sus zonas (Ayacucho: Huamanga, San Juan Bautista, Carmen Alto, Jesús Nazareno, Andrés Avelino Cáceres) con su polígono. (P1) Como admin quiero configurar por ciudad y categoría: comisiones, tarifa de servicio, timeouts y políticas (platform_settings) con historial de cambios. (P2) Como admin quiero activar/desactivar una ciudad o categoría con feature flag. (P1) Como usuario quiero que la app detecte o me deje elegir mi ciudad y zona.
Reglas: categorías prohibidas no pueden publicarse; cambiar una comisión no afecta transacciones ya creadas.
```

## Preguntas guía para /speckit.clarify
- ¿Profundidad máxima del árbol de categorías?
- ¿Las zonas son distritos o también barrios?
- ¿Valores iniciales de comisión por vertical?

## Decisiones de clarify (2026-09-28)
| Pregunta | Decisión |
|---|---|
| Profundidad del árbol de categorías | **2 niveles**: categoría > tipo (Construcción > Rotomartillo). Coincide con docs/01 y es simple de navegar en el celular. |
| Zonas | **Solo distritos** en el piloto: Ayacucho (Huamanga), San Juan Bautista, Carmen Alto, Jesús Nazareno y Andrés Avelino Cáceres Dorregaray. El modelo permite agregar barrios después sin migrar datos. |
| Polígonos de las zonas | **Límites oficiales**. Fuente: relaciones de OpenStreetMap (admin_level 8) derivadas de los límites del INEI; el dataset INEI simplificado se descartó porque no incluye Andrés Avelino Cáceres (Ley 30013, 2013) y tiene 4–9 vértices por distrito. Licencia ODbL: el mapa cita "© colaboradores de OpenStreetMap". |
| Comisiones iniciales | **Referencia de docs/01**, marcadas como valores de piloto: alquiler 10 % al arrendador + 5 % de tarifa de servicio al cliente; servicios 10 % al proveedor + 5 % al cliente. Se cambian desde `platform_settings` con historial; una transacción guarda su copia (price_snapshot) y no la afecta un cambio posterior. |

## PLAN (extra) — pegar después de prompts/plan-base.md
```
PostGIS para polígonos de zonas y función zona_de(punto). attributes_schema como JSON Schema validado en back y usado para renderizar formularios dinámicos en web. Seed con categorías y oficios iniciales de docs/01.
```
