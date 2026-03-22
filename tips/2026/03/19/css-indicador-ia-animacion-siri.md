# Indicadores visuales tipo “Siri” para **IA en progreso** (CSS, sin humo) ✨

Cuando una función usa IA (generación, resumen, búsqueda semántica…), conviene un **feedback visual** claro: el usuario entiende que *algo está procesando* y que no debe reenviar el formulario cien veces.

## Qué evitar

El enfoque de “muchos puntos con `keyframes` aleatorios en JS” (como en sandboxes didácticos) suele:

- Generar **CSS enorme** y difícil de mantener.
- Provocar **parpadeos** o trayectorias incoherentes.
- **No** respetar `prefers-reduced-motion`.

Referencia de partida (simplificada): [CodeSandbox – Siri CSS](https://codesandbox.io/p/sandbox/siri-css-43zxj?file=%2Fsrc%2Findex.js).

---

## Cómo hacerlo (paso a paso)

Lo que ves en el **[demo en vivo](#demo-en-vivo)** sigue siempre la misma idea: **capas** (neón detrás, contenido opaco delante) + **animaciones con pocos keyframes** + **clase o `aria-busy`** para activar/desactivar.

### 1. Input con “anillo neón” (gradiente cónico, no rectángulo)

**Problema:** si pones un `conic-gradient` en un rectángulo ancho, los lados verticales casi no muestran color al girar.

**Solución:**

1. Contenedor **`.ring`** con `position: relative`, `padding: 4px`, `border-radius` acorde al input, `overflow: hidden`.
2. Capa **`.ring__spin`**: `position: absolute`, centrada con `left: 50%` + `top: 50%` + `transform: translate(-50%, -50%)`, y un **cuadrado muy grande** (p. ej. `width/height: 160vmin` o `max(360cqmax, 160vmin)` con `container-type: size` en el padre) con `background: conic-gradient(...)` y `animation: rotate 3.5s linear infinite`.
3. Encima, **`.ring__surface`** > **`.ring__inner`** con fondo **sólido** (el del campo), `border-radius` igual al recorte y `z-index` por encima del spin. Solo se ve el anillo de los **píxeles del padding** entre el borde exterior y el campo.

```html
<div class="ai-ring ai-ring--active">
  <div class="ai-ring__spin" aria-hidden="true"></div>
  <div class="ai-ring__surface">
    <div class="ai-ring__inner">
      <input aria-busy="true" />
    </div>
  </div>
</div>
```

Activa el neón con una clase (p. ej. `.ai-ring--active .ai-ring__spin { opacity: 1 }`) cuando `loading` o `aria-busy="true"` en el input (en frameworks suele enlazarse con `[class.ai-ring--active]="loading"`).

### 2. Botón con ondas (equalizer)

1. Mientras carga, muestra **varias barras** (`span` de 3–4px de ancho) en un contenedor flex con `align-items: flex-end`.
2. Misma animación para todas: `transform: scaleY(...)` con `transform-origin: bottom` (o `center` si prefieres simetría).
3. **Retrasos escalonados** con `nth-child` o un `@for` en SCSS: `animation-delay: $i * 0.06s`.
4. Opcional: **gradiente distinto por barra** (colores alineados a tu `conic-gradient` del producto) y un **halo** en el botón con un segundo `@keyframes` en `box-shadow` solo mientras `.is-loading`.

```html
<button type="button" class="ai-btn ai-btn--loading" aria-busy="true">
  <span class="ai-btn__content">
    <span class="ai-btn__label">Generando…</span>
    <span class="siri-wave" aria-hidden="true">
      <span class="siri-wave__bar"></span>
      <!-- …8–10 barras .siri-wave__bar -->
    </span>
  </span>
</button>
```

(En Angular/React/Vue genera las barras con `*ngFor` / `map` / `v-for` y quita `ai-btn--loading` + ondas cuando termine la carga.)

### 3. Orbe al lado del input (anillo + partículas)

- **Anillo:** misma paleta `conic-gradient` que el input; recorta con **`mask` / `-webkit-mask`** radial para que solo se vea una **corona** (agujero interior transparente, anillo opaco).
- **Centro:** partículas **0×0** con **`box-shadow`** grande (glow) y dos animaciones anidadas: capa exterior mueve **X**, capa interior mueve **Y** (patrón tipo “puntoOut / puntoIn” del sandbox, pero con keyframes fijos en CSS).

Ocultar el anillo sin quitar partículas: una clase que haga `display: none` solo en la capa del conic.

### 4. Chip / badge compacto

Misma receta que el input, pero **pill**:

- Contenedor redondeado `999px`, `overflow: hidden`.
- **`.chip__spin`**: cuadrado grande centrado + `conic-gradient` girando.
- **`.chip__inner`**: `margin: 2px` (o el grosor que quieras), fondo vidrio/sólido, texto + punto de estado.
- Pulso: anima el punto y, si quieres, un **anillo** extra (`border` + `scale` + `opacity`) solo mientras “procesando”.

### 5. Paleta unificada

Reutiliza los **mismos stops** en botón, anillo, orbe y chip para que parezca **un sistema**, no cuatro experiments sueltos. En el demo se usa una familia tipo:

`#ff006e → #ff8500 → #ffea00 → #00f5a0 → #00bbf9 → #9b5de5 → #f15bb5 → #8338ec → #06ffa5`.

---

## Enfoque recomendado (resumen)

1. **Barras u ondas** con `animation-delay` escalonado (ecualizador suave).
2. **Gradiente cónico** en una capa **grande y cuadrada** centrada, **no** en el mismo rectángulo del input.
3. Todo en **CSS puro** (o SCSS con `@for`), sin bucles en runtime que inyecten 20 `@keyframes` distintos.

### Base tipo “ondas” (SCSS)

```scss
$line-count: 10;

.siri-wave {
  display: inline-flex;
  align-items: flex-end;
  justify-content: center;
  gap: 4px;
  padding: 5px 11px 6px;
  border-radius: 999px;
  background: rgba(0, 0, 0, 0.2);
}

.siri-wave__bar {
  width: 3.5px;
  height: 16px;
  border-radius: 999px;
  transform-origin: center bottom;
  animation: siri-wave 0.72s cubic-bezier(0.45, 0.15, 0.55, 1) infinite alternate;

  @for $i from 1 through $line-count {
    &:nth-child(#{$i}) {
      animation-delay: #{($i - 1) * 0.06}s;
    }
  }
}

@keyframes siri-wave {
  0% {
    transform: scaleY(0.28);
    opacity: 0.5;
  }
  100% {
    transform: scaleY(1.42);
    opacity: 1;
  }
}
```

Ajusta duración, alturas y colores a tu **design system**.

---

## Ideas de uso en producto

| Contexto | Patrón |
|----------|--------|
| **Botón** “Generar con IA” | Texto + ondas dentro del botón mientras `loading`; `aria-busy="true"` |
| **Borde de input** | Capa `conic-gradient` cuadrada detrás + relleno sólido del campo encima |
| **Chip** en cabecera | Mismo truco en forma pill + punto con pulso |
| **Lista / fila** | Barra delgada indeterminada bajo la fila que se está enriqueciendo con IA |

---

## Accesibilidad

- **`aria-busy="true"`** en el control o contenedor que está procesando.
- **`aria-live="polite"`** si el texto del botón o del estado cambia (“Generando…”).
- **`prefers-reduced-motion: reduce`**: `animation: none` en spin, ondas y pulsos; deja color/texto estático o un indicador mínimo.

---

## Demo en vivo

En esta web: **[Abrir demo](/tips/2026/3/19/css-indicador-ia-animacion-siri/demo)** — incluye:

1. Botón con ondas + halo al cargar  
2. Input con anillo neón (conic detrás del campo)  
3. Mismo input + orbe (corona + partículas; toggle para ocultar solo el borde del orbe)  
4. Chip “IA activa” / “En espera” con borde cónico y pulso opcional  

El código fuente del demo está en el repo (`siri-ai-demo.component.ts`): puedes copiar clases y adaptar a Vue, React o HTML plano.
