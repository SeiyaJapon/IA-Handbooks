# Work Record — Holded: Warning para clientes nuevos sin configuración contable

**Caso:** Facturación — Validación y warning para clientes nuevos sin configuración contable en Holded  
**Estado:** Implementado; correcciones post-verificación aplicadas en `finances`, `api` y `dashboard` — pendiente de commit/redeploy  
**Repos afectados:** `finances`, `api`, `dashboard`

---

## Current State

Implementación completa en branch `T2-416_warning_new_contacts_on_holded` en los tres repos.

El 2026-05-06 se detectó en staging que el caso de contacto Holded sin configuración contable devolvía 500 sin JSON desde `api`. El log de CloudWatch mostraba que `HoldedContactNeedsAccountingSetupError` salía como `Invoke Error` de Lambda, por lo que el error de negocio no estaba llegando como payload estructurado.

Corrección aplicada:
- `finances`: `HoldedContactNeedsAccountingSetupError` se reemplaza por `InvoiceProviderContactNeedsAccountingSetupError` en capa de aplicación.
- `finances`: `createInvoiceFromSourceHandler` captura el error de aplicación y reconoce también `name/contactId`, para no depender solo de identidad de clase.
- `finances`: corregido tipo `MatchResult["matchedBy"]` que rompía `npm run compile`.
- `api`: `InvoiceProviderPublisher` reconoce `HOLDED_CONTACT_NEEDS_ACCOUNTING_SETUP` tanto en payload estructurado como en `FunctionError` de Lambda.
- `api`: la respuesta 422 incluye `errorCode` además de `error`, y el validator acepta `force` boolean opcional.

Verificación:
- `finances`: tests focalizados de handler/error OK.
- `finances`: `npm run compile` OK.
- `api`: unit test del publisher OK.
- `api`: functional test del controller OK ejecutado fuera del sandbox para poder conectar con MySQL local.
- `dashboard`: el 422 `HOLDED_CONTACT_NEEDS_ACCOUNTING_SETUP` ya no cae al alert genérico; abre modal de warning y "Continuar" reintenta con `force: true`.
- `dashboard`: tests focalizados OK y `npx ng build` OK.

### Decisiones finales adoptadas (vs diseño inicial)
- **Flow basado en excepción** (no retorno de flags): `HoldedService` traduce el caso Holded a `InvoiceProviderContactNeedsAccountingSetupError`; el handler captura ese error de aplicación y retorna `{ ok: false, errorCode }`. La API responde 422.
- **`force` flag** (boolean) propagado de extremo a extremo para que "Continuar" omita el check de 430.
- **Estado en sesión** (`pendingAccountingSetupIds: Set`) en `receivables-advances.service.ts`: no hay persistencia en BD.
- **Campo contable en Holded**: se asume `accountNum === "430000000"` para detectar cuenta genérica. TODO: verificar nombre exacto del campo contra la API real.

### Archivos modificados / creados

**`finances`** (branch con cambios post-commit pendientes):
- `InvoiceProviderContactNeedsAccountingSetupError.ts` (nuevo error de aplicación)
- `contacts.ts`: retorna `{ contactId, needsAccountingConfig }`
- `HoldedService.ts`: lanza error si `needsAccountingConfig && !force`
- `createInvoiceFromSourceHandler.ts`: captura el error, retorna 422-style payload
- `parseEvent.ts`, `CreateInvoiceBySourceTypeInput.ts`: añaden `force`
- `InvoiceProviderInterface.ts`, `HoldedInvoiceProviderRepository.ts`: propagan `force`
- `CreateInvoiceFromReceivableAdvanceService.ts`, `CreateInvoiceFromBookEntryService.ts`: propagan `force`
- `ReceivableAdvanceInvoiceCreationStrategy.ts`, `BookEntryInvoiceCreationStrategy.ts`: propagan `force`
- Tests: todos actualizados

**`api`** (branch commiteado, 3215 tests passing):
- `InvoiceProviderController.js`: lee `force` del body, responde 422 con errorCode
- `providers/Services/InvoiceProviderPublisher/index.js`: incluye `force` en payload Lambda
- `test/unit/invoice-provider-publisher.service.spec.js`: payload actualizado

