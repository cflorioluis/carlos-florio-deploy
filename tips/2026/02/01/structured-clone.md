# 👯 Tip del Día: structuredClone() - El fin de los problemas con clones

Hacer una "copia profunda" (deep clone) de un objeto en JavaScript solía ser un dolor de cabeza. Había dos opciones populares, y ambas tenían problemas:

1.  **JSON.parse(JSON.stringify(obj))**: Rápido, pero rompía fechas, expresiones regulares, y se perdían funciones o valores `undefined`.
2.  **Librerías externas (como Lodash `cloneDeep`)**: Efectivo, pero añade peso innecesario a tu proyecto.

## 🚀 La solución nativa: structuredClone()

Ahora tenemos un método nativo en el navegador y en Node.js (v17+) que lo hace perfecto:

```javascript
const original = {
  name: "Carlos",
  details: {
    age: 30,
    hobbies: ["Code", "Coffee"]
  },
  date: new Date()
};

// ✨ Clonado perfecto de una sola línea
const clone = structuredClone(original);

console.log(clone.details === original.details); // false (es una copia real)
console.log(clone.date instanceof Date); // true (mantiene el tipo de dato)
```

### ✅ Qué soporta:
*   Objetos anidados y Arrays.
*   Tipos como `Date`, `Set`, `Map`, `RegExp`.
*   Referencias circulares (¡sí, las maneja!).

### ⚠️ Limitaciones:
*   No clona funciones.
*   No clona métodos de clases (prototipos).

¡Usa `structuredClone()` hoy mismo y deja atrás las hacks de JSON! 🛠️

#javascript #frontend #webdev #cleancode #tips
