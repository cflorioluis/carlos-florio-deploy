# 🛑 Graceful Shutdown en Node.js

Cuando haces un deploy nuevo, tu servidor antiguo tiene que apagarse. Si simplemente "matas" el proceso, cortas las conexiones de usuarios que estaban en mitad de una petición. Eso es una mala experiencia de usuario (errores 502/504).

Lo profesional es hacer un **Graceful Shutdown** (Apagado Elegante).

---

## 🎩 ¿Qué es?

Es el proceso de:
1.  Recibir la señal de apagado (`SIGTERM` o `SIGINT`).
2.  Dejar de aceptar **nuevas** conexiones.
3.  Esperar a que las peticiones **en curso** terminen.
4.  Cerrar conexiones a base de datos y otros recursos.
5.  Salir del proceso (`process.exit(0)`).

---

## 💻 Implementación Básica

```javascript
const express = require('express');
const app = express();

const server = app.listen(3000, () => {
  console.log('Server listening on port 3000');
});

// Manejador de señales
async function gracefulShutdown(signal) {
  console.log(`Received ${signal}. Starting graceful shutdown...`);

  // 1. Cerrar servidor HTTP (deja de aceptar nuevas conexiones)
  server.close(() => {
    console.log('HTTP server closed.');
    
    // 2. Cerrar conexión a Base de Datos (ejemplo)
    // await db.disconnect(); 
    // console.log('Database connection closed.');

    console.log('Process finished.');
    process.exit(0);
  });

  // (Opcional) Forzar cierre si tarda mucho
  setTimeout(() => {
    console.error('Could not close connections in time, forcefully shutting down');
    process.exit(1);
  }, 10000); // 10 segundos timeout
}

// Escuchar señales del sistema operativo
process.on('SIGTERM', () => gracefulShutdown('SIGTERM'));
process.on('SIGINT', () => gracefulShutdown('SIGINT'));
```

---

## 🔍 ¿Por qué es importante?

-   **Kubernetes/Docker**: Cuando K8s escala hacia abajo o hace un rolling update, envía `SIGTERM`. Si tu app lo ignora, K8s la matará a la fuerza (`SIGKILL`) después de 30s, cortando peticiones.
-   **Integridad de Datos**: Aseguras que las transacciones en curso se completen o se reviertan correctamente.

¡Trata a tus usuarios con respeto hasta el último milisegundo!
