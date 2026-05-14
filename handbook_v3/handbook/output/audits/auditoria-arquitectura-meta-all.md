# Caso: Auditoría de arquitectura meta-all

## Descripción

Auditoría exhaustiva de arquitectura de software de todos los repositorios en `workspace/meta-all/`, excluyendo `finances`.

## Current State

**Completado.** Informe generado en `informes/auditoria_arquitectura_software_meta_all.md`.

Última continuación: derivación de dominios candidatos para Kintai a partir de `workspace/meta-all/`, usando el informe previo como base y contrastando con catálogo, perfiles, repos locales y señales de `api/`.

Cobertura: 43 repositorios analizados (superficial o profundamente según relevancia).

## Hallazgos clave

- Sin arquitectura de capas en ningún repositorio crítico (dominio + infraestructura mezclados)
- AdonisJS 4.1 EOL en `api/` (JavaScript, sin soporte desde ~2019)
- `api-client-nodejs`: monolito de 1.426 líneas sin interfaces, ServiceLocator
- `aws-node-client`: JavaScript + AWS SDK v2 deprecado, usado por todo el ecosistema
- `oauth/`: Node.js 14 EOL — riesgo de seguridad activo
- Lambdas con timeout 600-900s que deberían ser ECS/Batch
- Sin DDD, sin eventos de dominio, sin CQRS
- Sin idempotencia en event consumers
- Dashboard: God Components (983 líneas), sin BFF, sin estado centralizado
- Mejores repos: `data-pipelines/`, `reconciliation/`, `qonto-client/`, `noa-bpm/` (NoaSender)

## Repositorios que requieren revisión adicional

- `api/` — solo muestras representativas, 90 controllers no revisados exhaustivamente
- `dashboard/` — solo módulos más críticos (~2000 ficheros TS)
- `api-client-nodejs/` — 20+ sub-módulos analizados parcialmente

## History

### 2026-04-28 — Ejecución inicial

- Leídos: CLAUDE.md, AGENTS.md, handbook/index.md, handbook/roles/default.md, handbook/skills/default.md, handbook/roles/software-architect.md
- Leído informe previo: ANALISIS_ARQUITECTURA_PATRONES_SOLID.md (baseline)
- Explorados 43 repositorios en 4 batches paralelos de agentes
- Generado informe completo: 1715 líneas, ~90KB
- Ruta del informe: `informes/auditoria_arquitectura_software_meta_all.md`

### 2026-05-05 — Derivación de dominios candidatos

- Leídos: handbook/index.md, governance, rol software-architect, skills default, memoria de reglas/catálogo/integración/glosario.
- Continuado desde este registro por coincidencia con `meta-all`.
- Contrastados: `workspace/meta-all/docs/repos-catalog.md`, perfiles `workspace/meta-all/profiles/*.json`, informe `informes/auditoria_arquitectura_software_meta_all.md`, metadatos de repos y señales de modelos/controladores/rutas de `api/`.
- Resultado mostrado por pantalla: propuesta de dominios candidatos para Kintai, separando dominios de negocio, dominios de soporte y capacidades técnicas/plataforma.
