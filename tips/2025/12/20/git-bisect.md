# 🕵️ Git Bisect: Encuentra el Bug en Segundos

Imagina esto: Todo funcionaba bien ayer. Hoy, algo está roto. Has hecho 50 commits entre medias. ¿Cuál rompió el código?

No revises uno por uno. Usa **Git Bisect**.

---

## 🪄 ¿Qué hace?

Utiliza búsqueda binaria para encontrar el commit culpable. En lugar de probar 50 commits, probarás ~6.

---

## 🛠️ Pasos

1.  **Inicia el modo bisect:**
    ```bash
    git bisect start
    ```

2.  **Dile que el estado actual está mal (bad):**
    ```bash
    git bisect bad
    ```

3.  **Dile un commit antiguo donde todo funcionaba bien (good):**
    ```bash
    git bisect good <hash-del-commit-bueno>
    # ej: git bisect good a1b2c3d
    ```

4.  **Git te moverá automáticamente a la mitad del historial.**
    Git hará checkout de un commit intermedio.
    -   Prueba tu app. ¿Funciona?
    -   Si funciona: `git bisect good` (el error está en la mitad futura).
    -   Si falla: `git bisect bad` (el error está en la mitad pasada).

5.  **Repite.**
    Git seguirá dividiendo a la mitad hasta que te diga:
    > **a1b2c3d is the first bad commit**

6.  **Termina:**
    ```bash
    git bisect reset
    ```
    (Vuelves a tu rama original).

---

## 🤖 Nivel Dios: Automatizado

Si tienes un test que falla cuando el bug está presente (ej: `npm test`), puedes hacer que git lo encuentre solo:

```bash
git bisect start HEAD <commit-bueno>
git bisect run npm test
```

Git ejecutará el test en cada paso y encontrará el commit culpable mientras tú vas a por café. ☕

¡Deja de adivinar y empieza a cazar!
