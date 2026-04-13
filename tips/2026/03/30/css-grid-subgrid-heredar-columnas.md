# CSS Grid `subgrid`: Heredar columnas del grid padre 🎯

`subgrid` permite que un grid hijo herede las columnas (o filas) de su grid padre, eliminando la necesidad de repetir definiciones y alineando elementos perfectamente a través de múltiples niveles de anidamiento.

---

## El problema sin `subgrid` ❌

```css
.grid {
  display: grid;
  grid-template-columns: 1fr 2fr 1fr;
  gap: 16px;
}

.card {
  display: grid;
  grid-template-columns: 1fr 2fr 1fr; /* ❌ Repite la misma definición */
  gap: 16px;
}
```

**Problemas:**
- Duplicación de código
- Si cambias el padre, debes cambiar el hijo
- Gaps no se alinean si cambian entre grids

---

## La solución: `subgrid` ✨

```css
.grid {
  display: grid;
  grid-template-columns: 1fr 2fr 1fr;
  gap: 16px;
}

.card {
  display: grid;
  grid-template-columns: subgrid; /* ✅ Hereda del padre */
}
```

**Beneficios:**
- ✅ Sin duplicación
- ✅ Cambios propagados automáticamente
- ✅ Gaps alineados perfectamente

---

## Sintaxis completa de `subgrid` 📐

```css
/* Heredar solo columnas */
grid-template-columns: subgrid;

/* Heredar solo filas */
grid-template-rows: subgrid;

/* Heredar ambas */
grid-template-columns: subgrid;
grid-template-rows: subgrid;

/* Combinar subgrid con columnas propias */
grid-template-columns: subgrid 1fr;
```

---

## Ejemplo 1: Card con layout heredado 🃏

```css
.dashboard {
  display: grid;
  grid-template-columns: 200px 1fr 150px;
  gap: 24px;
  padding: 24px;
}

.card {
  display: grid;
  grid-template-columns: subgrid;
  background: white;
  border-radius: 8px;
  padding: 16px;
}

.card-header {
  grid-column: 1;
  text-align: center;
}

.card-content {
  grid-column: 2;
}

.card-sidebar {
  grid-column: 3;
  background: #f3f4f6;
  border-radius: 4px;
  padding: 12px;
}
```

```html
<div class="dashboard">
  <div class="card">
    <div class="card-header">Header</div>
    <div class="card-content">Main content here</div>
    <div class="card-sidebar">Sidebar</div>
  </div>

  <div class="card">
    <div class="card-header">Another Card</div>
    <div class="card-content">More content</div>
    <div class="card-sidebar">Actions</div>
  </div>
</div>
```

**Resultado:** Las cards heredan las columnas del dashboard y se alinean perfectamente.

---

## Ejemplo 2: Formulario con labels alineados 📝

```css
.form-grid {
  display: grid;
  grid-template-columns: 150px 1fr;
  gap: 16px;
  max-width: 600px;
}

.form-field {
  display: grid;
  grid-template-columns: subgrid;
  margin-bottom: 16px;
}

.form-label {
  grid-column: 1;
  display: flex;
  align-items: center;
  font-weight: 600;
}

.form-input {
  grid-column: 2;
}

.form-input input {
  width: 100%;
  padding: 8px 12px;
  border: 1px solid #d1d5db;
  border-radius: 4px;
}
```

```html
<form class="form-grid">
  <div class="form-field">
    <label class="form-label" for="name">Name:</label>
    <div class="form-input">
      <input type="text" id="name" name="name" />
    </div>
  </div>

  <div class="form-field">
    <label class="form-label" for="email">Email:</label>
    <div class="form-input">
      <input type="email" id="email" name="email" />
    </div>
  </div>

  <div class="form-field">
    <label class="form-label" for="password">Password:</label>
    <div class="form-input">
      <input type="password" id="password" name="password" />
    </div>
  </div>
</form>
```

