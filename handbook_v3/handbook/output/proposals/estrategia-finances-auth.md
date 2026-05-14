# Work Record — Estrategia arquitectónica: finances + auth como casos reales

**Caso:** Análisis crítico de estrategia para reunión de arquitectura  
**Estado:** En progreso  
**Audiencia:** Engineering Manager / Sesión de Arquitectura  

---

## Current State

Análisis completo generado a continuación en este mismo documento.

Cubre:
- Evaluación de `finances` como referencia real (con sus limitaciones concretas)
- Evaluación de `auth/autorización` como nuevo servicio (alcance, riesgos, bootstrapping)
- Acceso directo a BD: cuándo es válido y cómo controlarlo
- Guidelines obligatorias desde el inicio vs diferibles
- Objeciones previsibles con respuestas concretas
- Decisiones que deben cerrarse en la reunión

---

## Análisis

### 1. Qué partes de esta estrategia son sólidas

**Usar `finances` como referencia estructural es la decisión correcta.**
No porque sea perfecta, sino porque es la única implementación en producción que prueba que el patrón funciona dentro del stack real del ecosistema (Lambda + TypeScript + AWS). No es teoría. Cuando alguien diga "esto es demasiado complejo para una Lambda", se puede señalar `finances` y decir "ya está funcionando".

**Construir `auth` como servicio independiente tiene justificación técnica sólida que no depende de terminología arquitectónica:**
- El login actual tiene un problema de seguridad documentado y real: sin rate limiting, en un framework EOL desde 2019, en el mismo proceso que 90 controllers que pueden provocar un deploy en cualquier momento.
- Un parche de seguridad en auth no debería requerir regresión manual sobre facturas, anticipos y risk.
- "¿Quién es el owner de autenticación?" hoy no tiene respuesta clara. Está en `api/` junto a todo lo demás.

**"Empezar con casos reales, derivar estándares" es el enfoque correcto contra "definir estándares en abstracto y luego aplicarlos".** Los estándares que no tienen implementación de referencia visible no se siguen. Los que tienen una referencia tienen fricción de incumplimiento ("¿por qué el nuevo servicio no hace lo que hace finances?").

**La decisión de no dividir la BD todavía es correcta.** Se está proponiendo lo único que puede hacerse de forma segura en este estado del sistema.

**Permitir acceso directo a la BD de forma controlada es honesto.** Es mejor tener reglas explícitas para algo que inevitablemente va a ocurrir, que prohibirlo y que ocurra de todas formas pero sin documentación ni revisión.

---

### 2. Qué partes son débiles o pueden generar problemas

**`finances` no es un modelo completo de servicio con estado.**

Este es el punto más importante del análisis.

`finances` es una Lambda de procesamiento sin persistencia propia. Su "repositorio" principal es `InMemoryInvoiceRepository` — los datos viven en memoria durante la ejecución, no en una base de datos. Los datos de negocio los obtiene de `api/` (a través de `KintaiReceivableAdvanceDataProviderRepository` y `KintaiBookEntryDataProviderRepository`). No tiene endpoint HTTP propio. No tiene tablas propias.

`auth/autorización`, en cambio, es un servicio con estado persistente, con un modelo de datos complejo (usuarios, roles, permisos, claims, sesiones), con un HTTP API que otros servicios consumen, y con requisitos de disponibilidad superiores al 99.9%.

Si se dice "auth tiene que seguir el mismo patrón que `finances`" sin esta distinción, el equipo copiará la estructura de capas (correcto) pero esperará que auth también sea sin estado y sin persistencia propia (incorrecto). Hay que separar explícitamente: la *estructura de capas* de `finances` es la referencia; el *modelo de despliegue* no.

**El alcance de `auth` es demasiado amplio para una primera fase.**

La lista incluye: usuarios, roles, permisos, claims, service-to-service auth, auditoría, integración con proveedores externos. Eso no es un servicio: es una plataforma de identidad. Sin un scope boundary explícito y cerrado, `auth-service` se convierte en el nuevo cuello de botella: todo depende de él, nada puede desplegarse hasta que esté completo, y la primera versión tarda 6 meses.

