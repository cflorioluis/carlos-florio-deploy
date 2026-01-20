# Bubble Sort: El Algoritmo de la Burbuja 🫧

El **Bubble Sort** es uno de los algoritmos de ordenamiento más simples y es ideal para entender cómo funciona la lógica de comparación y el intercambio de elementos.

## ¿En qué consiste?

Funciona revisando cada elemento de la lista que va a ser ordenada con el siguiente, intercambiándolos de posición si están en el orden equivocado ($n1 > n2$). Es necesario revisar varias veces toda la lista hasta que no se necesiten más intercambios, lo cual significa que la lista está ordenada.

Recibe su nombre porque los elementos más grandes "burbujean" hacia el final de la lista en cada iteración.

## Complejidad

| Caso | Complejidad $O(n)$ |
| :--- | :--- |
| **Peor caso** | $O(n^2)$ |
| **Caso promedio** | $O(n^2)$ |
| **Mejor caso** | $O(n)$ (si la lista ya está ordenada) |

## Ejemplo en TypeScript

```typescript
function bubbleSort(arr: number[]): number[] {
  const n = arr.length;
  let swapped: boolean;
  
  do {
    swapped = false;
    for (let i = 0; i < n - 1; i++) {
      if (arr[i] > arr[i + 1]) {
        // Intercambiar elementos
        [arr[i], arr[i + 1]] = [arr[i + 1], arr[i]];
        swapped = true;
      }
    }
  } while (swapped);
  
  return arr;
}
```

> [!TIP]
> Aunque no es eficiente para grandes conjuntos de datos, es excelente para propósitos educativos y para listas muy pequeñas que están "casi" ordenadas.
