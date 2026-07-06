# `.dockerignore`: builds más rápidos y seguros

Guía práctica de contenedores para APIs Node y frontends estáticos.

---

## Idea clave

Una imagen reproducible evita el clásico «en mi máquina funciona». Parte de una base pequeña (Alpine) y fija versiones.

---

## Fragmento de ejemplo

```dockerfile
FROM node:22-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev
COPY . .
EXPOSE 3000
CMD ["node", "dist/main.js"]
```

---

## Recursos

- Documentación: https://docs.docker.com/build/building/context/#dockerignore-files
