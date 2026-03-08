# 🎫 Tip: Elementos con curvas y borde que se adapta al path

Cuando un ticket (o cualquier bloque) tiene **formas con curvas** (muescas, recortes), el `outline` o `border` estándar de CSS **no sigue** esas curvas: dibuja el rectángulo del contenedor. En la [demo en vivo](/tips/2026/3/2/css-ticket-entrada-reto/demo) se comparan dos enfoques: **ejemplo 1** (CSS mask, outline que no sigue las muescas) y **ejemplo 2** (clip-path SVG + borde SVG que sí sigue el path). Haz clic en cada uno para ver la diferencia al seleccionar.

---

## Problema: el outline no sigue las curvas

Con **dos muescas** hechas con CSS `mask`, el elemento tiene forma de ticket, pero al aplicar `outline` o `border` para el estado “seleccionado”, el borde sigue la caja rectangular, no las curvas:

```css
.ticket--double {
  --r: 16px;
  mask-image:
    radial-gradient(var(--r) at 0 50%, #0000 98%, #000),
    radial-gradient(var(--r) at 100% 50%, #0000 98%, #000);
  mask-size: 50% 100%, 50% 100%;
  mask-position: left center, right center;
  mask-repeat: no-repeat;
}
.ticket--double.ticket--selected {
  outline: 2px solid #c00;  /* no sigue las muescas */
}
```

---

## Solución: clip-path SVG + borde que sigue el path

Para que el **borde de selección siga exactamente las curvas**, usamos:

1. **clip-path** con un `<clipPath>` SVG (mismo path que define la forma).
2. Un **SVG superpuesto** con el mismo path y `stroke`, visible solo cuando está seleccionado.

Así el borde es literalmente el contorno del path.

---

### Paso 1: Definir el clipPath en SVG (oculto)

Un SVG sin tamaño que solo define el path en coordenadas normalizadas (`objectBoundingBox`, 0–1):

```html
<svg width="0" height="0" class="clip-svg-hidden" aria-hidden="true">
  <defs>
    <clipPath id="notch-TKT-2026-0782" clipPathUnits="objectBoundingBox">
      <path d="M 0 0 L 0 0.34 C 0 0.39, 0.04 0.42, 0.04 0.5 C 0.04 0.58, 0 0.61, 0 0.66 L 0 1 L 1 1 L 1 0.66 C 1 0.61, 0.96 0.58, 0.96 0.5 C 0.96 0.42, 1 0.39, 1 0.34 L 1 0 Z"/>
    </clipPath>
  </defs>
</svg>
```

El path dibuja un rectángulo con dos muescas (curvas Bézier) a izquierda y derecha, centradas en altura.

---

### Paso 2: Aplicar clip-path al div del ticket

El bloque con la forma de ticket usa ese clipPath y un fondo (gradiente, color, etc.):

```css
.ticket--notch-svg {
  background: linear-gradient(135deg, #1e4a7a 0%, #253550 50%, #2a2a4a 100%);
  clip-path: url(#notch-TKT-2026-0782);
  -webkit-clip-path: url(#notch-TKT-2026-0782);
}
```

Con `clipPathUnits="objectBoundingBox"`, el path se escala al tamaño del elemento.

---

### Paso 3: Borde de selección que sigue el path

En lugar de `outline`, mostramos un **SVG superpuesto** (mismo path, solo trazo), solo cuando el ticket está seleccionado:

```html
<!-- ticketId: identificador del ticket (ej. número o string según tu modelo) -->
<div class="ticket-wrap ticket-wrap--notch-svg" (click)="toggleTicket(ticketId)">
  <div class="ticket ticket--notch-svg" [class.ticket--selected]="selectedTickets.has(ticketId)">
    <!-- contenido -->
  </div>
  <svg *ngIf="selectedTickets.has(ticketId)" class="ticket-border-svg" viewBox="-0.06 -0.06 1.12 1.12" preserveAspectRatio="none">
    <g transform="translate(0.5 0.5) scale(1.04) translate(-0.5 -0.5)">
      <path fill="none" stroke="#c00" stroke-width="4" stroke-linecap="round" stroke-linejoin="round"
            vector-effect="non-scaling-stroke" shape-rendering="geometricPrecision"
            d="M 0 0 L 0 0.34 C 0 0.39, 0.04 0.42, 0.04 0.5 C 0.04 0.58, 0 0.61, 0 0.66 L 0 1 L 1 1 L 1 0.66 C 1 0.61, 0.96 0.58, 0.96 0.5 C 0.96 0.42, 1 0.39, 1 0.34 L 1 0 Z"/>
    </g>
  </svg>
</div>
```

- **Mismo `d`** que el clipPath: el borde sigue exactamente la forma.
- **`scale(1.04)`** desde el centro: el borde queda un poco fuera del borde del div (4%).
- **`vector-effect="non-scaling-stroke"`**: el grosor del trazo no se estira con el escalado.
- **`shape-rendering="geometricPrecision"`**: ayuda a suavizar el trazo.

CSS del contenedor y del SVG:

```css
.ticket-wrap--notch-svg { position: relative; }
.ticket--notch-svg.ticket--selected { outline: none; }
.ticket-border-svg {
  position: absolute;
  top: -6px; left: -10px;
  width: calc(100% + 20px); height: calc(100% + 12px);
  pointer-events: none;
  overflow: visible;
}
```

---

## Resumen

| Enfoque | Forma | Borde al seleccionar |
|--------|--------|------------------------|
| Ejemplo 1 (mask) | Muescas con CSS mask | `outline` → **no** sigue las curvas |
| Ejemplo 2 (clip-path + SVG) | Muescas con clip-path SVG | SVG con mismo path → **sí** sigue las curvas |

Para elementos con curvas donde el “borde” debe adaptarse al path, usar **clip-path + SVG de borde** con el mismo path es la solución que ves en el ejemplo 2 de la demo.

#css #svg #clip-path #frontend #diseño