La forma de debilitar esta objeción es con un milestone concreto y pequeño (ver sección de decisiones).

**`shared-context` de `finances` no puede convertirse en librería compartida sin un proceso de gobernanza.**

`finances/src/shared-context/` contiene 40+ value objects (UUID, Money, Email, DateValue, etc.). Si `auth-service` importa esa librería para no duplicar código, `shared-context` pasa a ser un shared kernel: cambiar cualquier cosa ahí requiere coordinación entre dos servicios. Y si un tercer servicio también lo usa, tenemos un shared kernel con tres propietarios.

Las opciones son: (a) cada servicio tiene su propia copia de los tipos base que necesita, (b) hay un paquete `@kintai/domain-primitives` con proceso de gobernanza explícito. La opción (a) es más segura en esta fase. El código duplicado de un `UUID` value object no es el problema que hay que resolver hoy.

**Las guidelines "de acceso a datos" necesitan una regla operacional, no un principio.**

"Es válido si el acceso está acotado y documentado" es un principio. Sin un proceso concreto (¿quién documenta? ¿dónde? ¿quién lo revisa?) el principio se convierte en "hacemos lo que queremos y luego añadimos un comentario".

---

### 3. Riesgos técnicos reales

**Riesgo 1: auth-service como nuevo single point of failure.**

Hoy, si `api/` falla, todo falla. Si `auth-service` reemplaza la autenticación y tiene un bug o una mala configuración, nada puede acceder al sistema. El riesgo no es teórico: es exactamente el mismo riesgo que se critica de `api/`, replicado en un servicio más pequeño pero con mayor impacto porque es la primera puerta.

Mitigación concreta: durante la transición, `api/` y `auth-service` coexisten. Los nuevos servicios usan `auth-service`. Los servicios existentes siguen usando `api/` para autenticación hasta la migración explícita. `auth-service` no puede ser la única puerta de acceso hasta que haya demostrado estabilidad en producción.

**Riesgo 2: validación de token — llamada remota vs validación local.**

Si cada Lambda que necesita verificar un JWT hace una llamada HTTP a `auth-service`, se añade una dependencia de red en el hot path de cada request. Con 20+ Lambdas, eso es 20+ fuentes de latencia adicional y 20+ puntos que fallan si `auth-service` tiene un problema de latencia.

La solución correcta es validación local con clave pública: `auth-service` firma los tokens con una clave privada y expone la clave pública. Cada Lambda verifica el JWT localmente sin llamada de red. Esta decisión debe tomarse antes de escribir una línea de código de `auth-service`, porque afecta la API completa del servicio.

**Riesgo 3: bootstrapping de service-to-service auth.**

Si `auth-service` es el que otorga credenciales de service-to-service, ¿cómo se autentica `auth-service` con sí mismo durante el bootstrap? Necesita una respuesta antes de diseñar el sistema. Opciones concretas: AWS IAM para service-to-service (los servicios autentican con roles IAM, no con `auth-service`), o API keys estáticas en SSM para comunicaciones internas durante la fase de transición. No hay una respuesta universalmente correcta, pero sí hay que elegir una antes de construir.

**Riesgo 4: migración de sesiones existentes.**

Cuando `auth-service` entre en producción, ¿qué pasa con los JWTs emitidos por `api/`? Tienen el mismo formato (probablemente), pero los secretos de firma son distintos. Hay dos opciones: período de validación dual (ambos servicios validan tokens durante N días) o logout forzado de todas las sesiones activas. En una plataforma fintech con usuarios activos, el logout forzado tiene impacto en producto. Esto no es una decisión técnica menor; necesita alineación con producto.

**Riesgo 5: `finances` no valida la arquitectura para servicios con estado complejo.**

La arquitectura de `finances` funciona bien porque el dominio es relativamente contenido: una factura tiene líneas, un cliente, una serie numérica. No hay agregados que se modifican concurrentemente, no hay flujos de estado complejos, no hay invariantes que cruzan entidades.

