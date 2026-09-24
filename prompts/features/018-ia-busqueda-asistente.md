# 018 — IA: búsqueda en lenguaje natural y asistente

## SPECIFY
```
/speckit.specify Buscar escribiendo como se habla: 'necesito una hidrolavadora el sábado en Carmen Alto por menos de 60 soles' o 'se me rompió el caño del baño, necesito un gasfitero hoy'.
Historias: (P2) Como cliente quiero que la búsqueda entienda intención, vertical, categoría/oficio, fechas, zona y presupuesto y muestre resultados reales. (P2) Como cliente quiero un asistente que responda preguntas sobre publicaciones y condiciones usando solo datos de Qatu y que me lleve a reservar. (P2) Recomendaciones explicables ('similar, disponible en tus fechas y dentro de tu presupuesto').
Reglas: la IA nunca inventa precios, disponibilidad ni condiciones; se identifica como IA; si no hay datos, lo dice.
```

## Preguntas guía para /speckit.clarify
- ¿Proveedor de LLM y presupuesto mensual?

## PLAN (extra) — pegar después de prompts/plan-base.md
```
Extracción de intención a JSON validado con Zod → consulta al módulo search; embeddings en pgvector; caché de respuestas; registro de consultas para evaluación.
```
