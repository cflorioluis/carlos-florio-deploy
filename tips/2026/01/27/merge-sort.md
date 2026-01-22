# Merge Sort: Ordenamiento por Mezcla 🧩

El **Merge Sort** es un algoritmo de ordenamiento estable que garantiza un rendimiento consistente de **$O(n \log n)$**.

## ¿En qué consiste?

Al igual que Quick Sort, utiliza **Divide y Vencerás**:

1. **Dividir**: Divide el arreglo por la mitad repetidamente hasta tener arreglos de un solo elemento.
2. **Conquistar**: Mezcla (merge) los arreglos ordenando los elementos mientras los une.

## Complejidad Asintótica (Big O)

El **Merge Sort** garantiza un rendimiento estable sin importar el orden inicial de los datos. Puedes profundizar en la teoría de la eficiencia en nuestra [Guía Rápida de Notación Big O](/tips/2026/01/20/big-o-notation).

1. **Peor caso: $O(n \log n)$**
2. **Caso promedio: $O(n \log n)$**
3. **Mejor caso: $O(n \log n)$**

> [!NOTE]
> A diferencia de Selection, Insertion o Bubble sort, Merge Sort requiere **$O(n)$** de espacio adicional para realizar la mezcla.

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

## 🎮 Demo Interactivo

¡Visualiza el algoritmo en acción! Puedes controlar la ejecución paso a paso, escuchar el proceso y experimentar con el código.

[Ver Merge Sort en Acción](/tips/2026/01/27/merge-sort/demo)