**`dashboard`** (branch con cambios post-verificación pendientes):
- `ConfirmDialogComponent`: reutilizado para el warning de configuración contable Holded
- `es.ts`, `en.ts`: claves `HOLDED_ACCOUNTING_WARNING_*`
- `customer-invoice.interface.ts`, `customer-invoice-http.service.ts`, `customer-invoice.service.ts`: añaden `force`
- `receivables-advances.service.ts`: maneja 422, abre modal, reintenta con force, marca botón warning
- `obligation-entries-list.component.ts`: misma lógica de modal para book entries

---

## Flujo actual (end-to-end)

### Trigger en dashboard
El botón "Generar factura de anticipo" está en la lista de acciones de row de `receivable-advances`:

```
receivable-advances.service.ts → actionMap.publishToProvider()
  → customerInvoiceService.createReceivableAdvanceInvoice(advance)
  → customerInvoiceHttpService.createInvoiceFromEntity(entity, 'receivable-advance')
  → POST /invoices/{id}/publish-to-provider  { source_type: 'receivable-advance' }
```

Para book entries el trigger está en `obligation-entries-list.component.ts → createBookEntryInvoice()`, que llama al mismo servicio con `SourceType.BOOK_ENTRY`.

### API (AdonisJS)
```
InvoiceProviderController.publishToProvider()
  → InvoiceProviderPublisherService.publishToProvider({ id, sourceType })
  → LambdaClient.invoke('publishInvoiceToProvider', { id, sourceType })
  → Responde 201 { ok: true, id, source_type }  ← ignora el resultado del Lambda
```

### Lambda finances (TypeScript hexagonal)
```
createInvoiceFromSourceHandler → CreateInvoiceBySourceTypeUsecase.execute()
  → InvoiceCreationStrategySelector → Strategy (ReceivableAdvance | BookEntry)
  → CreateInvoiceFromXxxService → HoldedInvoiceProviderRepository.send(invoice)
  → HoldedService.createInvoice(invoice)
    → findOrCreateContactId()   ← aquí se crea el contacto si no existe
    → buildInvoicePayload()
    → POST /documents/invoice   ← aquí se crea la factura en Holded
```

### El gap actual
`findOrCreateContactId()` en `contacts.ts` devuelve `Promise<string | undefined>` — solo el ID.  
No hay indicación de si el contacto fue encontrado o creado.  
La API ignora el resultado del Lambda y siempre responde `{ ok: true }`.  
El dashboard no tiene información sobre el estado del contacto en Holded.

---

## Diseño de la solución

### Flujo nuevo propuesto

```
1. Usuario hace clic en "Crear factura"
2. → POST /invoices/{id}/publish-to-provider
3. Lambda: findOrCreateContactId()
   a. Si contacto NO existe → crea contacto en Holded, NO crea la factura
      → retorna { invoiceCreated: false, newContactCreated: true }
   b. Si contacto existe con 430000000 genérico → NO crea la factura
      → retorna { invoiceCreated: false, newContactCreated: false, needsAccountingConfig: true }
   c. Si contacto existe con cuenta específica → crea la factura normalmente
      → retorna { invoiceCreated: true }
4. API propaga el resultado al dashboard
5. Dashboard:
   - Si invoiceCreated: false → muestra modal
   - "Continuar" → llama de nuevo con force: true → crea factura (imputada a 430 genérico)
   - "Cancelar" → cierra modal, sin factura creada
```

**Pregunta abierta clave:** ¿La API de Holded devuelve la cuenta contable del contacto en GET /contacts?  
Esto determina si el punto 3b es implementable directamente o necesita otro mecanismo.  
Ver sección de preguntas abiertas.

---

## Cambios por repositorio

### `finances`

#### `contacts.ts`
**Qué cambia:** `findOrCreateContactId()` devuelve `{ contactId, isNewContact, needsAccountingConfig }` en lugar de `string | undefined`.

```typescript
// Antes
export async function findOrCreateContactId(...): Promise<string | undefined>

// Después
export type ContactResult = {
  contactId: string | undefined;
  isNewContact: boolean;
  needsAccountingConfig: boolean;  // true si 430000000 genérico (requiere verificar API Holded)
};
export async function findOrCreateContactId(...): Promise<ContactResult>
```

Cuando se crea: `isNewContact: true, needsAccountingConfig: true`.  
Cuando se encuentra: `isNewContact: false, needsAccountingConfig` depende de la cuenta contable.

#### `HoldedService.ts`
**Qué cambia:** `createInvoice()` devuelve `Promise<{ invoiceCreated: boolean; needsAccountingConfig: boolean }>`.

