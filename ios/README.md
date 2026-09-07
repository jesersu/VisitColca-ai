# Cliente iOS — Fase 1

App nativa en Swift 6 + SwiftUI. **Primer cliente del producto.**

Consume la API de `backend/` vía REST. No reimplementa reglas de negocio.

## Estructura prevista

- `Domain/` — Swift puro. Sin `import SwiftUI`, sin red. Es la capa que se
  desarrolla con TDD estricto (Swift Testing).
- `Data/` — repositorios y cliente HTTP. Tests de contrato contra fixtures.
- `Presentation/` — vistas SwiftUI y modelos `@Observable`. Previews, no TDD.

## Reglas

- Swift 6 con concurrencia estricta. No se desactiva.
- `@Observable`, no `ObservableObject`.
- Swift Testing (`@Test` / `#expect`), no XCTest, para código nuevo.
- Módulos como paquetes SPM locales.
- Sin panel de administración: eso vive en el cliente web (fase 3).
