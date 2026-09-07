# Backend — FastAPI

API REST async en Python. **Fuente de verdad de todo el negocio.**

Servido por igual a iOS, Android y web. Es client-agnostic: no se agregan
endpoints ni campos a medida de la pantalla de un cliente concreto.

- Autorización real: `Depends(require_admin)` en cada endpoint de escritura.
- Nunca procesa ni almacena datos de tarjeta — solo la referencia de la
  transacción que devuelve la pasarela.
