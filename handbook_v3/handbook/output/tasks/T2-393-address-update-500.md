# Bug: T2-393 — 500 al actualizar dirección + campos en blanco

## Rama
`T2-393_modify_address_for_logalty` (api + dashboard)

## Síntomas
- Error 500 al actualizar campos de dirección desde el dashboard (merchant-information component)
- Al recargar, los campos de dirección aparecen en blanco

## Diagnóstico

### Bug 1 — `addresses.state` es NOT NULL sin default
**Fichero**: `api/database/migrations/1776243992071_addresses_schema.js`  
```js
table.string("state").notNullable(); // sin default
```
**Causa**: `extractAddressPayload` pone `state: payload.state`, pero el formulario del dashboard no tiene campo `state`, por lo que siempre es `undefined/null`. La inserción en la tabla `addresses` viola el constraint NOT NULL → 500 en cada actualización.

### Bug 2 — DELETE fuera de la transacción
**Ficheros**: `app/Models/Address.js`, `app/Models/MerchantAddress.js`, `providers/Services/Address/index.js`  
```js
// Patrón incorrecto para este codebase:
this.query(trx).where(...)
// Correcto:
this.query().transacting(trx).where(...)
```
**Causa**: Las operaciones DELETE en `_deleteCurrentAddresses` se ejecutan fuera de la transacción. Cuando el INSERT falla (por Bug 1) y la transacción hace rollback, los DELETE ya se han confirmado permanentemente → la dirección queda borrada sin reemplazo → campos en blanco al recargar.

## Fixes aplicados

### API
- `api/database/migrations/1777458486300_make_address_state_nullable_schema.js` — `state` nullable
- `api/database/migrations/1777458500000_partner_addresses_schema.js` — tabla `partner_addresses`
- `api/app/Models/Address.js` — `.query()` → `.query().transacting(trx)`
- `api/app/Models/MerchantAddress.js` — `.query()` → `.query().transacting(trx)`
- `api/app/Models/PartnerAddress.js` (NUEVO) — modelo pivot con `deleteByPartnerId`
- `api/app/Models/Partner.js` — relación `addresses` vía `partner_addresses`
- `api/providers/Services/Address/index.js` — fix fetch transaccional + `replacePartnerAddress`
- `api/providers/Services/Address/Provider.js` — inyecta `PartnerAddress`
- `api/providers/Services/Partner/index.js` — override `_update`/`_create` + `AddressService`
- `api/providers/Services/Partner/Provider.js` — inyecta `AddressService`
- `api/app/Validators/UpdatePartner.js` — nuevos campos de dirección estructurada
- `api/providers/Services/Partner/settings.json` — elimina campos planos de dirección
- `api/app/Controllers/Http/PartnerController.js` — `showWith/indexWith: ["addresses"]`

### Dashboard
- `partners/_models/partner.model.ts` — `addresses[]` en lugar de campos planos
- `partner-information.component.ts` — nuevos campos + `streetTypeOptions`/`numberTypeOptions` exportados
- `partner-information.component.html` — sección de dirección estructurada con ng-select
- `partner-information.component.spec.ts` — fixture actualizado, tests de address_line reemplazados
- `merchant-information.component.ts` — importa y expone opciones de dropdown
- `merchant-information.component.html` — `street_type`/`number_type` como ng-select
- `edit-merchant-modal.component.ts` — importa y expone opciones de dropdown
- `edit-merchant-modal.component.html` — `street_type`/`number_type` como ng-select
- `debtor-create.component.ts` — importa y expone opciones de dropdown
- `debtor-create.component.html` — `street_type`/`number_type` como ng-select
- `loan-create.component.ts` — importa y expone opciones de dropdown
- `loan-create.component.html` — `street_type`/`number_type` como ng-select
- `invoice-manual-upload.component.ts` — importa y expone opciones de dropdown
- `invoice-manual-upload.component.html` — `street_type`/`number_type` como ng-select

## Pendiente de verificar
- Ejecutar migraciones en el entorno de pruebas
- Probar flujo de actualización de dirección en merchants, partners, debtors, loans y factoring
- Verificar que rollback funciona si falla el INSERT (dirección preexistente no se borra)

## Bug 3 — Formulario muestra campos de dirección vacíos en merchants existentes

### Causa raíz
Los merchants creados antes de T2-393 tienen dirección en las columnas planas (`address_line_1`, `city`, etc.) de la tabla `merchants`, pero NO en `merchant_addresses`. El componente `merchant-information.component.ts` lee desde `merchant.addresses?.[0]?.street_type` (estructura nueva), por lo que devuelve `undefined` para datos históricos.

### Diagnóstico adicional (staging)
La llamada a `GET https://api-staging.kintai.com/merchants/:id` no devuelve `addresses` en absoluto. Confirmado con token Bearer. Evidencia: la respuesta incluye `subscriptions: []` (siempre cargado) pero no `addresses`. Causa: el staging API corre código anterior a T2-393 (`showWith = ["subscriptions"]`), el despliegue del API no se ha completado correctamente.

