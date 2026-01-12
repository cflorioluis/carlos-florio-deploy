# CSS Grid: Auto-fit vs Auto-fill 📐

¿Alguna vez has usado CSS Grid y te has preguntado cuál es la diferencia entre `auto-fit` y `auto-fill`? Ambos crean columnas responsivas automáticamente, pero se comportan de manera diferente cuando hay espacio extra.

## La Diferencia Clave

```css
/* auto-fill: Crea columnas vacías si hay espacio */
.grid-fill {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 1rem;
}

/* auto-fit: Expande las columnas existentes para llenar el espacio */
.grid-fit {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 1rem;
}
```

## ¿Cuándo usar cada uno?

### Usa `auto-fill` cuando:
- Quieres mantener el tamaño de los elementos consistente
- Prefieres espacio vacío en lugar de elementos más anchos
- Estás creando una galería de imágenes con tamaños fijos

```css
.image-gallery {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 20px;
}
```

### Usa `auto-fit` cuando:
- Quieres que los elementos se expandan para llenar el espacio disponible
- Prefieres evitar espacios vacíos
- Estás creando layouts de tarjetas que deben ocupar todo el ancho

```css
.card-layout {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 24px;
}
```

## Ejemplo Visual

Imagina que tienes un contenedor de 1000px de ancho y elementos con `minmax(200px, 1fr)`:

**Con `auto-fill`:**
- Se crean 5 columnas (1000px ÷ 200px = 5)
- Si solo tienes 3 elementos, quedan 2 columnas vacías
- Cada elemento mide exactamente 200px

**Con `auto-fit`:**
- Se crean 5 columnas inicialmente
- Las columnas vacías se colapsan
- Los 3 elementos se expanden para ocupar ~333px cada uno

## Tip Pro 💡

Combina `auto-fit` con `minmax()` para crear layouts verdaderamente responsivos sin media queries:

```css
.responsive-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(min(100%, 300px), 1fr));
  gap: 2rem;
}
```

La función `min(100%, 300px)` asegura que en pantallas pequeñas, los elementos ocupen el 100% del ancho disponible, evitando overflow horizontal.

---

**Resumen rápido:**
- `auto-fill` = mantiene espacios vacíos
- `auto-fit` = expande elementos para llenar el espacio
- Ambos son perfectos para layouts responsivos sin media queries 🎯
