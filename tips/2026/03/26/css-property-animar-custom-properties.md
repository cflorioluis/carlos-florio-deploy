# CSS `@property`: Animar Custom Properties que antes no se podían 🎨

Las CSS Custom Properties (variables) eran estáticas: no podías animar gradientes, transforms o colores de forma fluida. Con `@property`, ahora puedes registrar propiedades CSS personalizadas con tipo, herencia y valor inicial, permitiendo transiciones y animaciones suaves.

---

## El problema: Variables CSS estáticas ❌

```css
.card {
  --color: #3b82f6;
  background: var(--color);
  transition: var(--color) 0.3s ease; /* ❌ No funciona */
}

.card:hover {
  --color: #ef4444;
}
```

**Resultado:** El cambio es abrupto, sin transición. Las variables CSS por defecto no son animables.

---

## La solución: `@property` ✨

```css
@property --card-color {
  syntax: '<color>';
  inherits: false;
  initial-value: #3b82f6;
}

.card {
  background: var(--card-color);
  transition: --card-color 0.3s ease; /* ✅ Ahora funciona */
}

.card:hover {
  --card-color: #ef4444;
}
```

**Resultado:** Transición suave entre `#3b82f6` y `#ef4444`.

---

## Sintaxis completa de `@property` 📐

```css
@property --nombre-propiedad {
  syntax: '<tipo>';
  inherits: true | false;
  initial-value: valor-inicial;
}
```

**Tipos válidos:**

| Sintaxis | Descripción | Ejemplos |
|----------|-------------|----------|
| `<color>` | Color | `#ff0000`, `rgb(255,0,0)`, `hsl(0,100%,50%)` |
| `<length>` | Longitud | `10px`, `2em`, `50%` |
| `<percentage>` | Porcentaje | `0%`, `50%`, `100%` |
| `<angle>` | Ángulo | `90deg`, `1.57rad`, `0.25turn` |
| `<time>` | Tiempo | `0.3s`, `500ms` |
| `<number>` | Número | `1`, `0.5`, `3.14` |
| `<integer>` | Entero | `1`, `2`, `10` |
| `<url>` | URL | `url('/image.jpg')` |
| `<transform-function>+` | Transform | `rotate(45deg)`, `translate(10px,0)` |
| `<custom-ident>` | Identificador personalizado | `flex`, `block` |
| `*` | Cualquier valor | Cualquier valor válido en CSS |

---

## Ejemplo 1: Animar gradientes de fondo 🌈

```css
@property --gradient-angle {
  syntax: '<angle>';
  inherits: false;
  initial-value: 0deg;
}

.gradient-button {
  background: linear-gradient(
    var(--gradient-angle),
    #3b82f6,
    #8b5cf6,
    #ec4899
  );
  transition: --gradient-angle 0.5s ease;
}

.gradient-button:hover {
  --gradient-angle: 180deg;
}
```

**Resultado:** El gradiente rota suavemente de 0° a 180° en hover.

---

## Ejemplo 2: Animar transforms con parámetros variables 🔄

```css
@property --rotation {
  syntax: '<angle>';
  inherits: false;
  initial-value: 0deg;
}

.spin-icon {
  transform: rotate(var(--rotation));
  transition: --rotation 0.5s cubic-bezier(0.34, 1.56, 0.64, 1);
}

.spin-icon:hover {
  --rotation: 360deg;
}
```

**Resultado:** El icono rota con un efecto elástico en hover.

---

## Ejemplo 3: Progress bar animada con `@property` 📊

```css
@property --progress {
  syntax: '<percentage>';
  inherits: false;
  initial-value: 0%;
}

.progress-bar {
  width: var(--progress);
  height: 8px;
  background: linear-gradient(90deg, #3b82f6, #8b5cf6);
  border-radius: 4px;
  transition: --progress 0.6s ease-out;
}

/* Animar desde 0% a 75% */
.progress-bar[data-value="75"] {
  --progress: 75%;
}
```

```html
<div class="progress-bar" data-value="75"></div>
```

**Resultado:** La barra crece suavemente de 0% a 75%.

---

## Ejemplo 4: Animar box-shadow con parámetros variables 💡

```css
@property --shadow-opacity {
  syntax: '<number>';
  inherits: false;
  initial-value: 0;
}

.glow-card {
  box-shadow: 0 0 20px rgba(59, 130, 246, var(--shadow-opacity));
  transition: --shadow-opacity 0.3s ease;
}

.glow-card:hover {
  --shadow-opacity: 0.6;
}
```

**Resultado:** El glow aparece suavemente en hover.

---

## Ejemplo 5: Animar `gap` en grids flexibly 🔧

```css
@property --gap-size {
  syntax: '<length>';
  inherits: false;
  initial-value: 8px;
}

.flex-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: var(--gap-size);
  transition: --gap-size 0.3s ease;
}

.flex-grid:hover {
  --gap-size: 24px;
}
```

**Resultado:** Los elementos del grid se separan suavemente en hover.

---

## Compatibilidad y polyfills 🌐

**Navegadores compatibles (2026):**

- ✅ Chrome/Edge 85+
- ✅ Safari 16.4+
- ✅ Firefox 128+
- ✅ Opera 71+

**Si necesitas soporte para navegadores antiguos:**

```css
/* Fallback sin animación */
.card {
  --color: #3b82f6;
  background: var(--color);
  transition: background 0.3s ease;
}

/* Con @property y animación */
@supports (property: --color) {
  @property --color {
    syntax: '<color>';
    inherits: false;
    initial-value: #3b82f6;
  }

  .card {
    transition: --color 0.3s ease;
  }
}
```

---

## Performance considerations ⚡

1. **Solo registra propiedades que necesites animar:** No registres más `@property` de lo necesario.

2. **Evita animaciones costosas:** Gradient backgrounds y shadows pueden ser pesados en dispositivos móviles.

3. **Usa `will-change` con precaución:**

```css
.card {
  will-change: --card-color; /* Úsalo solo si realmente lo necesitas */
}
```

4. **Prefiere `transform` y `opacity`:** Sigue siendo lo más performante para animaciones.

---

## Ejemplo práctico: Skeleton loading con `@property` 🦴

```css
@property --skeleton-opacity {
  syntax: '<number>';
  inherits: false;
  initial-value: 0.5;
}

.skeleton {
  background: #e5e7eb;
  animation: pulse 2s infinite ease-in-out;
}

@keyframes pulse {
  0%, 100% {
    --skeleton-opacity: 0.5;
  }
  50% {
    --skeleton-opacity: 1;
  }
}

.skeleton-element {
  background: rgba(255, 255, 255, var(--skeleton-opacity));
  transition: background 0.3s ease;
}
```

```html
<div class="skeleton">
  <div class="skeleton-element" style="height: 20px;"></div>
  <div class="skeleton-element" style="height: 60px; margin-top: 12px;"></div>
</div>
```

**Resultado:** Animación suave de pulso en elementos de skeleton.

---

## Resumen

| Concepto | Descripción |
|----------|-------------|
| `@property` | Registra propiedades CSS personalizadas animables |
| `syntax` | Define el tipo de valor (color, length, angle, etc.) |
| `inherits` | Si los elementos hijos heredan la propiedad |
| `initial-value` | Valor inicial obligatorio |
| Soporte | Chrome/Edge 85+, Safari 16.4+, Firefox 128+ |

Con `@property`, puedes animar cualquier cosa que antes era imposible: gradientes, box-shadow, gap, transforms con variables, y mucho más. 🎯

#css #css-features #animation #frontend #webdev
