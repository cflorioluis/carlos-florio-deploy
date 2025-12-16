# 🏷️ Semantic Versioning (SemVer): Pon Orden en tus Versiones

`v1.0.0`, `v2.1.4`, `v0.5.0`... ¿Qué significan realmente estos números? No son aleatorios. Siguen una regla sagrada llamada **Semantic Versioning**.

Entenderla es vital para no romper el código de quien use tu librería (o tu API).

---

## 🔢 El Formato: MAJOR.MINOR.PATCH

Ejemplo: `1.4.2`

1.  **MAJOR** (`1`): Cambios **incompatibles** (Breaking Changes).
    -   Si actualizas esto, tu código podría dejar de funcionar si no haces cambios.
    -   Ej: Renombrar una función pública, eliminar un endpoint, cambiar tipos de retorno.

2.  **MINOR** (`4`): Nueva funcionalidad **retro-compatible**.
    -   Añades cosas nuevas, pero lo viejo sigue funcionando igual.
    -   Ej: Añadir un nuevo método, un nuevo parámetro opcional.

3.  **PATCH** (`2`): Corrección de bugs **retro-compatible**.
    -   Arreglas algo interno, sin cambiar la API pública.
    -   Ej: Corregir un crash, optimizar rendimiento.

---

## 💡 Reglas de Oro

-   **v0.x.x**: Es el "Salvaje Oeste". Todo puede cambiar en cualquier momento. Úsalo para desarrollo inicial.
-   **v1.0.0**: Declaras que tu API es estable y pública. A partir de aquí, respetas SemVer.
-   **Nunca sobrescribas una versión**: Si publicaste la `1.0.1` y tenía un bug, no la borres y subas otra `1.0.1`. Publica la `1.0.2`.

---

## 📦 En `package.json` (npm)

Cuando instalas dependencias, ves símbolos raros:

-   `^1.2.3` (Caret): Permite actualizar MINOR y PATCH. (Ej: `1.3.0`, `1.2.4`, pero NO `2.0.0`). **Es el default y el más seguro.**
-   `~1.2.3` (Tilde): Permite actualizar solo PATCH. (Ej: `1.2.4`, pero NO `1.3.0`).
-   `1.2.3` (Exacta): Solo esa versión exacta.

¡Usa SemVer para comunicar, no solo para numerar!