- Si `contactResult.needsAccountingConfig` es true y no hay flag `force`: no llama a POST /documents/invoice, devuelve `{ invoiceCreated: false, needsAccountingConfig: true }`.
- Si `force: true` o `needsAccountingConfig: false`: crea la factura, devuelve `{ invoiceCreated: true, needsAccountingConfig: false }`.

Firma actualizada:
```typescript
async createInvoice(invoice: Invoice, options?: { force?: boolean }): Promise<{ invoiceCreated: boolean; needsAccountingConfig: boolean }>
```

#### `InvoiceProviderInterface.ts` (dominio)
**Qué cambia:** Puerto actualizado.
```typescript
// Antes
send(invoice: Invoice): Promise<void>

// Después
send(invoice: Invoice, options?: { force?: boolean }): Promise<{ invoiceCreated: boolean; needsAccountingConfig: boolean }>
```

#### `HoldedInvoiceProviderRepository.ts`
**Qué cambia:** Implementa el nuevo contrato del puerto.

#### Cadena de propagación en capa de aplicación
Todos los servicios y use cases que hoy devuelven `void` necesitan propagar el resultado:
- `CreateInvoiceFromReceivableAdvanceService`
- `CreateInvoiceFromBookEntryService`
- `BookEntryInvoiceCreationStrategy`
- `ReceivableAdvanceInvoiceCreationStrategy`
- `CreateInvoiceBySourceTypeUsecase` → cambia de `Promise<void>` a `Promise<{ invoiceCreated: boolean; needsAccountingConfig: boolean }>`

#### `createInvoiceFromSourceHandler.ts`
**Qué cambia:** El Lambda retorna el resultado enriquecido.
```typescript
// Antes
return { ok: true, created: true, sourceType, id }

// Después
return { ok: true, invoiceCreated: result.invoiceCreated, needsAccountingConfig: result.needsAccountingConfig, sourceType, id }
```

También acepta `force` en el evento de entrada y lo propaga.

---

### `api`

#### `InvoiceProviderController.js`
**Qué cambia:** Lee y expone el resultado del Lambda.
```javascript
// Antes
await this.service.publishToProvider({ id, sourceType });
return response.status(201).json({ ok: true, id, source_type: request.body.source_type });

// Después
const result = await this.service.publishToProvider({ id, sourceType, force: request.body.force });
return response.status(201).json({
  ok: true,
  id,
  source_type: request.body.source_type,
  invoice_created: result.invoiceCreated,
  needs_accounting_config: result.needsAccountingConfig
});
```

#### `InvoiceProviderPublisher/index.js`
**Qué cambia:** Pasa el flag `force` al payload del Lambda.
```javascript
payload: JSON.stringify({ id, sourceType, force: options?.force ?? false })
```

Ya retorna el payload completo del Lambda — sin cambios adicionales.

#### Validator `PublishInvoiceToProvider`
**Qué cambia:** Aceptar `force` (boolean, opcional) como campo válido del body.

---

### `dashboard`

#### `customer-invoice-http.service.ts`
**Qué cambia:** Tipo de retorno actualizado.
```typescript
// Antes
createInvoiceFromEntity(entity, sourceType): Observable<void>

// Después
createInvoiceFromEntity(entity, sourceType, force?: boolean): Observable<{
  invoice_created: boolean;
  needs_accounting_config: boolean;
}>
```

#### `customer-invoice.service.ts`
**Qué cambia:** Ambos métodos actualizan su tipo de retorno y aceptan `force`.

#### `receivables-advances.service.ts`
**Qué cambia:** El método `publishToProvider()` maneja el resultado.

Lógica nueva:
```
createReceivableAdvanceInvoice(advance)
  → si needs_accounting_config: true → abre modal warning
     → "Continuar" → createReceivableAdvanceInvoice(advance, force=true)
     → "Cancelar" → cierra modal
  → si invoice_created: true → alertService.simpleSuccess()
```

#### `obligation-entries-list.component.ts`
**Qué cambia:** `createBookEntryInvoice()` añade la misma lógica de modal.

#### Nuevo componente modal
**Nombre sugerido:** `HoldedAccountingWarningModalComponent`  
**Ubicación:** `customer-invoice/` o en `shared/`

Contenido según spec UX:
- Título: "Cliente creado en Holded."
- Cuerpo: "Completar configuración contable en Holded y luego volver a Plataforma para emitir la factura. Si continuás ahora, se imputará la contabilidad sin relación con el cliente"
- Botones: "Continuar" / "Cancelar"

