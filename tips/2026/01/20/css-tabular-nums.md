# Alinear Números con tabular-nums 🔢

¿Alguna vez has notado que las columnas de números en tus tablas o dashboards no se alinean correctamente? Esto sucede porque la mayoría de las fuentes modernas usan "proportional figures", donde cada número tiene un ancho diferente (el "1" es más estrecho que el "8").

Para solucionar esto de manera profesional, existe una propiedad de CSS diseñada específicamente para este fin.

**💡 Haz clic en el botón "Ver Demo en Vivo" arriba para interactuar con este efecto.**

---

## El Problema: Números Proporcionales

Por defecto, los números en la web suelen tener anchos variables. Esto significa que si tienes una lista de precios o estadísticas, los decimales y las comas no coincidirán verticalmente. 

Por ejemplo, el número `11.11` ocupa mucho menos espacio horizontal que `88.88`, lo que desalinea tus columnas y dificulta la lectura rápida de datos financieros.

## La Solución: font-variant-numeric

Para forzar al navegador a usar anchos uniformes para todos los dígitos, puedes usar la propiedad `font-variant-numeric` con el valor `tabular-nums`.

```css
.stats-container {
  /* La magia ocurre aquí */
  font-variant-numeric: tabular-nums;
  
  /* Fallback para navegadores antiguos */
  font-feature-settings: "tnum";
}
```

## ¿Qué hace exactamente?

Al activar `tabular-nums`, el navegador utiliza los glifos de "ancho fijo" de la fuente (si la fuente los soporta). Esto transforma a todos los números en elementos monospaciados, manteniendo el resto del texto con su espaciado proporcional normal.

### Beneficios:
1.  **Lectura Vertical**: Los puntos decimales y las comas se alinean perfectamente.
2.  **Estabilidad Visual**: Evita el molesto "salto" horizontal cuando los números cambian rápidamente (como en un cronómetro o contador).
3.  **Estética Profesional**: Es el estándar en aplicaciones financieras y de análisis de datos.

## Ejemplo de Código

```html
<div class="stats">
  <p>1234567,89</p>
  <p>1111111,11</p>
  <p>7777777,77</p>
</div>
```

```css
.stats {
  font-variant-numeric: tabular-nums;
}
```

---

**Resumen rápido:**
- Los números por defecto tienen anchos variables.
- `font-variant-numeric: tabular-nums` los alinea verticalmente.
- Indispensable para tablas, dashboards y cronómetros.
- Compatible con la gran mayoría de fuentes modernas del sistema e interfaces.
