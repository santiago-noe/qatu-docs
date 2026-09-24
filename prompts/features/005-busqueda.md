# 005 — Búsqueda y descubrimiento

## SPECIFY
```
/speckit.specify Clientes deben encontrar rápido una herramienta disponible o un proveedor cercano (flujo A2 y B1 en docs/02).
Historias: (P1) Como cliente quiero buscar herramientas por texto, categoría, zona o distancia, rango de fechas y precio, viendo solo las disponibles en esas fechas. (P1) Quiero buscar proveedores por oficio y zona, filtrando por verificados, calificación, precio y disponibilidad en una fecha. (P1) Quiero ordenar por relevancia, distancia, precio o calificación. (P1) Quiero ver resultados en lista y en mapa (ubicación aproximada). (P2) Quiero guardar favoritos y búsquedas. (P2) Como admin quiero ver búsquedas sin resultados para detectar demanda.
Reglas: la búsqueda tolera tildes y errores menores; nunca muestra la ubicación exacta; nuevos proveedores reciben una cuota de visibilidad justa.
```

## Preguntas guía para /speckit.clarify
- ¿Radio por defecto de búsqueda?
- ¿Qué señales pesan en el ranking de relevancia al inicio?

## PLAN (extra) — pegar después de prompts/plan-base.md
```
Postgres FTS con unaccent y configuración spanish + índices GIN; PostGIS ST_DWithin con índice GIST; exclusión de listings con availability_blocks solapados; paginación por cursor; ofuscación de coordenadas (redondeo o punto aleatorio en radio).
```