#### i18n (`es.ts` y `en.ts`)
Claves nuevas bajo `INVOICE`:
```
HOLDED_ACCOUNTING_WARNING_TITLE
HOLDED_ACCOUNTING_WARNING_BODY
HOLDED_ACCOUNTING_WARNING_CONFIRM_BUTTON
```

---

## Preguntas abiertas

### P1 — ¿La API de Holded expone la cuenta contable del contacto?
**Impacto:** Determina si el check "430000000" (escenario 2 del UX) es implementable directamente.

Si GET /contacts devuelve un campo como `accountingAccount: "430000000"`:
→ implementar el check en `findOrCreateContactId()` cuando el contacto ya existe.

Si NO devuelve esta información:
→ Opciones alternativas:
  a. Simplificar: el modal solo aparece cuando `isNewContact: true` (primera vez). El check posterior no existe.  
  b. GET /contacts/{id} para obtener detalle completo del contacto.  
  c. Tabla propia en Kintai: guardar flag `holded_accounting_configured: boolean` por empresa, actualizable manualmente.

**Esta pregunta debe responderse antes de implementar.** La opción más simple sería (a) si el UX puede aceptar que solo aparezca la primera vez.

### P2 — Scope del flag `force`
¿El usuario puede hacer clic en "Continuar" en el modal y forzar la creación de la factura con 430 genérico? Según el UX: sí. El flag `force` es necesario.

### P3 — ¿Aplica a book entries también?
La descripción habla de "clientes" en general. Confirmar si el mismo warning aplica al flujo de book entries (`obligation-entries-list`).

---

## History

### 2026-05-05 — Estudio inicial
- Explorados repos `finances`, `api`, `dashboard`
- Trazado el flujo completo end-to-end
- Identificados todos los archivos a modificar
- Documentadas preguntas abiertas
- Sin código modificado

### 2026-05-05 — Implementación completa
- Creadas ramas `T2-416_warning_new_contacts_on_holded` en los tres repos
- Implementada la cadena completa: HoldedService → handler → API → dashboard
- 72 tests fallidos en `finances` por el parámetro `force` nuevo → corregidos por subagente
- Commit en `finances`: 574/574 tests passing
- Commit en `api`: 3215/3215 tests passing
- Commit en `dashboard`: sin test suite de componentes pero compilación limpia

### 2026-05-06 — Corrección 500 API/Lambda en staging
- Analizado Postman + CloudWatch: `HoldedContactNeedsAccountingSetupError` aparecía como `Invoke Error`, por lo que `api` devolvía 500 sin JSON.
- Causa técnica principal: error custom de TypeScript compilado a ES5 sin `Object.setPrototypeOf`, haciendo frágil el `instanceof` del handler.
- Endurecido `finances` para capturar el error por prototype correcto y por fallback `name/contactId`.
- Endurecido `api` para mapear el error conocido aunque llegue como `FunctionError` de Lambda.
- Añadidos tests de regresión en ambos repos.

### 2026-05-06 — Mejora arquitectónica del error capturado por el handler
- Sustituido `HoldedContactNeedsAccountingSetupError` por `InvoiceProviderContactNeedsAccountingSetupError` en capa de aplicación.
- `HoldedService` traduce el detalle de proveedor a un error semántico del caso de uso.
- `createInvoiceFromSourceHandler` ya no importa un error específico de infraestructura Holded.
- Eliminado el error específico `HoldedContactNeedsAccountingSetupError` al quedar sin uso.
- Verificación: tests focalizados de error/handler/HoldedService OK y `npm run compile` OK.

### 2026-05-07 — Dashboard local no reacciona al 422 de staging
- Revisados pantallazos: la petición `POST /invoices/1244/publish-to-provider` devuelve 422 JSON, pero dashboard muestra `ACTIONS.SAVED_ERROR`.
- Causa: la rama local de dashboard no tenía la implementación T2-416; `publishToProvider()` trataba cualquier error como genérico y `CustomerInvoiceService` no propagaba `force`.
- Añadido detector `isHoldedAccountingSetupError()` para reconocer `errorCode`, `error`, `code` o payload stringificado en 422.
- `receivables-advances.service.ts` y `obligation-entries-list.component.ts` abren `ConfirmDialogComponent` con copy específico y reintentan con `force: true` si el usuario confirma.
- `customer-invoice-http.service.ts`, fake e interfaz propagan `force`.
- Verificación: 57 tests focalizados OK con coverage desactivado; primera ejecución con coverage pasó tests pero falló por umbrales globales; `npx ng build` OK.
