# Quick Sort: El Poder de Divide y Vencerás ⚡

El **Quick Sort** es uno de los algoritmos más utilizados en la práctica debido a su excepcional rendimiento promedio.

## ¿En qué consiste?

Utiliza la estrategia de **Divide y Vencerás**:

1. **Elegir un Pivot**: Se selecciona un elemento del arreglo.
2. **Partición**: Los elementos menores que el pivot van a la izquierda, y los mayores a la derecha.
3. **Recursión**: Se aplica el mismo proceso a las sub-listas de la izquierda y la derecha.

## Complejidad

| Caso | Complejidad $O(n)$ |
| :--- | :--- |
| **Peor caso** | $O(n^2)$ (ej. lista ya ordenada con mal pivot) |
| **Caso promedio** | $O(n \log n)$ |
| **Mejor caso** | $O(n \log n)$ |

## Ejemplo en TypeScript

```typescript
function quickSort(arr: number[]): number[] {
  if (arr.length <= 1) return arr;

  const pivot = arr[arr.length - 1]; // Usamos el último como pivot
  const left = [];
  const right = [];

  for (let i = 0; i < arr.length - 1; i++) {
    if (arr[i] < pivot) {
      left.push(arr[i]);
    } else {
      right.push(arr[i]);
    }
  }

  return [...quickSort(left), pivot, ...quickSort(right)];
}
```

> [!IMPORTANT]
> La elección del pivot es crucial para evitar el peor caso de $O(n^2)$. Técnicas como "Mediana de Tres" ayudan a mantener el rendimiento en $O(n \log n)$.
