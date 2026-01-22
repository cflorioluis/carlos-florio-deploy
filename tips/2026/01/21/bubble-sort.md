# Bubble Sort: El Algoritmo de la Burbuja 🫧

El **Bubble Sort** es uno de los algoritmos de ordenamiento más simples y es ideal para entender cómo funciona la lógica de comparación y el intercambio de elementos.

## ¿En qué consiste?

Funciona revisando cada elemento de la lista que va a ser ordenada con el siguiente, intercambiándolos de posición si están en el orden equivocado. Es necesario revisar varias veces toda la lista hasta que no se necesiten más intercambios, lo cual significa que la lista está ordenada.

Recibe su nombre porque los elementos más grandes "burbujean" hacia el final de la lista en cada iteración.

## Complejidad Asintótica (Big O)

El **Bubble Sort** es un algoritmo con una eficiencia limitada para grandes volúmenes de datos. Para entender mejor qué significan estos términos, te recomendamos leer nuestra [Guía Rápida de Notación Big O](/tips/2026/01/20/big-o-notation).

1. **Peor caso: $O(n^2)$**
2. **Caso promedio: $O(n^2)$**
3. **Mejor caso: $O(n)$** (si la lista ya está ordenada)

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

> Aunque no es eficiente para grandes conjuntos de datos ($O(n^2)$), es excelente para propósitos educativos y para listas muy pequeñas que están "casi" ordenadas.

## 🎮 Demo Interactivo

¡Visualiza el algoritmo en acción! Puedes controlar la ejecución paso a paso, escuchar el proceso y experimentar con el código.

[Ver Bubble Sort en Acción](/tips/2026/01/21/bubble-sort/demo)
