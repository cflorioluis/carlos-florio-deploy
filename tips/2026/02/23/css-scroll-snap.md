# 📌 Tip del Día: CSS Scroll Snap - Carruseles sin JavaScript

¿Cansado de carruseles que dependen de librerías pesadas? **Scroll Snap** te permite crear secciones que "encajan" al hacer scroll con **CSS puro**.

## Contenedor y elementos

```css
.carrusel {
  display: flex;
  overflow-x: auto;
  scroll-snap-type: x mandatory;
  gap: 1rem;
  scroll-padding: 1rem;
}

.carrusel-item {
  flex: 0 0 80%;
  scroll-snap-align: center;
  scroll-snap-stop: always;
}
```

- **scroll-snap-type: x mandatory**: el scroll horizontal "obliga" a encajar en un punto.
- **scroll-snap-align**: `start` | `center` | `end` — dónde se alinea el elemento.
- **scroll-snap-stop: always**: evita que pasen varios items de golpe (opcional).

## Suavidad

```css
.carrusel {
  scroll-behavior: smooth;
}
```

## Pro tip

En móvil, si quieres una "tarjeta" por vista:

```css
.carrusel-item {
  flex: 0 0 85%;
  scroll-snap-align: center;
}
```

Menos JS, menos bugs, mejor rendimiento. 🚀

#css #frontend #ux #scroll #carrusel
