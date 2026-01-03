# 📐 Tip del Día: CSS Flexbox `gap`

## 💡 ¿Luchando con los márgenes?

Antes, para separar elementos en un layout Flexbox, usábamos `margin` en los hijos:

```css
.hijo {
  margin-right: 1rem;
}
.hijo:last-child {
  margin-right: 0;
}
```

¡Es un dolor de cabeza! Siempre terminas con un margen extra que no quieres al final o al principio.

### ✅ La Solución: La propiedad `gap`

Inspirada en CSS Grid, la propiedad `gap` (y `row-gap`, `column-gap`) ahora funciona perfectamente en **Flexbox**. Define el espacio *entre* los elementos, ignorando los bordes exteriores.

```css
.contenedor {
  display: flex;
  gap: 1rem; /* Espacio horizontal y vertical */
}
```

---

## 🚀 Ventajas Increíbles

1.  **Sin selectores raros**: Olvídate de `:last-child` o el truco del margen negativo.
2.  **Mantenimiento**: Si cambias de Flex a Grid, el `gap` seguirá ahí funcionando igual.
3.  **Multilinea**: Si los elementos hacen wrap a la siguiente línea, `gap` mantendrá la separación vertical automáticamente.

---

## 🛠️ Compatibilidad

Hoy en día, `gap` en Flexbox tiene soporte en todos los navegadores modernos (>95%). Si no necesitas soportar navegadores muy antiguos (como IE11), **es el estándar a seguir**.

**¡Limpia tu CSS sustituyendo márgenes por gaps! 📐✨**
