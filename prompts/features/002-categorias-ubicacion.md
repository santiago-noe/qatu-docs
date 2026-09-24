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

## PLAN (extra) — pegar después de prompts/plan-base.md
```
PostGIS para polígonos de zonas y función zona_de(punto). attributes_schema como JSON Schema validado en back y usado para renderizar formularios dinámicos en web. Seed con categorías y oficios iniciales de docs/01.
```
