# 016 — Panel de administración y moderación

## SPECIFY
```
/speckit.specify Operar la plataforma día a día.
Historias: (P1) Como moderador quiero colas de verificaciones, publicaciones en revisión y reportes. (P1) Como admin quiero buscar usuarios, suspender o reactivar con motivo. (P1) Como admin quiero ver y operar cualquier transacción con su línea de tiempo y auditoría. (P1) Como admin quiero editar platform_settings y feature flags. (P2) Como admin quiero un dashboard de métricas norte (docs/01).
Reglas: toda acción administrativa se audita; principio de mínimo privilegio por rol interno.
```

## Preguntas guía para /speckit.clarify
- (Responde lo que pregunte clarify usando docs/.)

## PLAN (extra) — pegar después de prompts/plan-base.md
```
Sección /admin en la misma app Next.js protegida por rol; vistas SQL para métricas.
```
