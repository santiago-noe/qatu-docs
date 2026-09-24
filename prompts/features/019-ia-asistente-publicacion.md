# 019 — IA: asistente para publicar

## SPECIFY
```
/speckit.specify Publicar debe ser fácil para personas con poca experiencia digital.
Historias: (P2) Como arrendador quiero subir fotos y que se sugiera categoría, título y descripción que puedo editar. (P2) Como proveedor quiero redactar mi bio y paquetes a partir de lo que dicto. (P2) Como moderador quiero detección de fotos duplicadas o robadas.
Reglas: la IA propone, el usuario confirma; nunca completa precio ni garantía por su cuenta.
```

## Preguntas guía para /speckit.clarify
- (Responde lo que pregunte clarify usando docs/.)

## PLAN (extra) — pegar después de prompts/plan-base.md
```
Llamadas a LLM multimodal detrás de un puerto AiAssistant; hash perceptual de imágenes para duplicados.
```
