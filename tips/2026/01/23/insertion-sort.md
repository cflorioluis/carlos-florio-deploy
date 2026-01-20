# Insertion Sort: Ordenamiento por Inserción 📥

El **Insertion Sort** es la forma en que la mayoría de las personas ordenan una mano de cartas. Es eficiente para arreglos pequeños y casi ordenados.

## ¿En qué consiste?

El algoritmo construye el arreglo final ordenado un elemento a la vez.

1. Toma un elemento de la lista.
2. Lo compara con los elementos que ya están ordenados a su izquierda.
3. Lo inserta en la posición correcta, desplazando los elementos mayores hacia la derecha.

## Complejidad

| Caso | Complejidad $O(n)$ |
| :--- | :--- |
| **Peor caso** | $O(n^2)$ |
| **Caso promedio** | $O(n^2)$ |
| **Mejor caso** | $O(n)$ (si ya está ordenado) |

## Ejemplo en TypeScript

```typescript
function insertionSort(arr: number[]): number[] {
  for (let i = 1; i < arr.length; i++) {
    let current = arr[i];
    let j = i - 1;
    
    // Desplaza los elementos de arr[0..i-1] que son mayores que current
    while (j >= 0 && arr[j] > current) {
      arr[j + 1] = arr[j];
      j--;
    }
    arr[j + 1] = current;
  }
  return arr;
}
```

> [!TIP]
> Es un algoritmo **estable** (no cambia el orden relativo de elementos iguales) y **in-place** (requiere una cantidad constante de memoria extra).
