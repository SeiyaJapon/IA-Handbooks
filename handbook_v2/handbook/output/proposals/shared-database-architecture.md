# Work Record — Propuesta arquitectura base de datos compartida

**Caso:** Preparación de sesión de arquitectura: gestión de base de datos compartida en DDD/microservicios  
**Estado:** En progreso  
**Audiencia:** Engineering Manager (reunión arquitectura)

---

## Current State

Propuesta completa generada en `/Users/franciscoperez/Kintai/PROPUESTA_ARQUITECTURA_BASE_DE_DATOS_COMPARTIDA.md`

Cubre:
- Situación actual (base evidencial del ecosistema meta-all)
- Problemas reales de la base de datos compartida
- Consecuencias a corto, medio y largo plazo
- Propuesta de solución en 5 fases (sin split inmediato de BD)
- Comunicación entre servicios (síncrona: SDK/HTTP + asíncrona: eventos)
- Trazabilidad y observabilidad
- Decisiones a llevar a la reunión con pros/contras y recomendación

---

## History

### 2026-05-04 — Primera ejecución

- Leídos todos los informes de auditoría del ecosistema (43 repos, api/ AdonisJS, lambdas, dashboard)
- Contexto clave: MySQL compartida accesible solo vía api/ monolito; todos los Lambdas via api-client-nodejs
- Generada propuesta profunda para reunión con EM

### 2026-05-05 — Versión 2.0 — múltiples cambios

- Versión del documento: 1.0 → 2.0
- Fase 0: añadido mapa de 5 macro-dominios (Identity, Risk, Lending/Finance, Payments, Legal)
- Fase 1 auth-service: modelo de datos completo, corte duro en autorización
- Fase 2: renombrada "Reconstrucción", estado real de notifications/documents/reports
- Sección 7.4: añadida evaluación del modelo de ejecución por servicio (Lambda sprawl)
- Sección 7.5: Rollbar vs Sentry, recomendación de adoptar Sentry en nuevos servicios
- Decisión 1: clarificado que modularizar = extraer bounded contexts
- Decisión 2: Opción B (dividir BD) es la recomendada a largo plazo; Opción A solo realidad de Fase 1
- Resumen ejecutivo: punto 4 actualizado, división de BD como dirección, no como opción
- Anexo A: CQRS — cuándo y en qué dominios considerarlo (Risk y Lending como candidatos)

### 2026-05-05 — Revisión Fase 2 con estado real de repos

- `notifications/`, `documents/`, `reports/` ya existen fuera de `api/` — no son candidatos a "extraer"
- Fase 2 renombrada a "Reconstrucción de servicios de plataforma"
- Documentado el estado real de cada repo con sus problemas concretos
- `notifications/`: sin interfaces, lógica en handlers, severity alta
- `documents/`: mejor (ETL parcial), severity media
- `reports/`: estructura plana, severity alta
- El trabajo es restructurar siguiendo el patrón de Fase 1, no extraer de api/
- Referencia a estos repos en Fase 3 limpiada

### 2026-05-05 — Revisión auth-service con modelo concreto

- Incorporado modelo de datos completo: persons, users, companies, company_persons, kyc/kyb, user_permissions con scope
- Roles y posiciones excluidos del camino de autorización (solo helpers de UX en el futuro)
- Clarificada la restricción de autorización única: corte duro en día 1, `api/` deja de evaluar permisos
- Autenticación puede migrarse gradualmente; autorización no puede tener dos fuentes de verdad simultáneas
- Migración de datos de permisos debe completarse antes del go-live

### 2026-05-04 — Revisión Fase 1

- Fase 1 reescrita: ya no es "modularización interna del monolito"
- Fase 1 es ahora: consolidar `finances` como servicio de referencia + construir `auth-service`
- `auth-service` asume login, autenticación y autorización; reemplaza `oauth/` (EOL)
- Resumen ejecutivo y Decisión 5 actualizados para alinear con el nuevo Fase 1

### 2026-05-04 — Revisión Fase 0

- Sustituido "OpenAPI para api/" por inventario de endpoints + contratos mínimos en TypeScript
- Añadido ejemplo de contrato (`GetCompanyByIdContract` con `as const`)
- Añadida nota explícita posponiendo OpenAPI sin descartarlo como opción futura
- Actualizado resumen ejecutivo: "contratos OpenAPI" → "contratos en TypeScript por dominio"
- Fases posteriores, AsyncAPI y estructura general sin cambios
