# CSS `:has()` + combinador `>`: estilizar al padre con **hijo directo** 🧬

`:has()` es el pseudo-selector que permite **elegir un ancestro** según lo que contiene. Cuando lo combinas con **`>`** (hijo directo), el criterio se vuelve muy preciso: solo cuenta lo que cuelga **justo debajo**, no nietos ni descendientes más lejanos.

![Diagrama visual: el selector `p:has(> b)` y un árbol DOM donde solo el `<b>` hijo directo del `<p>` hace match](/tips/2026/03/18/css-has-p-hijo-directo-b-diagrama.png)

## La idea en una línea

```css
p:has(> b) { ... }
```

Selecciona un `<p>` que tenga al menos un **hijo directo** `<b>`. Si el `<b>` está dentro de un `<a>` dentro del `<p>`, **no cuenta** (no es hijo directo del `<p>`).

## Ejemplo mental (árbol)

```html
<p>
  <a><b>negrita dentro del enlace</b></a>  <!-- este <b> NO matchea p:has(> b) -->
  <b>negrita directa</b>                    <!-- este SÍ matchea -->
</p>
```

Solo el segundo `<b>` hace que el `<p>` cumpla `p:has(> b)`.

## Por qué importa

- **Menos falsos positivos** al estilizar contenedores (cards, filas de tabla, bloques de aviso).
- **CSS sin JS** para variantes de layout: “si este bloque tiene un hijo directo `.media`, usa grid de dos columnas”, etc.

## Otros combinadores dentro de `:has()`

| Patrón | Significado breve |
|--------|-------------------|
| `article:has(.error)` | `article` con **cualquier** descendiente `.error` |
| `nav:has(> a.active)` | `nav` con enlace activo como **hijo directo** |
| `label:has(+ input:invalid)` | `label` seguido de un input inválido (hermano adyacente) |

## Soporte

`:has()` está soportado en navegadores modernos (Chrome, Firefox, Safari, Edge). Si aún tienes que soportar legacy, combina con **progressive enhancement** o un fallback sin la regla.

## Relación con otros tips

Ya publicamos una guía general de `:has()`; este tip remarca el matiz del **combinador hijo directo (`>`)**, que es donde mucha gente se equivoca al leer selectores complejos.