### Fix aplicado
- `api/database/migrations/1777458520000_migrate_merchant_flat_addresses_schema.js` (NUEVO) — migración de datos que toma los campos planos de merchants existentes (`address_line_1`, `city`, `post_code`, `country`) y crea entradas en `addresses` + `merchant_addresses`. Solo migra merchants con `country` NOT NULL que no tengan ya una entrada en `merchant_addresses`.

### Pendiente
- Verificar que el despliegue del API a staging incluye los cambios de T2-393 en `MerchantController` (el `showWith = ["subscriptions", "addresses"]`)
- Ejecutar `node ace migration:run` en staging para aplicar la nueva migración de datos
- Verificar formulario merchant-information en staging después del despliegue correcto

## Bug 4 — Desplazamiento visual de campos de domicilio en ficha merchant

### Causa raíz
`merchant-information.component.html` usa `<app-address-form [formGroup]="formGroup"></app-address-form>` para los campos nuevos de domicilio, pero la ficha merchant mantiene `Código Postal`, `Ciudad` y `País` con el layout histórico de ficha horizontal (`row` + label/input por columnas).

`app-address-form.component.html` está diseñado como grid compacto con columnas `col-md-*` y labels encima del input. Ese patrón encaja en formularios de creación/modal, pero no en la ficha merchant, donde los demás campos usan label a la izquierda e input a la derecha.

### Alcance
- Es un problema de dashboard/layout, no de API ni de datos.
- Se ve sólo en merchant porque partner no usa `app-address-form`; mantiene todos los campos de domicilio escritos inline con el layout horizontal.
- Tocar `app-address-form` globalmente podría afectar debtor, edit-merchant-modal, loan-create y factoring, donde el grid compacto parece intencional.

### Recomendación
Aplicar un fix local en `merchant-information`: reemplazar el uso de `app-address-form` por el mismo layout horizontal que usa partner, o parametrizar `app-address-form` con un modo `profile` y usarlo sólo en la ficha merchant.

### Fix aplicado
- `dashboard/src/app/modules/merchants/merchant-edit/company-information/merchant-information/merchant-information.component.html` — reemplazado `app-address-form` por filas horizontales locales para los campos de domicilio nuevos.
- `dashboard/src/app/modules/merchants/merchant-edit/company-information/merchant-information/merchant-information.component.ts` — expone `streetTypeOptions` y `numberTypeOptions`.
- `dashboard/src/app/modules/shared/address-form/address-options.ts` (NUEVO) — opciones compartidas de tipo de vía y tipo de número.
- `dashboard/src/app/modules/shared/address-form/address-form.component.ts` y `dashboard/src/app/modules/partners/partner-edit/partner-information/partner-information.component.ts` — consumen las opciones desde el archivo compartido para evitar dependencia de shared/merchant hacia partner; limpiado parámetro `err` sin uso en el handler de error de partner.

### Verificación
- `npx ng build --configuration staging --aot` — OK.
- `npx eslint src/app/modules/merchants/merchant-edit/company-information/merchant-information/merchant-information.component.ts src/app/modules/partners/partner-edit/partner-information/partner-information.component.ts src/app/modules/shared/address-form/address-form.component.ts src/app/modules/shared/address-form/address-options.ts` — OK.
- `npx ng test --watch=false --browsers=ChromeHeadless --include=src/app/modules/merchants/merchant-edit/company-information/merchant-information/merchant-information.component.spec.ts` — el bundle compila, pero Karma no puede arrancar porque no existe Google Chrome en `/Applications/Google Chrome.app/Contents/MacOS/Google Chrome`.
- `npx ng test --watch=false --browsers=FirefoxHeadless --include=src/app/modules/merchants/merchant-edit/company-information/merchant-information/merchant-information.component.spec.ts` — el bundle compila, pero Karma no puede arrancar porque `FirefoxHeadless` no está registrado; falta `karma-firefox-launcher` en el proyecto.

## Bug 5 — `checks.information` sigue false con dirección en `merchant_addresses`

### Diagnóstico
En la rama T2-393 de `workspace/meta-all/api`, `MerchantService.getChecks` ya carga `company.addresses` y calcula `information` con:

```js
!!company.email &&
!!company.name &&
!!companyAddress?.street &&
!!companyAddress?.city &&
!!companyAddress?.post_code &&
!!companyAddress?.country
```

La consulta sobre `addresses` + `merchant_addresses` sólo valida la parte de dirección. Para el merchant `35849`, la fila indicada sí contiene `street`, `city`, `post_code` y `country`; si el endpoint devuelve `information: false`, las causas probables son:

- `merchants.email` o `merchants.name` está vacío/nulo.
- El API desplegado en `api.kintai.com` no está ejecutando el código T2-393 y todavía usa la lógica anterior, que revisa columnas planas de `merchants` (`address_line_1`, `city`, `post_code`, `country`) e ignora `merchant_addresses`.

