# 🎨 Tip del Día: Valores Arbitrarios en Tailwind CSS

## 💡 ¿Te falta una clase en Tailwind?

A veces necesitas un valor muy específico que no está en la configuración por defecto de Tailwind (como un `top: 117px` o un color exacto de marca). 

No hace falta que vuelvas a escribir CSS puro en un archivo aparte.

### ✅ La Solución: Valores Arbitrarios `[]`

Tailwind permite usar corchetes para generar clases sobre la marcha con cualquier valor que necesites.

```html
<!-- Un color exacto de fondo -->
<div class="bg-[#1da1f2]">...</div>

<!-- Un valor de posición específico -->
<div class="top-[117px]">...</div>

<!-- Incluso para grid dinámico -->
<div class="grid-cols-[1fr_500px_2fr]">...</div>
```

---

## 🚀 ¿Por qué es útil?

1.  **Sin salir del HTML**: Mantienes la velocidad de desarrollo de Tailwind sin crear clases "one-off" en el config.
2.  **Mantiene la especificidad**: Tailwind se encarga de generar el CSS correctamente.
3.  **Funciona con modificadores**: Puedes usarlo con `hover:`, `md:`, etc.
    ```html
    <div class="hover:bg-[#ff00cc]">...</div>
    ```

---

## 🛠️ Ejemplo Pro: Modificar variables CSS

Si tienes variables de CSS definidas, también puedes usarlas:

```html
<div class="bg-[var(--mi-color-personalizado)]">...</div>
```

**¡Rompe las limitaciones del config con los valores arbitrarios! 🎨✨**
