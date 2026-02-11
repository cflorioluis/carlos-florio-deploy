# `Promise.withResolvers()`: Simplifica tus Promesas ⚡

A veces necesitamos crear una promesa y resolverla o rechazarla desde **fuera** de su ejecutor. Tradicionalmente, terminábamos con un código un poco "sucio" extrayendo las funciones `resolve` y `reject`.

### El patrón antiguo 🏚️

```javascript
let resolve, reject;
const promise = new Promise((res, rej) => {
  resolve = res;
  reject = rej;
});

// Ahora podemos usar resolve() o reject() en cualquier lugar
```

### La forma "Top": `Promise.withResolvers()` 🚀 (ES2024)

Esta nueva API nos devuelve directamente la promesa y sus controladores en un solo objeto (o mediante destructuración).

```javascript
const { promise, resolve, reject } = Promise.withResolvers();

// Ejemplo de uso:
setTimeout(() => {
  resolve("¡Todo listo! ✅");
}, 2000);

const data = await promise;
console.log(data); // "¡Todo listo! ✅"
```

### ¿Cuándo es útil? 🤔

1.  **Eventos**: Cuando quieres convertir un flujo basado en eventos en una promesa de forma limpia.
2.  **Streams/WebSockets**: Para esperar un mensaje específico antes de continuar con la ejecución.
3.  **Tests**: Para mockear respuestas asíncronas de manera más legible.

### Compatibilidad
Ya está disponible en las versiones más recientes de Chrome, Firefox, Safari y Node.js (v22+). ¡Una joya para limpiar tu código asíncrono!
