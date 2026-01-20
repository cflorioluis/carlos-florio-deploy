# Selection Sort: Ordenamiento por Selección 🔍

El **Selection Sort** es un algoritmo sencillo que mejora ligeramente la lógica del Bubble Sort al minimizar el número de intercambios.

## ¿En qué consiste?

El algoritmo divide la lista en dos partes: la parte de elementos ya ordenados (al principio) y la parte de elementos por ordenar. 

1. Busca el elemento más pequeño de la parte no ordenada.
2. Lo intercambia con el primer elemento de la parte no ordenada.
3. Lo mueve a la parte ordenada.

Repite este proceso hasta que toda la lista esté en la sección ordenada.

## Complejidad

| Caso | Complejidad $O(n)$ |
| :--- | :--- |
| **Peor caso** | $O(n^2)$ |
| **Caso promedio** | $O(n^2)$ |
| **Mejor caso** | $O(n^2)$ |

> [!NOTE]
> A diferencia de otros algoritmos, el Selection Sort siempre realiza el mismo número de comparaciones, sin importar si la lista está ordenada o no.

## Ejemplo en TypeScript

```typescript
function selectionSort(arr: number[]): number[] {
  const n = arr.length;
  
  for (let i = 0; i < n - 1; i++) {
    let minIdx = i;
    for (let j = i + 1; j < n; j++) {
      if (arr[j] < arr[minIdx]) {
        minIdx = j;
      }
    }
    
    if (minIdx !== i) {
      // Intercambiar el mínimo encontrado con el elemento actual
      [arr[i], arr[minIdx]] = [arr[minIdx], arr[i]];
    }
  }
  
  return arr;
}
```
