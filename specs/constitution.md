# Constitution.md
 
Principios no negociables del proyecto. Cualquier decisión técnica o de código que entre en conflicto con esto debe justificarse explícitamente o descartarse.
 
## 1. Seguridad de pagos
 
- Nunca se procesan, transmiten ni almacenan datos de tarjeta (número, CVV, fecha) en el backend propio.
- Todo pago pasa por el checkout tokenizado de la pasarela (Culqi/Niubiz). El backend solo guarda la referencia de la transacción.
- Ningún endpoint de pago se expone sin autenticación.
## 2. Autorización
 
- La UI (Next.js middleware) nunca es la única barrera de seguridad — es solo UX.
- Todo endpoint de escritura (`POST`/`PUT`/`DELETE`) sobre tours, posts o usuarios valida el rol en el backend (`require_admin`), sin excepción.
- No se duplica la fuente de verdad de usuarios: el rol y perfil viven en PostgreSQL, enlazados al `user_id` de Supabase Auth.
## 3. Datos
 
- Una sola base de datos relacional (PostgreSQL) para todo el negocio: tours, reservas, pagos, posts, usuarios.
- No se introduce una segunda base de datos (NoSQL, Firestore, etc.) salvo justificación técnica explícita.
- Los estados de una reserva (`pendiente` / `pagado` / `cancelado`) son la única fuente de verdad del flujo de compra.
## 4. Clientes
 
- El producto se entrega en tres clientes, en este orden y no otro: **iOS nativo → Android nativo → web**.
  No se empieza un cliente hasta que el anterior esté funcionando contra la API real.
- El backend es la única fuente de verdad de negocio. Ningún cliente reimplementa reglas de negocio
  (precios, cupos, transiciones de estado de una reserva) por su cuenta.
- El backend es client-agnostic: no se agregan endpoints ni campos hechos a medida de la pantalla de un
  cliente concreto. Si un cliente necesita otra forma de los datos, la resuelve el cliente.
- El SEO sigue siendo un requisito de negocio no negociable, y se cumple en el **cliente web** (fase 3):
  catálogo y blog con SSR/SSG (Next.js), nunca solo client-side. Las apps nativas no son el canal de
  indexación y por lo tanto no lo contradicen — pero tampoco lo reemplazan: la web no es opcional.
- Las apps nativas son nativas de verdad (Swift/SwiftUI y Kotlin/Compose). No se introduce una capa
  multiplataforma (React Native, Flutter, KMP) para "ahorrar" trabajo.
- El panel de administración vive dentro del cliente web, bajo rutas protegidas, y llega con la fase 3.
  No se crea una aplicación de admin separada, ni un panel de admin dentro de las apps móviles.
## 5. Simplicidad
 
- No se agregan servicios, librerías o capas de abstracción nuevas sin que resuelvan un problema concreto ya identificado.
- Antes de introducir una nueva dependencia, se prioriza lo que ya ofrece el stack elegido (Next.js, FastAPI, PostgreSQL, Supabase Auth).
## 6. Decisiones cerradas
 
Estas decisiones no se reabren sin una razón técnica nueva y documentada:
 
- PostgreSQL como única base de datos.
- Supabase Auth para autenticación local y social.
- Culqi/Niubiz como pasarela de pago (Stripe no opera para comercios peruanos).
- Arquitectura decoupled: un backend FastAPI compartido por todos los clientes.
- Orden de entrega de clientes: iOS nativo, luego Android nativo, y por último el cliente web en Next.js.
 