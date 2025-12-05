# 🆔 UUID vs CUID2 vs NanoID: La Guerra de los IDs

`1`, `2`, `3`... Los IDs autoincrementales son fáciles, pero peligrosos. Si mi usuario es `/users/500`, sé que hubo 499 antes y que probablemente exista el `/users/501`.

Necesitas IDs únicos y no secuenciales. Pero, ¿cuál elegir?

---

## 1. UUID (v4)
El estándar de facto.
-   **Formato**: `123e4567-e89b-12d3-a456-426614174000` (36 caracteres).
-   **Pros**: Soportado nativamente por casi todas las bases de datos (Postgres `uuid`). Colisiones imposibles en la práctica.
-   **Contras**: Muy largo. No es "URL friendly" (guiones). Ocupa mucho espacio en índices de DB si no se guarda como binario.

## 2. NanoID
El competidor moderno y ligero.
-   **Formato**: `V1StGXR8_Z5jdHi6B-myT` (21 caracteres por defecto).
-   **Pros**: **Mucho** más corto que UUID. URL-friendly (A-Za-z0-9_-). Más rápido de generar.
-   **Contras**: No es un estándar de base de datos (se guarda como texto).

## 3. CUID2
El nuevo chico en el barrio, diseñado para la nube.
-   **Formato**: `tz4a98xxat96zi9i` (longitud variable, ~24 chars).
-   **Pros**: Seguro contra colisiones incluso en entornos distribuidos masivos. Incluye "fingerprint" del host para evitar colisiones si se generan al mismo milisegundo en máquinas distintas.
-   **Contras**: Menos conocido.

---

## 🏆 Veredicto

-   **Usa UUID (v4)** si tu base de datos lo soporta nativamente (Postgres) y quieres el estándar aburrido y seguro.
-   **Usa NanoID** si necesitas IDs cortos para URLs públicas (ej: enlaces compartidos, códigos de referencia) y usas bases de datos que no soportan UUID nativo (como Mongo o MySQL antiguo).
-   **Usa CUID2** si estás construyendo sistemas distribuidos muy complejos y te preocupa la entropía.

**Consejo Pro:** Nunca expongas tu Primary Key autoincremental (`id: 1`). Si la usas internamente, expón un `public_id` (UUID/NanoID) en la API.
