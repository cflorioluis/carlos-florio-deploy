# 🐳 Docker Multi-stage Builds: Imágenes más ligeras y seguras

Si tus imágenes de Docker ocupan 1GB para una simple API de Node.js, necesitas **Multi-stage Builds**.

---

## 📉 El Problema

Cuando haces `npm install` y `npm run build`, generas un montón de archivos que no necesitas en producción:
- `node_modules` completos (incluyendo devDependencies).
- Código fuente TypeScript.
- Cachés de compilación.
- Herramientas de sistema necesarias para compilar (compiladores de C++, Python, etc.).

Todo esto engorda tu imagen y aumenta la superficie de ataque.

---

## ✅ La Solución: Multi-stage

La idea es usar una imagen "grande" para construir y una imagen "pequeña" para ejecutar.

```dockerfile
# 🏗️ Stage 1: Build
FROM node:18-alpine AS builder

WORKDIR /app

# Copiamos solo package.json para aprovechar caché de capas
COPY package*.json ./

# Instalamos TODAS las dependencias (incluyendo dev)
RUN npm ci

# Copiamos el código fuente
COPY . .

# Compilamos (genera carpeta dist/)
RUN npm run build

# 🏃 Stage 2: Runner
FROM node:18-alpine AS runner

WORKDIR /app

# Copiamos solo lo necesario del stage anterior
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/package*.json ./

# Instalamos SOLO dependencias de producción
RUN npm ci --only=production

# (Opcional) Crear usuario no-root por seguridad
USER node

CMD ["node", "dist/main.js"]
```

---

## 🚀 Resultados

| Tipo de Imagen | Tamaño Aprox. |
|----------------|---------------|
| Estándar (todo junto) | ~900 MB |
| **Multi-stage** | **~150 MB** |

### Beneficios:
1.  **Despliegues más rápidos**: Subir y bajar 150MB es mucho más rápido que 1GB.
2.  **Ahorro de costes**: Menos espacio en disco y ancho de banda.
3.  **Seguridad**: Tu imagen de producción no tiene el código fuente original, ni compiladores, ni herramientas de desarrollo.

¡Pon a dieta a tus contenedores!
