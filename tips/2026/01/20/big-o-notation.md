# Introducción a la Notación Big O 📈

La **notación Big O** es una forma matemática de expresar cómo crece el tiempo de ejecución (o el espacio en memoria) de un algoritmo en función del tamaño de la entrada. Es fundamental para analizar la eficiencia sin depender del hardware específico.

## ¿Por qué es importante?

En programación, no solo importa que el código funcione, sino qué tan bien escala. Un algoritmo que es rápido con 10 elementos podría tardar horas con 1 millón si su complejidad no es la adecuada.

## Los 6 Tipos Principales de Complejidad

Aquí te mostramos los casos más comunes que encontrarás:

1. **Constante: $O(1)$** - Excelente/Mejor
   - El tiempo de ejecución no cambia sin importar el tamaño de la entrada.
   - *Ejemplo*: Acceder a un elemento en un array por su índice.
   
2. **Logarítmica: $O(\log n)$** - Bueno
   - Crece muy lentamente.
   - *Ejemplo*: Búsqueda binaria en una lista ordenada.
   
3. **Lineal: $O(n)$** - Aceptable
   - Si duplicas los datos, duplicas el tiempo.
   - *Ejemplo*: Recorrer un array con un simple bucle `for`.
   
4. **Lineal-logarítmica: $O(n \log n)$** - Malo
   - Es la complejidad óptima para algoritmos de ordenamiento basados en comparación.
   - *Ejemplo*: **Merge Sort** y **Quick Sort**.
   
5. **Cuadrática: $O(n^2)$** - Horrible
   - El tiempo crece al cuadrado. Si duplicas los datos, el tiempo se multiplica por 4.
   - *Ejemplo*: Algoritmos con bucles anidados como **Bubble Sort**.
   
6. **Exponencial: $O(2^n)$** y **Factorial: $O(n!)$** - Peor
   - Prácticamente inutilizables para conjuntos grandes.
   - *Ejemplo*: Resolver el problema del viajante por fuerza bruta.

## Gráfico de Complejidad Big O

![Gráfico Big O](/tips/2026/01/21/big-o-complexity-chart.png)

Este gráfico muestra visualmente cómo diferentes complejidades escalan:

- **Verde** ($O(\log n)$, $O(1)$): Rendimiento excelente.
- **Amarillo** ($O(n)$): Rendimiento aceptable.
- **Naranja** ($O(n \log n)$): Comienzo del impacto en rendimiento.
- **Rojo** ($O(n^2)$, $O(2^n)$): Peligro de bloqueo del sistema con datos grandes.

---

> [!TIP]
> Al elegir un algoritmo, siempre busca bajar tu Big O. Pasar de $O(n^2)$ a $O(n \log n)$ puede ser la diferencia entre una app que vuela y una que se cuelga.
