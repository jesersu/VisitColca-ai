---
name: constitution-guard
description: "Aplica los principios no negociables de VisitColca al escribir o revisar código sensible. Trigger: pagos, payments, Culqi, Niubiz, tarjeta, card data, CVV, checkout, pasarela, autenticación, auth, Supabase, JWT, rol, role, require_admin, endpoint de escritura, admin, reserva, booking, base de datos, database, nueva dependencia, new dependency, SEO, SSR."
metadata:
  source: specs/constitution.md
  version: "1.0"
---

## Cuándo se aplica

Al escribir, revisar o proponer código que toque:

- Pagos o la pasarela (Culqi / Niubiz)
- Autenticación o autorización
- Endpoints de escritura del backend
- Estados de una reserva
- La base de datos, o el agregado de una dependencia nueva

La fuente de verdad es `specs/constitution.md`. Este archivo es su **versión operativa**:
no repite los principios, los convierte en verificaciones.

---

## Verificaciones

### Pagos

- [ ] Ningún campo de tarjeta (número, CVV, vencimiento, titular) aparece en un modelo,
      un request, un log, una tabla ni una variable.
- [ ] El backend guarda únicamente la referencia de transacción que devuelve la pasarela.
- [ ] Ningún endpoint de pago está expuesto sin autenticación.

**Si el código propuesto recibe datos de tarjeta en el backend propio: PARAR.** No es
algo a corregir más adelante. Explicar que eso mete al proyecto en scope PCI-DSS completo
y proponer el checkout tokenizado de la pasarela.

### Autorización

- [ ] Todo endpoint `POST` / `PUT` / `DELETE` sobre tours, posts o usuarios valida el rol
      en el backend con `Depends(require_admin)`.
- [ ] La verificación en el cliente (middleware de Next.js, estado de sesión en iOS o
      Android) nunca es la única barrera: es UX.

**Señal de alarma:** un endpoint de escritura sin `Depends(require_admin)`, o cualquier
justificación con la forma "ya lo valida el frontend".

### Datos

- [ ] Una sola base de datos relacional: PostgreSQL. No se introduce NoSQL, Firestore ni
      Redis como fuente de verdad sin justificación técnica explícita y documentada.
- [ ] El rol y el perfil del usuario viven en la tabla `users`, enlazados al `user_id` de
      Supabase Auth. No se duplican usuarios entre sistemas.
- [ ] Los estados de una reserva (`pendiente` / `pagado` / `cancelado`) son la única fuente
      de verdad del flujo de compra.

### Clientes

- [ ] El backend es client-agnostic: no se agregan endpoints ni campos a medida de la
      pantalla de un cliente concreto.
- [ ] Ningún cliente reimplementa reglas de negocio (precios, cupos, transiciones de estado).
- [ ] El catálogo y el blog del cliente web usan SSR/SSG. El SEO es requisito de negocio.
- [ ] Las apps móviles son nativas: no se introduce React Native, Flutter ni KMP.

### Simplicidad

- [ ] Antes de agregar una dependencia, verificar qué ofrece ya el stack elegido
      (Next.js, FastAPI, PostgreSQL, Supabase Auth, Swift/SwiftUI).
- [ ] Una capa de abstracción nueva exige un problema concreto **ya identificado**,
      no uno anticipado.

---

## Decisiones cerradas (no reabrir sin razón técnica nueva)

| Decisión | Motivo |
|---|---|
| PostgreSQL sobre MySQL / SQL Server | Mejor soporte JSON y full-text; ecosistema con FastAPI |
| Supabase Auth sobre Firebase Auth | Evita duplicar usuarios en dos bases distintas |
| Culqi / Niubiz sobre Stripe | Stripe no opera pagos para comercios peruanos |
| iOS → Android → web | El cliente web es el último, no el primero |
| Nativo sobre multiplataforma | El objetivo incluye aprender cada stack |

Si una propuesta reabre alguna de estas, decirlo explícitamente antes de implementarla.

---

## Cómo responder ante una violación

1. **Nombrar el principio** que se rompe, citando `specs/constitution.md`.
2. **Explicar la consecuencia técnica concreta**, no la regla en abstracto.
   Decir "guardar el número de tarjeta mete al backend en scope PCI-DSS completo",
   no "está prohibido".
3. **Proponer la alternativa** que respeta el principio.
4. Si el usuario lo reafirma con una razón técnica válida: proceder, y **documentar la
   excepción** en "Decisiones cerradas" de la constitución.

Un principio se puede reabrir. Lo que no se puede es romperlo en silencio.