### Pendiente
- Comprobar en producción `SELECT id, name, email, address_line_1, city, post_code, country FROM merchants WHERE id = 35849;`.
- Confirmar si el código desplegado de API contiene el cambio de `getChecks` que carga `addresses`.

## Current State
✅ Implementación completa en todos los módulos: merchants, partners, debtors, loans, factoring.
`street_type` y `number_type` son ng-select en todos los formularios.
Todos los tests unitarios y de integración corregidos.
✅ Migración de datos para merchants históricos creada.
⚠️ Staging API pendiente de redespliegue correcto (código T2-393 no reflejado).
✅ Bug visual de dashboard merchant-information corregido localmente: el bloque de domicilio usa layout horizontal uniforme.
✅ Scripts manuales para producción preparados en `workspace/meta-all/api/sql_chunks/para_nico/`:
- `migrate_merchant_addresses.sql` consolida chunks de `addresses` y `merchant_addresses`.
- `migrate_partner_addresses.sql` consolida `addresses` y `partner_addresses`.
✅ Dashboard: corregidos fallos de pipeline tras merge con master en specs Angular. `CompanySetupComponent` declara `WizardStepDirective` en su TestBed, y los specs que importan `SharedModule`/`CRUDTableModule`/`InlineSVGModule` sin `HttpClient` ahora usan `provideHttpClient(...)` + `provideHttpClientTesting()`. El bundle completo de tests compila localmente; Karma no puede ejecutar porque no hay Chrome local en `/Applications/Google Chrome.app/Contents/MacOS/Google Chrome`.
⚠️ `checks.information` puede seguir devolviendo `false` aunque exista `merchant_addresses`: la lógica exige también `company.email` y `company.name`, y en un despliegue sin T2-393 todavía se miran las columnas planas de `merchants`.
✅ Confirmado por equipo: el falso `information: false` observado en producción se debía a que la instancia tardó en levantarse con el código nuevo.

## History
- 2026-04-29: Diagnóstico y fix de bugs 1 y 2 implementado (merchants)
- 2026-04-30: Implementación partner (API + dashboard), dropdowns en todos los módulos
- 2026-04-30: Fix tests de integración — `replacePartnerAddress` stubbed en before hook; eliminados errores de validación de `address_line_1`/`address_line_2` del test PATCH (UpdatePartner validator ya no incluye esos campos)
- 2026-04-30: Diagnóstico bug 3 — merchants existentes sin `merchant_addresses`. Staging API corre código viejo (verificado con Bearer token). Creada migración de datos `1777458520000`.
- 2026-05-06: Diagnóstico bug visual en ficha merchant — los campos de domicilio nuevos usan `app-address-form` con grid `col-md-*`, mientras CP/Ciudad/País siguen con filas horizontales. Recomendado fix local o variante `profile` del componente compartido.
- 2026-05-06: Fix bug visual aplicado en dashboard — `merchant-information` deja de usar `app-address-form` para la ficha y usa filas horizontales locales. Opciones de dirección movidas a shared. Build staging AOT correcto; test unitario no ejecutable con Chrome por falta de Chrome local ni con Firefox por falta de `karma-firefox-launcher`.
- 2026-05-06: Preparados scripts SQL manuales en `workspace/meta-all/api/sql_chunks/para_nico/` para pasar a producción sin añadir una migración de inserción de datos al proyecto. El script de merchants incluye 30 chunks de `addresses` y 42 chunks de `merchant_addresses`; el de partners incluye 82 inserciones de `addresses` y 82 de `partner_addresses`.
- 2026-05-06: Corregida la entrega de SQL para Nico: los chunks originales contenían un corte inválido en la fila `36085` (`de Leon-Carrizo km. 8.5 (LE-441)`). `migrate_merchant_addresses.sql` se sustituyó por la versión raíz correcta `workspace/meta-all/api/migrate_addresses.sql`. La entrega queda únicamente en dos SQL: merchants y partners.
- 2026-05-06: Fix pipeline dashboard tras merge con master: el spec de `CompanySetupComponent` ya declara `WizardStepDirective` para evitar `NG0303` sobre `[subtitle]`/`[nextStepText]`; añadidos proveedores de `HttpClient` de testing a specs que renderizan módulos con `InlineSVGModule` para evitar `NullInjectorError: No provider for HttpClient`. Verificado `npx eslint` sobre specs tocados y compilación completa del bundle de `ng test`; ejecución local bloqueada por ausencia de Chrome.
- 2026-05-07: Revisado `/companies/:id/checks` para merchant `35849`: en T2-393 `information` usa `email`, `name` y la primera dirección relacionada (`street`, `city`, `post_code`, `country`). La fila de `addresses` aportada cubre la dirección, pero no prueba `email/name`; además, si producción no tiene el despliegue T2-393, seguirá ignorando `merchant_addresses`.
- 2026-05-07: Confirmado por equipo que la instancia de producción tardó en levantarse con el código nuevo; el `information: false` era consistente con una instancia vieja todavía sirviendo la lógica anterior.
