# Librerías locales en Docker: `npm link` vs `yalc` 🐳

Ayer vimos cómo `npm link` nos facilita la vida al desarrollar librerías locales. Pero, ¿qué pasa cuando nuestro proyecto corre dentro de **Docker**?

### El problema de los Symlinks 🖇️
`npm link` funciona mediante **enlaces simbólicos**. El problema es que un enlace creado en tu Mac apuntando a `/Users/carlos/mi-lib` no existe dentro del contenedor Linux de Docker. Para Docker, ese enlace está "roto".

### Opción A: El camino difícil con `npm link` 🛠️
Para que funcione, debes:
1.  **Montar ambos como volúmenes** en tu `docker-compose.yml`:
    ```yaml
    volumes:
      - ./mi-proyecto:/app
      - ../mi-libreria:/libreria-local
    ```
2.  **Ejecutar el link dentro del contenedor**:
    ```bash
    docker exec -it mi-contenedor npm link /libreria-local
    ```

### Opción B: El camino Pro con `Yalc` 🚀
**Yalc** es la alternativa preferida para entornos virtualizados como Docker. En lugar de symlinks, utiliza un repositorio local (`.yalc`) que imita el comportamiento de un registro de npm real.

#### Cómo usarlo:
1.  **Instala yalc globalmente** (en tu máquina): `npm i -g yalc`
2.  **En tu librería**: Ejecuta `yalc publish`. Esto la "publica" en un almacen local.
3.  **En tu proyecto**: Ejecuta `yalc add mi-libreria`. 

#### ¿Por qué es mejor para Docker?
-   **Sin Symlinks**: Yalc copia físicamente los archivos a una carpeta `.yalc` dentro de tu proyecto.
-   **Volúmenes**: Como los archivos están dentro del proyecto, Docker los detecta automáticamente a través del volumen estándar.
-   **Persistencia**: Funciona perfectamente incluso si reinicias el contenedor o reconstruyes la imagen.

### Conclusión
Si trabajas solo en local, `npm link` es rápido. Pero si tu flujo de trabajo incluye **Docker**, ahorra frustraciones y pásate a **Yalc**.
