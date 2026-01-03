# 🔢 Tip del Día: Rangos Rápidos con `Array.from()`

## 💡 ¿Necesitas un array del 1 al 10 rápidamente?

A veces necesitamos generar una secuencia numérica para un mapa de UI, una prueba o un contador. Podrías hacer un bucle `for`, pero hay una forma más funcional y elegante.

### ✅ La Solución: `Array.from()`

`Array.from()` no solo convierte objetos "parecidos a un array" en arrays reales, sino que acepta un segundo argumento: una función de mapeo.

```javascript
// Generar [0, 1, 2, 3, 4]
const sequence = Array.from({ length: 5 }, (_, i) => i);

// Generar [1, 2, 3, 4, 5]
const range = Array.from({ length: 5 }, (_, i) => i + 1);
```

---

## 🚀 ¿Cómo funciona?

-   `{ length: 5 }`: Creamos un objeto con una longitud definida. `Array.from` lo interpreta como un array de 5 elementos vacíos.
-   `(_, i) => i`: La función de mapeo. El primer argumento es el valor (vacío), el segundo es el **índice** `i`. ¡Usamos el índice para crear el valor!

---

## 🛠️ Ejemplos Prácticos

-   **Renderizar estrellas**: `Array.from({ length: rating }).map(...)`
-   **Abecedario**: 
    ```javascript
    const alphabet = Array.from({ length: 26 }, (_, i) => 
      String.fromCharCode(65 + i)
    );
    ```

**¡Menos bucles `for`, más elegancia funcional! 🧊**
