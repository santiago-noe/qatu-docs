# 004 — Perfiles de proveedores de servicios

## SPECIFY
```
/speckit.specify Los proveedores de oficios necesitan un perfil que genere confianza y permita reservarlos o pedirles cotización (flujos B y C en docs/02).
Historias: (P1) Como proveedor quiero crear mi perfil con oficios, años de experiencia, descripción, fotos de trabajos anteriores (portafolio) y zonas de cobertura. (P1) Quiero definir tarifa por hora con mínimo de horas y/o paquetes de precio fijo por oficio. (P1) Quiero definir mi disponibilidad semanal y bloquear días. (P1) Quiero indicar los días de garantía de mi trabajo. (P2) Quiero indicar si acepto trabajos urgentes el mismo día. (P1) Como cliente quiero ver el perfil con insignias de verificación, reseñas, tasa de respuesta y trabajos completados.
Reglas: solo usuarios con nivel P pueden aparecer como proveedores para servicios en el hogar; perfil incompleto no aparece en búsquedas.
```

## Preguntas guía para /speckit.clarify
- ¿Un proveedor puede ser una empresa con varios técnicos (equipo) en el MVP?
- ¿Se permite 'precio a cotizar' sin tarifa publicada?

## PLAN (extra) — pegar después de prompts/plan-base.md
```
provider_profiles, provider_trades, service_packages, provider_coverage_zones, weekly_availability; métricas derivadas (tasa de respuesta, tiempo medio) calculadas por eventos.
```
