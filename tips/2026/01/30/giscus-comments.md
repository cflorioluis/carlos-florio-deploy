# 💡 Tip del Día: Comentarios "Serverless" con Giscus 💬

¿Tienes un blog estático o un portafolio personal y echas de menos los comentarios? 🤔

Añadir una base de datos solo para esto suele ser "matar moscas a cañonazos". Hoy te presento **Giscus**, la solución elegante que uso en esta misma web.

## 🚀 ¿Qué es Giscus?

Es un sistema de comentarios impulsado por **GitHub Discussions**. Básicamente, usa la API de GitHub para almacenar los comentarios y reacciones de tu web como si fueran discusiones en un repositorio.

### ✨ Ventajas
1.  **Sin Base de Datos**: GitHub es tu backend. Gratis y confiable.
2.  **Cero Spam**: Requiere cuenta de GitHub para comentar (adiós bots).
3.  **Persistencia**: Los datos son tuyos, viven en tu repositorio.
4.  **Estilo**: Soporta temas claros, oscuros y personalizados.
5.  **Open Source**: Transparente y seguro.

## 🛠️ ¿Cómo se implementa?

Solo necesitas configurar un script en tu HTML (o componente Angular/React):

```html
<script src="https://giscus.app/client.js"
        data-repo="tu-usuario/tu-repo"
        data-repo-id="..."
        data-category="Announcements"
        data-category-id="..."
        data-mapping="pathname"
        data-reactions-enabled="1"
        data-theme="preferred_color_scheme"
        data-lang="es"
        crossorigin="anonymous"
        async>
</script>
```

Puedes obtener tu configuración exacta en [giscus.app](https://giscus.app).

---

### 👇 ¡Pruébalo ahora mismo!
¿Ves la caja de comentarios de abajo? **¡Es Giscus en acción!**
Deja un saludo, una reacción o cuéntame qué sistema usas tú.

#webdev #frontend #jamstack #opensource #github
