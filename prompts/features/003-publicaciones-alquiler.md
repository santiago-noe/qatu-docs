# 003 — Publicaciones de herramientas en alquiler

## SPECIFY
```
/speckit.specify Los arrendadores (personas o negocios) publican herramientas con toda la información necesaria para alquilar sin ambigüedad (flujo A1 en docs/02).
Historias: (P1) Como arrendador quiero publicar una herramienta con categoría, atributos, fotos, accesorios incluidos, valor de reposición y precios por hora/día/fin de semana/semana/mes. (P1) Quiero que el sistema me sugiera una garantía según categoría y valor, y poder ajustarla dentro de un rango. (P1) Quiero definir recojo (zona pública, dirección exacta privada) y/o delivery con tarifa. (P1) Quiero elegir modo de reserva (por solicitud o inmediata), política de cancelación (Flexible/Moderada/Estricta), nivel mínimo de verificación del arrendatario, duración mínima y máxima y antelación mínima. (P1) Quiero bloquear fechas en un calendario. (P1) Quiero pausar, editar y archivar mis publicaciones. (P2) Como negocio quiero publicar varias unidades del mismo modelo (inventario). (P1) Como moderador quiero revisar primeras publicaciones y de categorías de riesgo.
Reglas: mínimo 3 fotos; la foto del número de serie es privada; editar precios no afecta reservas existentes.
```

## Preguntas guía para /speckit.clarify
- ¿El inventario multi-unidad entra en el MVP o se modela 1 publicación = 1 unidad?
- ¿Rangos permitidos de garantía por categoría?
- ¿El arrendador persona puede ofrecer delivery?

## PLAN (extra) — pegar después de prompts/plan-base.md
```
Tabla tool_listings con version; availability_blocks con period tstzrange y EXCLUDE USING gist (listing_id WITH =, period WITH &&); subida de fotos por URL prefirmada + job de procesamiento de imágenes (sharp, WebP, 3 tamaños).
```
