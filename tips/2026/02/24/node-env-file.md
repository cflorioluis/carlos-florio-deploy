# 🐳 Tip del Día: Node.js --env-file (cargar .env nativo)

Desde **Node.js 20.6** puedes cargar un archivo `.env` **sin instalar dotenv**. Ideal para scripts y APIs.

## Uso básico

```bash
node --env-file=.env server.js
```

O en tus scripts de `package.json`:

```json
{
  "scripts": {
    "start": "node --env-file=.env src/main.js",
    "dev": "node --env-file=.env.development --watch src/main.js"
  }
}
```

## Múltiples archivos

El último gana si hay conflicto de claves:

```bash
node --env-file=.env --env-file=.env.local app.js
```

## En el código

Las variables quedan en `process.env` como siempre:

```javascript
const port = process.env.PORT ?? 3000;
const dbUrl = process.env.DATABASE_URL;
```

## Ventajas

- ✅ Cero dependencias (ni `dotenv`)
- ✅ Mismo comportamiento en desarrollo y producción
- ✅ Compatible con `--watch` y el resto de flags de Node

Si usas Node 20+, deja de instalar `dotenv` para proyectos pequeños. 🎯

#node #backend #env #dotenv #javascript
