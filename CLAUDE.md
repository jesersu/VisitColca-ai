# CLAUDE.md
 
Este archivo da contexto a Claude Code sobre el proyecto para que las sugerencias y el código generado sean consistentes con la arquitectura definida.
 
## Descripción del proyecto
 
Plataforma para venta de paquetes turísticos. Un único backend sirve a tres clientes,
que se construyen en este orden: **1) app iOS nativa → 2) app Android nativa → 3) cliente web**.

Funcionalidad del producto:
- Catálogo de tours con reservas
- Pagos con tarjeta de crédito/débito
- Blog de tours y recomendaciones
- Panel de administración (CRUD de tours, posts y usuarios)
- Autenticación local + login social (Google, Facebook)
## Stack tecnológico
 
| Capa | Tecnología | Notas |
|---|---|---|
| Cliente iOS (fase 1) | Swift + SwiftUI | app nativa — primer cliente a construir |
| Cliente Android (fase 2) | Kotlin + Jetpack Compose | app nativa — segundo cliente |
| Cliente web (fase 3) | Next.js (React) | SSR/SSG para SEO del catálogo y blog — último cliente |
| Gestor de paquetes | pnpm | usar `pnpm install` / `pnpm dev`, no npm ni yarn |
| Backend | FastAPI (Python) | API REST, async |
| Base de datos | PostgreSQL | vía Supabase o Neon |
| Autenticación | Supabase Auth | login local + Google/Facebook, JWT |
| Pagos | Culqi / Niubiz | pasarela peruana, checkout tokenizado (nunca se procesan datos de tarjeta en el propio backend) |
| Storage de imágenes | Cloudflare R2 | tours y posts del blog |
| Hosting cliente web | Cloudflare Pages / Vercel | solo aplica en la fase 3 |
| Distribución apps | App Store / Google Play | |
| Hosting backend | Railway / Render / Fly.io | |
 
## Arquitectura
 
- Arquitectura decoupled: un backend FastAPI y varios clientes independientes, comunicados vía REST.
- El backend es **client-agnostic**: no contiene lógica de presentación acoplada a ningún cliente.
  Sirve por igual a iOS, Android y web. El contrato de la API es el artefacto compartido crítico.
- Orden de construcción: iOS nativo primero, Android nativo después, cliente web al final.
  Cada cliente nuevo consume la API existente; no se crea un backend por plataforma.
- El panel de administración vive **dentro del cliente web**, bajo rutas protegidas `/admin/*` — no es una
  aplicación aparte, y por lo tanto llega recién en la fase 3. Las apps móviles no incluyen panel de admin.
- Autorización en dos capas:
  1. El cliente (middleware de Next.js en web, o el estado de sesión en iOS/Android) verifica el rol del JWT
     para decidir qué muestra. Esto es **solo UX**, en cualquier plataforma.
  2. FastAPI valida el JWT y el rol en cada endpoint de escritura vía `Depends(require_admin)` — esta es la capa de seguridad real.
- Los datos de usuario (`role`, perfil) viven en la tabla `users` de PostgreSQL, enlazados al `user_id` que entrega Supabase Auth. No se duplican usuarios entre sistemas.
## Estructura de endpoints (backend)
 
```
/api/tours          # público: listado y detalle de paquetes
/api/posts           # público: blog
/api/bookings         # reservas de usuarios autenticados
/api/admin/tours       # CRUD, solo admin
/api/admin/posts       # CRUD, solo admin
/api/admin/users       # gestión de usuarios, solo admin
```
 
## Modelos de datos clave
 
- `tours`: paquetes turísticos (título, descripción, itinerario, precio, cupos, imágenes)
- `bookings`: reservas (usuario, tour, estado: pendiente/pagado/cancelado)
- `payments`: referencia a la transacción de la pasarela (nunca datos de tarjeta)
- `posts`: entradas del blog (título, contenido markdown, imágenes, tags)
- `users`: perfil y rol (`admin` / `customer`), enlazado a Supabase Auth
## Convenciones de código
 
_(completar según se defina el proyecto: estilo de commits, linting, testing, estructura de carpetas)_
 
## Comandos
 
_(completar cuando se inicialice el repo: `dev`, `build`, `test`, `lint`)_
 
## Decisiones ya tomadas (no reabrir sin justificación)
 
- PostgreSQL sobre MySQL/SQL Server — mejor soporte JSON/full-text y ecosistema con FastAPI.
- Supabase Auth sobre Firebase Auth — evita duplicar usuarios en dos bases de datos distintas (Postgres + Firestore).
- Culqi/Niubiz sobre Stripe — Stripe no opera pagos para comercios registrados en Perú.
- Orden de entrega de clientes: **iOS nativo → Android nativo → web**. El cliente web es el último, no el primero.
- Apps móviles nativas por plataforma, no multiplataforma (React Native / Flutter) — el objetivo incluye aprender
  cada stack nativo, no abstraerlo.
 