# CSS :has() - El Selector Padre 🎨

Durante años, CSS no podía seleccionar elementos padres basándose en sus hijos. ¡Eso cambió con `:has()`! Este selector revolucionario te permite aplicar estilos a un elemento padre dependiendo de qué hijos tenga.

## ¿Por qué es revolucionario?

Antes de `:has()`, si querías estilizar un contenedor basándote en su contenido, necesitabas JavaScript. Ahora puedes hacerlo con CSS puro.

## Sintaxis Básica

```css
/* Selecciona elementos que TIENEN cierto hijo */
selector:has(child-selector) {
  /* estilos */
}
```

## Ejemplos Prácticos

### 1. Estilizar Formularios con Errores
```css
/* Cambia el borde del formulario si tiene un input con error */
form:has(input.error) {
  border: 2px solid red;
  background-color: #fff5f5;
}

/* Añade un ícono de advertencia al label si su input tiene error */
label:has(+ input.error) {
  color: red;
}
label:has(+ input.error)::before {
  content: '⚠️ ';
}
```

```html
<form>
  <label for="email">Email</label>
  <input id="email" type="email" class="error">
  <!-- El formulario y el label se estilizan automáticamente -->
</form>
```

### 2. Cards con Imágenes vs Sin Imágenes
```css
/* Card normal */
.card {
  padding: 1rem;
  background: white;
  border-radius: 8px;
}

/* Card con imagen: ajusta el padding */
.card:has(img) {
  padding: 0;
}

.card:has(img) .card-content {
  padding: 1rem;
}

/* Card sin imagen: añade un borde */
.card:not(:has(img)) {
  border: 2px solid #e0e0e0;
}
```

### 3. Listas con Checkboxes Seleccionados
```css
/* Resalta el item de lista cuando su checkbox está checked */
li:has(input[type="checkbox"]:checked) {
  background-color: #e8f5e9;
  border-left: 4px solid #4caf50;
}

/* Tacha el texto cuando está checked */
li:has(input[type="checkbox"]:checked) label {
  text-decoration: line-through;
  opacity: 0.6;
}
```

```html
<ul>
  <li>
    <input type="checkbox" id="task1">
    <label for="task1">Completar el proyecto</label>
  </li>
  <li>
    <input type="checkbox" id="task2" checked>
    <label for="task2">Revisar código</label>
    <!-- Este item se estiliza automáticamente -->
  </li>
</ul>
```

### 4. Navegación con Dropdown Abierto
```css
/* Cambia el estilo del nav cuando un dropdown está abierto */
nav:has(.dropdown.open) {
  background-color: rgba(0, 0, 0, 0.95);
}

/* Oscurece otros items cuando hay un dropdown abierto */
nav:has(.dropdown.open) .nav-item:not(:has(.dropdown.open)) {
  opacity: 0.5;
}
```

### 5. Contenedor con Contenido Vacío
```css
/* Muestra un mensaje cuando el contenedor está vacío */
.container:not(:has(*)) {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 200px;
}

.container:not(:has(*))::before {
  content: 'No hay contenido disponible';
  color: #999;
  font-style: italic;
}
```

## Casos de Uso Avanzados

### 6. Tablas con Filas Seleccionadas
```css
/* Estiliza la tabla cuando tiene filas seleccionadas */
table:has(tr.selected) {
  border: 2px solid #2196f3;
}

/* Muestra acciones bulk solo cuando hay selección */
.bulk-actions {
  display: none;
}

table:has(tr.selected) ~ .bulk-actions {
  display: flex;
}
```

### 7. Grid Responsivo Inteligente
```css
/* Ajusta el grid basándose en cuántos items tiene */
.grid {
  display: grid;
  gap: 1rem;
}

/* 1 item: ocupa todo el ancho */
.grid:has(.item:only-child) {
  grid-template-columns: 1fr;
}

/* 2-3 items: 2 columnas */
.grid:has(.item:nth-child(2)):not(:has(.item:nth-child(4))) {
  grid-template-columns: repeat(2, 1fr);
}

/* 4+ items: 3 columnas */
.grid:has(.item:nth-child(4)) {
  grid-template-columns: repeat(3, 1fr);
}
```

### 8. Formulario con Campos Requeridos Vacíos
```css
/* Deshabilita el botón submit si hay campos requeridos vacíos */
form:has(input:required:invalid) button[type="submit"] {
  opacity: 0.5;
  cursor: not-allowed;
  pointer-events: none;
}

/* Muestra un mensaje de ayuda */
form:has(input:required:invalid)::after {
  content: 'Por favor completa todos los campos requeridos';
  display: block;
  color: #f44336;
  margin-top: 1rem;
  font-size: 0.875rem;
}
```

## Combinaciones Poderosas

### Con :not()
```css
/* Selecciona divs que NO tienen párrafos */
div:not(:has(p)) {
  background-color: #f5f5f5;
}
```

### Con :is()
```css
/* Selecciona articles que tienen headings o imágenes */
article:has(:is(h1, h2, h3, img)) {
  margin-bottom: 2rem;
}
```

### Anidado
```css
/* Selecciona sections que tienen articles con imágenes */
section:has(article:has(img)) {
  background-color: #fafafa;
}
```

## Tip Pro 💡

Usa `:has()` para crear componentes "auto-conscientes" que se adaptan a su contenido:

```css
/* Card que se adapta automáticamente */
.card {
  display: flex;
  flex-direction: column;
}

/* Si tiene imagen, usa layout horizontal en desktop */
.card:has(img) {
  flex-direction: row;
}

/* Si tiene más de 3 párrafos, usa columnas */
.card:has(p:nth-of-type(3)) .content {
  column-count: 2;
  column-gap: 2rem;
}

/* Si tiene un botón, añade padding extra abajo */
.card:has(button) {
  padding-bottom: 2rem;
}
```

## Compatibilidad

`:has()` tiene excelente soporte en navegadores modernos:

- ✅ Chrome 105+
- ✅ Firefox 121+
- ✅ Safari 15.4+
- ✅ Edge 105+

```css
/* Fallback para navegadores antiguos */
@supports not (selector(:has(*))) {
  /* Estilos alternativos o usa JavaScript */
  .card {
    /* estilos por defecto */
  }
}
```

## Rendimiento ⚡

`:has()` es eficiente, pero ten cuidado con selectores muy complejos:

```css
/* ✅ Bueno - específico y simple */
form:has(input.error) { }

/* ⚠️ Cuidado - puede ser lento en listas grandes */
body:has(.modal-open) * { }

/* ❌ Evitar - demasiado complejo */
div:has(section:has(article:has(p:has(span)))) { }
```

---

**Resumen rápido:**
- `:has()` selecciona padres basándose en sus hijos
- Elimina la necesidad de JavaScript para muchos casos
- Perfecto para formularios, cards, listas y layouts adaptativos
- Excelente soporte en navegadores modernos
- El selector que CSS necesitaba desde hace años 🚀
