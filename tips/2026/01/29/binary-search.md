# 💡 Tip del Día: Algoritmo Binary Search 🔍

¿Alguna vez has buscado una palabra en un diccionario abriéndolo por la mitad y descartando la mitad incorrecta? ¡Eso es **Binary Search**!

---

## 🚀 ¿Qué es Binary Search?

La **Búsqueda Binaria** es un algoritmo ultra eficiente para encontrar un elemento en una **lista ordenada**. En lugar de comprobar cada elemento uno por uno (como en la "Búsqueda Lineal"), este algoritmo divide el espacio de búsqueda a la mitad en cada paso.

### 📊 Complejidad: $O(\log n)$
Es increíblemente rápido. Para una lista de **1 millón** de elementos:
- **Búsqueda Lineal**: Podría necesitar hasta 1,000,000 de pasos → $O(n)$
- **Búsqueda Binaria**: Solo necesita **20 pasos** como máximo → $O(\log n)$

<div style="text-align: center; margin: 20px 0;">
  <a href="/tips/2026/01/20/big-o-notation" style="display: inline-block; padding: 10px 20px; background: rgba(102, 126, 234, 0.1); color: #667eea; border-radius: 8px; text-decoration: none; font-weight: 500; border: 1px solid rgba(102, 126, 234, 0.2);">
    📚 Aprende más sobre Notación Big O
  </a>
</div>

---

## 🧠 ¿Cómo funciona?

Imagina que buscas el número **42** en una lista ordenada del 1 al 100:

1. **Mirar al centro** (50).
2. ¿Es 42? No. ¿Es menor que 50? Sí. -> **Descartamos del 50 al 100**.
3. **Mirar al nuevo centro** (25).
4. ¿Es 42? No. ¿Es mayor que 25? Sí. -> **Descartamos del 1 al 25**.
5. **Repetir** hasta encontrarlo.

```typescript
function binarySearch(arr: number[], target: number): number {
  let low = 0;
  let high = arr.length - 1;

  while (low <= high) {
    // Calcular punto medio (evitando overflow en lenguajes como C++/Java)
    const mid = Math.floor(low + (high - low) / 2);
    const guess = arr[mid];

    if (guess === target) {
      return mid; // 🎉 ¡Encontrado!
    }

    if (guess > target) {
      high = mid - 1; // Buscar en la mitad inferior
    } else {
      low = mid + 1;  // Buscar en la mitad superior
    }
  }

  return -1; // ❌ No encontrado
}
```

---

## ✨ Ventajas y Desventajas

| ✅ Ventajas | ❌ Desventajas |
|------------|---------------|
| Extremadamente rápido ($O(\log n)$) | **Requiere que la lista esté ordenada** |
| Eficiente en memoria (iterativo) | No sirve para listas desordenadas |
| Lógica simple y elegante | Más difícil de implementar correctamente que la lineal (bugs de "off-by-one") |

---

## 🎮 ¡Pruébalo Tú Mismo!

Hemos creado una **animación interactiva** para que veas cómo el algoritmo reduce el espacio de búsqueda paso a paso.

👉 **Haz clic en el botón "Ver Demo" arriba a la derecha para verlo en acción.** 🚀

---

**Fecha de publicación**: 29 de enero de 2026