El dominio de auth es intrínsecamente más complejo: un usuario tiene roles que varían por empresa, permisos que se sobrescriben, sesiones activas en múltiples dispositivos. La arquitectura de `finances` es el punto de partida correcto, pero no garantiza que el diseño de `auth` sea sencillo. El equipo debe saber que la complejidad adicional es del dominio, no de la arquitectura.

---

### 4. Qué sobra o debe postponerse

**Integración con proveedores externos de identidad (Phase 0).**
Auth0, Cognito, SAML, OIDC: no son necesarios para que el sistema funcione. Si entran en el scope de Phase 0, triplican la complejidad sin entregar valor real para el equipo de desarrollo interno. Se posponen hasta que el modelo de autenticación propio esté estabilizado.

**Modularización de `api/` (bloqueante para finances/auth).**
Se menciona como opcional. Debe quedar explícitamente como: "se hace cuando se toca `api/` por otra razón, no como proyecto independiente, y no bloquea finances ni auth". Sin ese marco, alguien va a proponer hacerlo antes de empezar con auth "para tener la base limpia", y se habrán perdido 3 meses.

**Guidelines para patrones de eventos, read models, DLQs, circuit breakers (Phase 0).**
Estos patrones no son ejercitables en el trabajo real de Phase 0. Una guideline que no tiene implementación de referencia ni caso de uso inmediato es documentación cosmética. Se dejan para cuando un servicio real los necesite.

**Sistema de generación de proyectos o scaffolding.**
El planteamiento lo excluye explícitamente. Bien. Confirmarlo en la reunión evita que alguien lo proponga como "la primera tarea".

---

### 5. Cómo justificar esta estrategia sin terminología arquitectónica

El argumento que funciona en una reunión real no es "queremos DDD" o "queremos arquitectura hexagonal". Es:

**Sobre auth:**
> "El sistema actual de login está en un framework sin soporte desde 2019, sin rate limiting (documentado en el análisis de seguridad), y cualquier deploy de api/ puede afectar la autenticación aunque no hayamos tocado nada de auth. Estamos proponiendo sacarlo a un servicio independiente para que un parche de seguridad en auth no requiera regresión de todo el monolito, y para que cuando alguien pregunte '¿quién es responsable de auth?', haya una respuesta."

**Sobre finances como referencia:**
> "finances ya está en producción con esta estructura y funciona. No estamos proponiendo algo nuevo; estamos proponiendo que el siguiente servicio haga lo mismo que el que ya funciona."

**Sobre acceso a datos:**
> "El sistema actual ya tiene 20+ Lambdas accediendo a datos a través de api/. El problema no es que accedan; es que no hay ningún registro de qué accede a qué. Estamos proponiendo que los nuevos servicios documenten explícitamente qué datos usan y por qué, sea via api/ o directo."

**Sobre guidelines:**
> "No son un ejercicio de documentación. Son la forma de que el siguiente servicio que construya alguien que no estaba en esta reunión siga el mismo patrón. Sin guidelines mínimas, en 6 meses tenemos 3 servicios con 3 estructuras distintas."

---

### 6. Mínimo que debe definirse para que esto no sea otro sistema inconsistente

Estos seis elementos son no negociables. Sin ellos, finances y auth serán consistentes entre sí porque las construye el mismo equipo en el mismo mes. El tercer servicio, construido 4 meses después por alguien distinto, divergirá.

1. **Estructura de carpetas canónica** — un directorio de referencia que define dónde va cada tipo de código. No un documento: un repo vivo.

2. **Regla de dependencias entre capas** — expresada como test o lint rule, no como texto. Si el dominio importa de infraestructura, el pipeline falla.

3. **Patrón de composición** — cómo se conectan las implementaciones con las interfaces. `finances` usa funciones de composición (`createInvoiceBySourceTypeComposition`). Si auth usa un DI framework distinto, hay que una decisión explícita de por qué.

4. **Estrategia de validación de tokens** — local con clave pública vs llamada remota. Afecta la API de auth y la implementación de todos los consumidores. Se decide antes de escribir código.

5. **Regla para acceso directo a BD** — condiciones explícitas, proceso de documentación, y quién revisa. No un principio: un proceso.