**Resultado:** Todos los labels están alineados verticalmente con el mismo ancho.

---

## Ejemplo 3: Tabla con filas como subgrids 📊

```css
.table {
  display: grid;
  grid-template-columns: 100px 1fr 150px 100px;
  gap: 1px;
  background: #d1d5db;
  border-radius: 8px;
  overflow: hidden;
}

.table-header {
  display: grid;
  grid-template-columns: subgrid;
  background: #f3f4f6;
  padding: 12px 16px;
  font-weight: 600;
}

.table-row {
  display: grid;
  grid-template-columns: subgrid;
  background: white;
  padding: 12px 16px;
}

.table-row:hover {
  background: #f9fafb;
}

.table-cell {
  display: flex;
  align-items: center;
}
```

```html
<div class="table">
  <div class="table-header">
    <div class="table-cell">ID</div>
    <div class="table-cell">Name</div>
    <div class="table-cell">Email</div>
    <div class="table-cell">Status</div>
  </div>

  <div class="table-row">
    <div class="table-cell">1</div>
    <div class="table-cell">John Doe</div>
    <div class="table-cell">john@example.com</div>
    <div class="table-cell">Active</div>
  </div>

  <div class="table-row">
    <div class="table-cell">2</div>
    <div class="table-cell">Jane Smith</div>
    <div class="table-cell">jane@example.com</div>
    <div class="table-cell">Inactive</div>
  </div>
</div>
```

**Resultado:** Filas alineadas perfectamente con el header, sin necesidad de `table` HTML.

---

## Ejemplo 4: Combinar subgrid con columnas propias 🧩

```css
.dashboard {
  display: grid;
  grid-template-columns: 1fr 2fr 1fr;
  gap: 24px;
}

.card {
  display: grid;
  /* Columna 1 y 2 del padre + columna propia */
  grid-template-columns: subgrid 100px;
  gap: 16px;
}

.card-main {
  grid-column: 1 / 3; /* Ocupa 2 columnas del subgrid */
  background: white;
  border-radius: 8px;
  padding: 16px;
}

.card-sidebar {
  grid-column: 4; /* Columna propia */
  background: #f3f4f6;
  border-radius: 8px;
  padding: 16px;
}
```

```html
<div class="dashboard">
  <div class="card">
    <div class="card-main">Main content (heredado)</div>
    <div class="card-sidebar">Sidebar (propio)</div>
  </div>
</div>
```

---

## Compatibilidad y polyfills 🌐

**Navegadores compatibles (2026):**

- ✅ Chrome/Edge 117+
- ✅ Firefox 71+
- ✅ Safari 16.5+

**Fallback para navegadores antiguos:**

```css
/* Fallback sin subgrid */
.card {
  display: grid;
  grid-template-columns: 1fr 2fr 1fr;
  gap: 16px;
}

/* Con subgrid (soportado) */
@supports (grid-template-columns: subgrid) {
  .card {
    grid-template-columns: subgrid;
  }
}
```

---

## Consideraciones de performance ⚡

1. **Nesting profundo:** Evita más de 3-4 niveles de subgrid para mantener el rendimiento.

2. **Gaps:** Los gaps se comparten entre grids padre e hijo, lo que ahorra cálculos.

3. **Responsive:** `subgrid` funciona perfectamente con `minmax()` y `auto-fit`:

```css
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 16px;
}

.card {
  display: grid;
  grid-template-columns: subgrid;
}
```

---

## Resumen

| Concepto | Descripción |
|----------|-------------|
| `subgrid` | Hereda columnas/filas del grid padre |
| `grid-template-columns: subgrid` | Hereda columnas del padre |
| `grid-template-rows: subgrid` | Hereda filas del padre |
| Soporte | Chrome/Edge 117+, Firefox 71+, Safari 16.5+ |

Con `subgrid`, eliminas la duplicación de layouts y aseguras alineación perfecta en grids anidados. 🎯

#css #css-grid #subgrid #layout #frontend #webdev
