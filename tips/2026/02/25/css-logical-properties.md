# 📐 Tip del Día: CSS Logical Properties - margin-inline, padding-block

Las **propiedades lógicas** de CSS permiten que tu layout respete **dirección de escritura** (LTR/RTL) y **modo de escritura** (horizontal/vertical) sin duplicar reglas.

## El problema clásico

```css
/* ❌ Físico: asume siempre izquierda/derecha */
.card {
  margin-left: 1rem;
  margin-right: 1rem;
  padding-top: 0.5rem;
  padding-bottom: 0.5rem;
}
```

En árabe o hebreo (RTL) tendrías que invertir con más CSS.

## La solución: lógico

```css
/* ✅ Lógico: se adapta al idioma y al writing-mode */
.card {
  margin-inline: 1rem;   /* izquierda y derecha en LTR */
  padding-block: 0.5rem; /* arriba y abajo */
}
```

## Mapeo rápido

| Físico        | Lógico (inline = horizontal, block = vertical) |
|---------------|--------------------------------------------------|
| margin-left   | margin-inline-start                              |
| margin-right  | margin-inline-end                                |
| margin-top    | margin-block-start                               |
| margin-bottom | margin-block-end                                 |
| padding-left  | padding-inline-start                             |
| width         | inline-size                                      |
| height        | block-size                                       |

## Atajos

- **margin-inline**: start + end
- **margin-block**: start + end
- **padding-inline** / **padding-block**: igual

## Cuándo usarlas

- Sitios multiidioma (RTL/LTR).
- Componentes que quieres reutilizar en distintos contextos.
- Preferencia moderna: muchos equipos usan ya `margin-inline` por defecto.

Un solo conjunto de reglas para todos los modos de escritura. 🌍

#css #frontend #rtl #i18n #layout
