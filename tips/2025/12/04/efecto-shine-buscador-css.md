# 💡 Tip del Día: Efecto Shine - Animación de Luz en el Borde

## ¿Qué es el efecto Shine?

El **efecto Shine** es una animación CSS que crea una luz que recorre continuamente el borde de un elemento, similar al efecto que puedes ver en [CodeWiki.google](https://codewiki.google). Es un efecto visual elegante que añade dinamismo a elementos como inputs, botones o contenedores.

**💡 Haz clic en el botón "Ver Demo en Vivo" arriba para ver la animación en acción.**

---

## 🎯 Características del Efecto

- ✨ **Animación continua**: La luz recorre el borde de forma circular e infinita
- 🌈 **Adaptación a esquinas redondeadas**: Sigue perfectamente el `border-radius` del elemento
- 🎨 **Tema adaptable**: Funciona tanto en modo claro como oscuro
- 🔄 **Múltiples capas**: Usa dos pseudo-elementos para crear un efecto más rico y continuo

---

## 📐 Estructura Base

El efecto se basa en una clase reutilizable que puedes aplicar a cualquier elemento:

```html
<div class="search-wrapper shine">
  <input type="text" class="search-input" />
</div>
```

**Requisitos del elemento:**
- Debe tener `position: relative` (o `absolute`/`fixed`)
- Debe tener `overflow: hidden` para contener el efecto
- Puede tener `border-radius` para esquinas redondeadas

---

## 🎨 Implementación con Tailwind CSS

### Configuración de Tailwind

Primero, necesitas configurar las animaciones personalizadas en tu `tailwind.config.js`:

```javascript
module.exports = {
  theme: {
    extend: {
      keyframes: {
        'shine-trail': {
          '0%': { 'offset-distance': '0%' },
          '100%': { 'offset-distance': '100%' }
        },
        'shine-trail-offset': {
          '0%': { 'offset-distance': '50%' },
          '100%': { 'offset-distance': '150%' }
        }
      },
      animation: {
        'shine-trail': 'shine-trail 15s infinite linear',
        'shine-trail-offset': 'shine-trail-offset 15s infinite linear'
      }
    }
  }
}
```

### HTML con Clases Tailwind

```html
<div class="relative overflow-hidden rounded-lg border border-gray-300 dark:border-gray-700 p-4 flex items-center gap-2">
  <!-- Pseudo-elementos con clases personalizadas -->
  <div class="shine-before"></div>
  <div class="shine-after"></div>
  
  <input 
    type="text" 
    class="relative z-10 flex-1 bg-transparent outline-none placeholder:text-gray-400"
    placeholder="Buscar..." 
  />
  <span class="relative z-10 text-gray-400">🔍</span>
</div>
```

### CSS Personalizado para Tailwind

Como Tailwind no tiene soporte nativo para `offset-path` y `offset-anchor`, necesitas agregar estas clases personalizadas:

```css
/* Agregar en tu archivo CSS global o en @layer components */
@layer components {
  .shine-before,
  .shine-after {
    @apply absolute w-[200px] aspect-[3/1] blur-[12px] z-[1] pointer-events-none;
    content: '';
    background: radial-gradient(
      100% 100% at right center,
      var(--card-bg) 0%,
      rgba(255, 255, 255, 1) 5%,
      rgba(102, 126, 234, 1) 40%,
      transparent 80%
    );
    offset-path: border-box;
    offset-anchor: 100% 50%;
    transition: opacity 0.2s ease;
  }

  .shine-before {
    @apply opacity-50;
    animation: shine-trail 15s infinite linear;
  }

  .shine-after {
    animation: shine-trail-offset 15s infinite linear;
    offset-distance: 50%;
  }

  /* Modo oscuro */
  .dark .shine-before,
  .dark .shine-after {
    background: radial-gradient(
      100% 100% at right center,
      var(--card-bg) 0%,
      rgba(255, 255, 255, 0.5) 5%,
      rgba(0, 122, 244, 0.8) 40%,
      transparent 80%
    );
  }
}
```

---

## 🎨 Implementación con SASS/SCSS

### Estructura SASS Modular

Crea un archivo `_shine-effect.scss`:

```scss
// Variables
$shine-width: 200px;
$shine-aspect-ratio: 3 / 1;
$shine-blur: 12px;
$shine-duration: 15s;
$shine-opacity: 0.5;

// Colores para modo claro
$shine-color-light: rgba(102, 126, 234, 1);
$shine-color-white: rgba(255, 255, 255, 1);

// Colores para modo oscuro
$shine-color-dark: rgba(0, 122, 244, 0.8);
$shine-color-white-dark: rgba(255, 255, 255, 0.5);

// Mixin para el gradiente
@mixin shine-gradient($white-color, $primary-color) {
  background: radial-gradient(
    100% 100% at right center,
    var(--card-bg) 0%,
    #{$white-color} 5%,
    #{$primary-color} 40%,
    transparent 80%
  );
}

// Clase base
.shine {
  position: relative;
  overflow: hidden;

  // Pseudo-elementos base
  &::before,
  &::after {
    content: '';
    position: absolute;
    width: $shine-width;
    aspect-ratio: $shine-aspect-ratio;
    filter: blur($shine-blur);
    z-index: 1;
    pointer-events: none;
    offset-path: border-box;
    offset-anchor: 100% 50%;
    transition: opacity 0.2s ease;
    
    // Gradiente para modo claro
    @include shine-gradient($shine-color-white, $shine-color-light);
  }

  // Primera capa
  &::before {
    animation: shine-trail $shine-duration infinite linear;
    opacity: $shine-opacity;
  }

  // Segunda capa (con offset)
  &::after {
    animation: shine-trail-offset $shine-duration infinite linear;
    offset-distance: 50%;
  }

  // Modo oscuro
  [data-theme='dark'] & {
    &::before,
    &::after {
      @include shine-gradient($shine-color-white-dark, $shine-color-dark);
    }
  }
}

// Animaciones
@keyframes shine-trail {
  0% {
    offset-distance: 0%;
  }
  100% {
    offset-distance: 100%;
  }
}

@keyframes shine-trail-offset {
  0% {
    offset-distance: 50%;
  }
  100% {
    offset-distance: 150%;
  }
}
```

### Uso con Mixins SASS

Puedes crear un mixin reutilizable para diferentes variantes:

```scss
// Mixin para crear variantes del efecto
@mixin shine-effect(
  $width: 200px,
  $duration: 15s,
  $primary-color: rgba(102, 126, 234, 1),
  $blur: 12px
) {
  position: relative;
  overflow: hidden;

  &::before,
  &::after {
    content: '';
    position: absolute;
    width: $width;
    aspect-ratio: 3 / 1;
    filter: blur($blur);
    z-index: 1;
    pointer-events: none;
    offset-path: border-box;
    offset-anchor: 100% 50%;
    
    background: radial-gradient(
      100% 100% at right center,
      var(--card-bg) 0%,
      rgba(255, 255, 255, 1) 5%,
      $primary-color 40%,
      transparent 80%
    );
  }

  &::before {
    animation: shine-trail $duration infinite linear;
    opacity: 0.5;
  }

  &::after {
    animation: shine-trail-offset $duration infinite linear;
    offset-distance: 50%;
  }
}

// Uso del mixin
.search-wrapper {
  @include shine-effect(
    $width: 200px,
    $duration: 15s,
    $primary-color: rgba(102, 126, 234, 1)
  );
  
  border-radius: 8px;
  border: 1px solid var(--border-color);
  padding: 8px 12px;
}
```

### SASS con Funciones y Loops

Para crear múltiples variantes automáticamente:

```scss
// Función para generar colores
@function shine-color($base-color, $opacity: 1) {
  @return rgba($base-color, $opacity);
}

// Loop para crear variantes de color
$colors: (
  'blue': (102, 126, 234),
  'purple': (147, 51, 234),
  'green': (34, 197, 94),
  'red': (239, 68, 68)
);

@each $name, $rgb in $colors {
  .shine-#{$name} {
    @include shine-effect(
      $primary-color: shine-color($rgb, 1)
    );
  }
}
```

---

## 🔧 Propiedades CSS Avanzadas Utilizadas

### `offset-path: border-box`

Esta propiedad hace que el elemento siga el contorno del borde del elemento padre, incluyendo las esquinas redondeadas. Es la clave para que la luz se adapte perfectamente a cualquier `border-radius`.

**Alternativas:**
- `offset-path: inset(0 round var(--border-radius))` - Más preciso para esquinas específicas
- `offset-path: circle()` - Para formas circulares
- `offset-path: polygon()` - Para formas personalizadas

### `offset-anchor`

Define el punto de anclaje del elemento que se mueve. `100% 50%` significa:
- **100%**: Anclado al borde derecho del elemento
- **50%**: Centrado verticalmente

Esto hace que el segmento de luz "apunte" hacia adelante en la dirección del movimiento.

### `aspect-ratio`

Mantiene la proporción del elemento. `3 / 1` crea un rectángulo horizontal que se ve como un segmento de línea, no como un punto.

---

## 🎨 Personalización

### Con Tailwind (usando variables CSS)

```css
.shine-custom {
  --shine-width: 300px;
  --shine-duration: 10s;
  --shine-color: rgba(255, 0, 0, 1);
}

.shine-custom .shine-before,
.shine-custom .shine-after {
  width: var(--shine-width);
  animation-duration: var(--shine-duration);
  background: radial-gradient(
    100% 100% at right center,
    var(--card-bg) 0%,
    rgba(255, 255, 255, 1) 5%,
    var(--shine-color) 40%,
    transparent 80%
  );
}
```

### Con SASS (usando mixins)

```scss
// Variante rápida
.fast-shine {
  @include shine-effect(
    $duration: 10s,
    $primary-color: rgba(255, 0, 0, 1)
  );
}

// Variante ancha
.wide-shine {
  @include shine-effect(
    $width: 300px,
    $blur: 8px
  );
}
```

---

## ⚠️ Consideraciones y Limitaciones

### Compatibilidad del Navegador

- **`offset-path`**: Requiere navegadores modernos (Chrome 46+, Firefox 72+, Safari 15.4+)
- **`aspect-ratio`**: Requiere navegadores modernos (Chrome 88+, Firefox 89+, Safari 15+)
- Para navegadores antiguos, considera usar `@supports` con un fallback

### Fallback para Navegadores Antiguos

```scss
.shine {
  &::before,
  &::after {
    display: none; // Fallback
    
    @supports (offset-path: border-box) {
      display: block;
    }
  }
}
```

### Rendimiento

- El efecto usa `filter: blur()` que puede ser costoso en dispositivos de bajo rendimiento
- Considera usar `will-change: offset-distance` para optimizar la animación
- En móviles, puedes desactivar el efecto para mejorar el rendimiento

```scss
@media (prefers-reduced-motion: reduce) {
  .shine {
    &::before,
    &::after {
      animation: none;
    }
  }
}
```

---

## 🎯 Casos de Uso

### 1. Inputs de Búsqueda
Añade dinamismo a campos de búsqueda sin distraer.

### 2. Botones Destacados
Resalta botones importantes o de acción principal.

### 3. Tarjetas Interactivas
Añade un efecto sutil a cards que se pueden hacer clic.

### 4. Navegación
Destaca elementos de navegación activos o importantes.

---

## 🚀 Optimizaciones

### Con Tailwind

```css
.shine-before,
.shine-after {
  @apply will-change-[offset-distance];
}
```

### Con SASS

```scss
.shine {
  &::before,
  &::after {
    will-change: offset-distance;
  }
}
```

---

## 📝 Resumen

- ✅ **Tailwind CSS**: Usa clases de utilidad + CSS personalizado para propiedades avanzadas
- ✅ **SASS/SCSS**: Usa mixins y variables para código más mantenible
- ✅ **Reutilizable**: Aplica a cualquier elemento con una clase
- ✅ **Animación continua**: Dos capas que se mueven de forma infinita
- ✅ **Adaptable**: Sigue esquinas redondeadas perfectamente
- ✅ **Temático**: Funciona en modo claro y oscuro
- ⚠️ **Compatibilidad**: Requiere navegadores modernos

---

## 🔗 Recursos

- [MDN: offset-path](https://developer.mozilla.org/en-US/docs/Web/CSS/offset-path)
- [MDN: offset-anchor](https://developer.mozilla.org/en-US/docs/Web/CSS/offset-anchor)
- [Tailwind CSS Documentation](https://tailwindcss.com/docs)
- [SASS Documentation](https://sass-lang.com/documentation)
- [CodeWiki.google](https://codewiki.google) - Inspiración original

---

**¿Te gustó este tip?** ¡Elige tu método preferido (Tailwind o SASS) y aplica el efecto shine a tus elementos! 🚀
