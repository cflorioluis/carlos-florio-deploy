# Efecto Espejo con CSS Puro 🪞

¿Sabías que puedes crear un efecto de reflexión o "espejo" en tus imágenes con una sola propiedad de CSS? Sin necesidad de duplicar imágenes, usar pseudo-elementos o trucos complejos de gradientes.

Se trata de la propiedad `-webkit-box-reflect`.

**💡 Haz clic en el botón "Ver Demo en Vivo" arriba para ver la reflexión en acción.**

---

## La Magia: -webkit-box-reflect

Con esta propiedad puedes proyectar una copia del elemento en cualquier dirección.

```css
img {
  width: 300px;
  -webkit-box-reflect: below -5px linear-gradient(transparent, rgba(0, 0, 0, 0.5));
}
```

## ¿Qué está pasando aquí?

La propiedad `-webkit-box-reflect` acepta tres parámetros principales:

1.  **Dirección**: `below` coloca la reflexión debajo del elemento (también puedes usar `above`, `left`, `right`).
2.  **Desplazamiento**: `-5px` es el espacio entre la imagen y su reflexión.
3.  **Máscara (opcional)**: `linear-gradient(...)` permite que la reflexión se desvanezca suavemente, dando un efecto mucho más realista.

## Ejemplo Práctico

Imagina que tienes una imagen de Charmeleon y quieres darle ese efecto de reflexión profesional.

![Charmeleon original sin reflexión](/tips/2026/01/19/charmeleon.png)

## Aplicando el Efecto

Al aplicar el CSS mencionado arriba, el navegador genera automáticamente la reflexión, dándole este aspecto:

```css
.product-shot {
  -webkit-box-reflect: below 0px linear-gradient(transparent, rgba(0, 0, 0, 0.4));
}
```

## ¿Cuándo usarlo?

Este efecto es súper útil para:
- **Fotos de productos**: Imprescindible para ecommerce de calzado o electrónica.
- **Hero sections**: Da una sensación de profundidad.
- **Interfaces Modernas**: Perfecto para lograr el estilo "glassy" o Apple-like.

## ⚠️ Compatibilidad y Progressive Enhancement

Es importante recordar que esta propiedad funciona en **Safari, Chrome y Edge**, pero **no está soportada en Firefox**.

Sin embargo, es un candidato perfecto para **Progressive Enhancement**: los usuarios de navegadores compatibles verán el efecto especial, mientras que el resto verá la imagen normal. ¡Lujoso y seguro al mismo tiempo!

---

**Resumen rápido:**
- `-webkit-box-reflect` crea reflexiones instantáneas.
- Soporta dirección, espacio (gap) y máscaras de gradiente.
- Ideal para un toque elegante en fotos de producto.
- Usa CSS progresivo para no romper la experiencia en Firefox.
