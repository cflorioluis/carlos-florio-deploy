# Merge Sort: Ordenamiento por Mezcla 🧩

El **Merge Sort** es un algoritmo de ordenamiento estable que garantiza un rendimiento consistente de $O(n \log n)$.

## ¿En qué consiste?

Al igual que Quick Sort, utiliza **Divide y Vencerás**:

1. **Dividir**: Divide el arreglo por la mitad repetidamente hasta tener arreglos de un solo elemento.
2. **Conquistar**: Mezcla (merge) los arreglos ordenando los elementos mientras los une.

## Complejidad

| Caso | Complejidad $O(n)$ |
| :--- | :--- |
| **Peor caso** | $O(n \log n)$ |
| **Caso promedio** | $O(n \log n)$ |
| **Mejor caso** | $O(n \log n)$ |

> [!NOTE]
> A diferencia de Selection, Insertion o Bubble sort, Merge Sort requiere $O(n)$ de espacio adicional para realizar la mezcla.

## Ejemplo en TypeScript

```typescript
function mergeSort(arr: number[]): number[] {
  if (arr.length <= 1) return arr;

  const mid = Math.floor(arr.length / 2);
  const left = mergeSort(arr.slice(0, mid));
  const right = mergeSort(arr.slice(mid));

  return merge(left, right);
}

function merge(left: number[], right: number[]): number[] {
  const result = [];
  let i = 0, j = 0;

  while (i < left.length && j < right.length) {
    if (left[i] < right[j]) {
      result.push(left[i++]);
    } else {
      result.push(right[j++]);
    }
  }

  return result.concat(left.slice(i)).concat(right.slice(j));
}
```