6. **Milestone concreto de auth-service Phase 0** — qué operaciones cubre al finalizar, qué operaciones siguen en api/, y cuándo se considerará "listo para que nuevos servicios lo usen".

---

### 7. Guidelines obligatorias desde el inicio

Estas deben existir antes de que se escriba la primera línea de `auth-service`.

---

#### Estructura de proyecto

```
src/
  <context>/
    domain/
      entities/
      value-objects/
      interfaces/      ← puertos (interfaces de repositorios, servicios externos)
      errors/
    application/
      use-cases/
      commands/        ← entrada al caso de uso (primitivos, no entidades de dominio)
      responses/       ← salida del caso de uso (primitivos, no entidades de dominio)
    infrastructure/
      adapters/        ← implementaciones de los puertos del dominio
      handlers/        ← entrada real (Lambda handler, HTTP controller)
      composition/     ← construcción del árbol de dependencias
  shared/
    domain/            ← tipos base (sólo los estrictamente necesarios)
```

No se crean más carpetas de nivel sin decisión explícita.

---

#### Regla de dependencias (obligatoria como lint o test)

- `domain/` no importa de `application/`, `infrastructure/`, ni de ningún framework externo.
- `application/` importa solo de `domain/`. No importa tipos HTTP, de ORM, ni de AWS SDK.
- `infrastructure/` puede importar de `application/` y `domain/`. Es el único lugar donde entran las dependencias externas.

Un import `import { Request } from 'express'` en cualquier archivo fuera de `infrastructure/` rompe el build.

---

#### Contratos de entrada y salida de casos de uso

Los casos de uso reciben y devuelven primitivos de TypeScript (`string`, `number`, objetos planos). No reciben entidades de dominio ni tipos de infraestructura. No devuelven entidades de dominio.

```typescript
// Correcto
class AuthenticateUserUseCase {
  async execute(command: { email: string; password: string }): Promise<{ accessToken: string; refreshToken: string }>
}

// Incorrecto: el handler no debe construir la entidad User para pasársela al use case
class AuthenticateUserUseCase {
  async execute(command: User): Promise<AuthToken>
}
```

---

#### Interfaces antes que implementaciones

Cada puerto del dominio tiene su interfaz TypeScript definida en `domain/interfaces/` antes de que exista ninguna implementación. La implementación en `infrastructure/` implementa esa interfaz.

```typescript
// domain/interfaces/UserRepository.ts
export interface UserRepository {
  findByEmail(email: string): Promise<User | null>;
  save(user: User): Promise<void>;
}

// infrastructure/adapters/MysqlUserRepository.ts
export class MysqlUserRepository implements UserRepository { ... }
```

---

#### Testing mínimo

- Cada caso de uso tiene al menos un test unitario.
- Los tests unitarios no instancian ninguna clase de `infrastructure/`.
- Los tests crean instancias de dominio directamente, con datos en memoria.
- El pipeline CI rechaza un PR si la cobertura de use cases baja del umbral definido (a acordar, sugiero ≥80%).

No se exige coverage total. Se exige que la lógica de negocio sea testeable sin base de datos.

---

#### Logging estructurado

Todos los servicios usan una interfaz `ILogger` inyectada, no `console.log` directamente.

```typescript
// domain/interfaces/Logger.ts (o shared/domain/)
export interface ILogger {
  info(message: string, context?: Record<string, unknown>): void;
  error(message: string, error?: Error, context?: Record<string, unknown>): void;
  warn(message: string, context?: Record<string, unknown>): void;
}
```

La implementación real en `infrastructure/` puede ser `pino`, `winston`, o lo que decida el equipo. El dominio no sabe qué es.

Todo log de producción incluye `correlation_id`.

---

#### Correlation ID

El correlation ID se genera o se propaga en el handler de entrada (Lambda o HTTP). Se inyecta en el contexto de ejecución y llega al logger y a todos los servicios externos que se llaman.

```typescript
// infrastructure/handlers/loginHandler.ts
export const handler = async (event: APIGatewayEvent) => {
  const correlationId = event.headers['x-correlation-id'] ?? crypto.randomUUID();
  // Se propaga a todos los servicios downstream vía contexto o logger
};
```

