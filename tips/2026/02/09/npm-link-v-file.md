# Evita `file:` usa `npm link` para tus librerías locales

Cuando estamos desarrollando una librería y queremos probarla en un proyecto real, solemos caer en la tentación de referenciarla directamente en el `package.json` usando una ruta de archivo:

```json
"dependencies": {
  "mi-libreria": "file:../ruta/a/mi-libreria"
}
```

Aunque funciona, tiene varios **inconvenientes**:
1. Modifica tu `package.json` (peligro de subirlo por error).
2. Requiere reinstalar (`npm install`) cada vez que haces un cambio si quieres asegurar que los enlaces simbólicos se mantengan correctamente.
3. No siempre replica fielmente cómo se comportará la librería cuando esté publicada en npm.

### La solución: `npm link` 🔗

`npm link` es una herramienta integrada que crea enlaces simbólicos inteligentes entre tus proyectos.

### Paso 1: "Publicar" localmente
Ve a la carpeta de tu **librería** y ejecuta:
```bash
npm link
```
Esto crea un enlace simbólico global en tu sistema que apunta a esta carpeta.

### Paso 2: Usar en tu proyecto
Ahora, ve a la carpeta de tu **proyecto** y ejecuta:
```bash
npm link mi-libreria
```
*(Sustituye `mi-libreria` por el nombre que tenga el `package.json` de la librería)*.

### ¿Por qué es mejor?
- **Cambios en tiempo real**: Cualquier cambio que hagas en el código de la librería se reflejará instantáneamente en tu proyecto (siempre que la librería se transpile/compile automáticamente).
- **Limpio**: No ensucia tu `package.json`.
- **Estandarizado**: Es la forma oficial de Node.js para el desarrollo local entre paquetes.

### Cómo deshacer el enlace
Cuando termines de probar, simplemente ejecuta en tu proyecto:
```bash
npm unlink mi-libreria
```
Y npm volverá a buscar la versión oficial en el registro.
