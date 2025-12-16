# 🔐 JWT vs Sessions: La Eterna Pregunta

¿Cómo mantengo al usuario logueado? ¿Token (JWT) o Cookie de Sesión? La respuesta corta: **Depende**. La respuesta larga: Sigue leyendo.

---

## 🍪 Sessions (Cookies)

El método clásico.
1.  Servidor crea un `session_id`, lo guarda en memoria/DB y se lo manda al navegador en una cookie `HttpOnly`.
2.  Navegador envía la cookie en cada petición automáticamente.
3.  Servidor busca el `session_id` en su DB para saber quién es.

-   **✅ Pros**:
    -   **Revocación instantánea**: Si borras la sesión de la DB, el usuario sale fuera al instante (genial para "Cerrar sesión en todos los dispositivos").
    -   **Cookies HttpOnly**: Protegidas contra XSS (JavaScript no puede leerlas).
-   **❌ Contras**:
    -   **Stateful**: Necesitas consultar la DB/Redis en cada petición.
    -   **CSRF**: Necesitas protección extra contra Cross-Site Request Forgery.

---

## 🎫 JWT (JSON Web Tokens)

El método moderno (stateless).
1.  Servidor crea un JSON con datos (`{ userId: 123 }`), lo firma criptográficamente y se lo da al cliente.
2.  Cliente lo guarda (LocalStorage o Cookie) y lo envía en el header `Authorization: Bearer <token>`.
3.  Servidor valida la firma matemática. No necesita consultar DB.

-   **✅ Pros**:
    -   **Stateless**: Escalabilidad infinita. El servidor no guarda nada.
    -   **Cross-domain**: Fácil de usar entre diferentes dominios/APIs.
-   **❌ Contras**:
    -   **No se pueden revocar**: Si roban un JWT válido, es válido hasta que expire. (Solución: listas negras, pero entonces vuelves a ser stateful).
    -   **Tamaño**: Son más grandes que una cookie ID.

---

## 🏆 ¿Cuál elijo?

-   **Usa Sessions** si: Es una app web tradicional, monolito o pocos microservicios, y la seguridad crítica (revocación) es prioridad.
-   **Usa JWT** si: Tienes muchos microservicios (para no consultar la DB de sesiones en cada uno), es una App Móvil, o necesitas delegar autenticación (OAuth/Auth0).

**Consejo Pro:** Si usas JWT en web, guárdalo en una **Cookie HttpOnly** también, no en LocalStorage, para evitar XSS.