No se genera correlation ID en casos de uso ni en el dominio.

---

#### Gestión de errores

- Los errores de dominio (invariante violada, entidad no encontrada, regla de negocio) se definen en `domain/errors/` y extienden una clase base `DomainError`.
- El caso de uso puede lanzar errores de dominio. No lanza errores HTTP.
- El handler de infraestructura es responsable de mapear errores de dominio a códigos HTTP o a mensajes de evento.
- Los errores de infraestructura (timeout de BD, fallo de red) no se dejan propagar sin wrapper hacia el dominio.

---

#### Naming

| Concepto | Convención | Ejemplo |
|---|---|---|
| Caso de uso | `<Verbo><Nombre>UseCase` | `AuthenticateUserUseCase` |
| Puerto (interfaz) | `<Nombre>Repository` o `<Nombre>Port` | `UserRepository`, `TokenSignerPort` |
| Adaptador | `<Proveedor><Nombre><Capacidad>` | `JwtTokenSigner`, `MysqlUserRepository` |
| Comando de entrada | `<Verbo><Nombre>Command` | `AuthenticateUserCommand` |
| Respuesta | `<Verbo><Nombre>Response` | `AuthenticateUserResponse` |
| Error de dominio | `<Nombre>Error` en `domain/errors/` | `InvalidCredentialsError`, `AccountLockedError` |

---

#### Lo que no está permitido

- Importar tipos de framework (`express`, `aws-lambda`, `typeorm`, `knex`) en `domain/` o `application/`.
- Hacer queries SQL o llamadas ORM en `domain/` o `application/`.
- Llamar `console.log`, `console.error` directamente — solo vía `ILogger`.
- Instanciar implementaciones concretas de infraestructura dentro de casos de uso.
- Exponer entidades de dominio fuera de la capa de aplicación (deben convertirse a primitivos con `toPrimitives()`).
- Añadir lógica de negocio en handlers de infraestructura.
- Usar `any` en contratos de entrada o salida de casos de uso.

---

### 8. Guidelines que deben esperar a una segunda fase

Estas se documentan como "pendientes" en las guidelines iniciales, no como opcionales. Hay que saber que no están.

- **Eventos de dominio**: cómo se publican, qué formato tienen, qué garantías de entrega. Depende de que haya más de un servicio productor/consumidor real.
- **Read models y sincronización por eventos**: hasta que exista un caso real donde un servicio necesite datos de otro con consistencia eventual.
- **Patrones de resiliencia** (circuit breaker, retry con backoff, DLQ): hasta que un servicio en producción los necesite.
- **API versioning**: hasta que un servicio tenga consumidores externos que no se pueden actualizar simultáneamente.
- **Contratos entre servicios**: gestión de versiones de OpenAPI o TypeScript contracts entre servicios. Después de que los primeros servicios estén estabilizados.
- **Shared kernel governance**: proceso formal para cambios en `shared/`. Hasta que haya más de un servicio usando la librería compartida.
- **Auditoría formal y compliance**: más allá del logging básico, modelos de auditoría para operaciones sensibles de auth. Segundo milestone de auth.

---

### 9. Objeciones técnicas previsibles y respuestas

---

**"Esto es over-engineering para un Lambda"**

Respuesta directa: `finances` ya está en producción con esta estructura. No es una propuesta teórica. El coste adicional de separar en capas en un servicio TypeScript es horas de setup inicial, no semanas. El beneficio es que cualquier regla de negocio puede testarse en milisegundos sin base de datos ni red. Si alguien tiene un ejemplo concreto de por qué esta estructura no funciona en `finances`, ese es el argumento a rebatir.

---

**"Construir auth desde cero es arriesgado. Usemos Auth0 o Cognito"**

Esta objeción es técnicamente legítima, no superficial. La respuesta correcta no es rechazarla:

