# TypeScript: `satisfies` frente a `as`

CSS moderno aplicado a interfaces reales.

---

## Concepto

Pequeños cambios en CSS o en patrones de UI mejoran legibilidad, rendimiento o inclusión sin reescribir toda la app.

---

## Ejemplo

```css
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## Referencia

https://www.typescriptlang.org/docs/handbook/release-notes/typescript-4-9.html