Auth0/Cognito resuelven autenticación (verificar que el usuario es quien dice ser). No resuelven la autorización de Kintai: qué empresa puede ver este usuario, qué permisos tiene dentro de esa empresa, qué operaciones financieras puede ejecutar según su rol. Ese modelo vive en la BD de Kintai y tiene 4 tablas relacionadas (`roles`, `user_roles`, `permissions`, `role_permissions`) con lógica contextual compleja.

Si se usa Auth0/Cognito para autenticación, todavía se necesita un servicio interno que modele la autorización. El scope del servicio se reduce (no tiene que gestionar passwords, MFA, tokens), pero no desaparece.

La decisión no es "Auth0 vs construir todo desde cero". Es "¿resolvemos autenticación con un proveedor externo y construimos solo la autorización internamente?". Esa es una pregunta válida para la reunión.

---

**"El acceso directo a la BD desde servicios va a crear caos"**

La situación actual: 20+ Lambdas acceden a todos los datos a través de `api-client-nodejs`, una clase de 1.426 líneas sin interfaces. Si un Lambda necesita datos de companies, risk, y anticipos en la misma operación, hace tres llamadas a `api/` que hace tres queries SQL. El caos ya existe; no está documentado.

La propuesta no empeora eso. La introduce con: (a) ownership documentado, (b) conjunto de tablas acotado, (c) revisión explícita. Es más controlado que el estado actual.

Si la objeción es "el futuro será difícil de mantener", la respuesta es que todas las decisiones de acceso directo se documentan como transicionales, con el servicio propietario identificado. La deuda está registrada; no escondida.

---

**"¿Qué pasa con las sesiones activas cuando auth-service entre en producción?"**

Esta es una objeción operacional concreta, no teórica. Respuesta:

Durante la transición, `api/` sigue emitiendo y validando sus propios tokens. `auth-service` emite y valida los suyos. Los nuevos servicios (o los que migren explícitamente) usan `auth-service`. Los servicios no migrados siguen con `api/`. No hay momento de corte abrupto.

La migración de sesiones activas se planifica cuando un servicio migra, no en el lanzamiento de `auth-service`. El plan específico depende de si los tokens tienen formato compatible (probablemente sí, son JWT) y de si los secretos de firma son iguales (probablemente no).

---

**"¿Por qué no usar el `oauth/` existente como base?"**

`oauth/` corre sobre Node.js 14 EOL (sin soporte de seguridad), tiene 5 dependencias (ejs, hbs, config, fs) que sugieren una implementación de plantillas web, no un servicio de autenticación. No es una base: es un riesgo de seguridad documentado. Si la pregunta es "¿no podríamos arreglarlo?", la respuesta es que el coste de arreglar Node.js 14 + dependencias legacy + ausencia de arquitectura de capas es comparable al de construir bien desde cero, con la diferencia de que construir desde cero produce algo con tests y estructura clara.

---

**"¿Cómo evitamos que `auth-service` se convierta en el nuevo monolito?"**

El límite lo define el scope boundary: `auth-service` es responsable de autenticación y autorización. No es responsable de KYC, ni de notificaciones de seguridad, ni de gestión de empresas, ni de nada que no sea "¿puede este usuario hacer esto?". Cualquier funcionalidad que se proponga añadir se evalúa contra ese criterio. Si no es "determinar si alguien puede hacer algo", no es de auth.

El riesgo real es que con el tiempo la "lógica de negocio detrás de un permiso" (un usuario puede aprobar anticipos si su empresa tiene tipo X y su rol es Y) acabe siendo tan compleja que se convierta en toda la lógica de negocio de la plataforma. El antídoto es que los permisos representen *qué puede hacer*, no *bajo qué condiciones de negocio puede hacerlo*. Las condiciones de negocio son del dominio que ejecuta la operación.

---

### 10. Decisiones que deben cerrarse en la reunión

Estas no pueden salir como "por decidir". Cada una necesita un propietario y una respuesta.

---

**Decisión 1: Scope boundary de auth-service Phase 0**

¿Qué operaciones cubre la primera versión?

Propuesta mínima viable:
- `/login` — email + password → access token + refresh token
- `/refresh` — refresh token → nuevo access token
- `/validate` — token → claims (para validación interna, no de red)
- Modelo de usuarios, roles básicos (admin, internal, company) y permisos planos

Queda fuera de Phase 0: MFA, external IdPs, service-to-service tokens formales, permisos contextuales por empresa.

*Necesita: acuerdo explícito de qué entra y qué no.*

---

**Decisión 2: Estrategia de validación de tokens**

Opción A: Validación local con clave pública (recomendada)
- `auth-service` firma tokens con clave privada RSA/EC
- Expone un endpoint público de JWKS (`/.well-known/jwks.json`)
- Cada servicio descarga la clave pública al arrancar y verifica tokens localmente
- Sin llamada de red en el hot path
- Pros: sin SPOF en validación, latencia cero en el camino crítico
- Contras: revocación de tokens es eventual (hasta expiración del token), requiere gestión de claves

Opción B: Llamada remota a auth-service para validación
- Cada servicio llama a `auth-service` en cada request
- Pros: revocación inmediata, un punto de verdad
- Contras: SPOF, latencia adicional, `auth-service` necesita escalar con el tráfico total del sistema

*Necesita: decisión antes de escribir código. Afecta la API pública de auth y la implementación de todos los consumidores.*

---

**Decisión 3: `shared-context` — propiedad y gestión de cambios**

Opción A: Cada servicio tiene sus propios tipos base (UUID, Money, etc.) — sin librería compartida en Phase 0.
- Pros: sin acoplamiento, cada servicio evoluciona sin coordinación
- Contras: duplicación de ~200 líneas de value objects

Opción B: Paquete `@kintai/domain-primitives` publicado en npm privado, con proceso de gobernanza explícito.
- Pros: sin duplicación, coherencia entre servicios
- Contras: cambio en el paquete requiere coordinar releases, proceso de revisión

*Recomendación: Opción A en Phase 0. Opción B cuando haya más de dos servicios y el coste de sincronización sea visible.*

---

**Decisión 4: Relación con `oauth/` existente**

¿Es `auth-service` un reemplazo de `oauth/` o un sistema paralelo?

- Si reemplazo: plan de migración explícito. ¿Cuándo se desactiva `oauth/`? ¿Qué servicios lo consumen hoy?
- Si paralelo: ¿cuál es el criterio para que un nuevo servicio use uno u otro?

*Necesita respuesta. Tener dos repos con responsabilidades solapadas sin un plan es acumulación de deuda intencional.*

---

**Decisión 5: Regla operacional para acceso directo a BD**

Propuesta de regla:

Un servicio puede acceder directamente a tablas de la BD compartida si:
1. El conjunto de tablas está documentado en el README del servicio con el campo `db_access`.
2. El acceso es de lectura solamente, o la escritura es sobre tablas que el propio servicio gestiona.
3. El acceso ha sido revisado por un segundo miembro del equipo antes del primer deploy.
4. Está marcado como `TRANSITIONAL` si se espera que sea reemplazado por una llamada a un servicio propietario en el futuro.

¿Quién aprueba? ¿Dónde se documenta? ¿Cada PR o solo el primero por servicio?

*Necesita: aprobación del proceso propuesto o propuesta alternativa.*

---

**Decisión 6: `finances` como referencia estructural, no de despliegue**

Acordar explícitamente: lo que se toma de `finances` es la estructura de capas y el patrón de composición. Lo que no se toma: el modelo sin persistencia propia, la ausencia de HTTP API, y el deploy como Lambda sin estado de dominio.

Esto evita el malentendido de "¿auth-service es una Lambda o un servicio con DB?".

*Necesita: declaración explícita en el documento de arquitectura.*

---

## History

### 2026-05-04 — Primera ejecución

- Leída la estrategia propuesta con los puntos de finances, auth, acceso a datos y guidelines
- Revisada la estructura real de `finances` (Lambda de procesamiento sin persistencia propia, estructura hexagonal clara)
- Revisado el estado de `oauth/` (Node.js 14 EOL, dependencias legacy)
- Revisado el análisis previo de login y del modelo de identidad/acceso
- Generado análisis crítico completo con 10 secciones según lo solicitado
