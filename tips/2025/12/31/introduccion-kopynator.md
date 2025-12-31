# 🌍 Kopynator: Traducciones en la Nube (Cosecha Propia)

Hoy quiero compartir con vosotros algo muy especial en lo que he estado trabajando: **Kopynator**. Es una herramienta de "cosecha propia" diseñada para solucionar uno de los problemas más repetitivos en el desarrollo: la gestión de textos y traducciones.

Actualmente se encuentra en **fase Beta**, y aunque todavía estoy puliendo detalles, ya es funcional y está ayudando a centralizar el contenido de varios proyectos.

---

## 🚀 ¿Qué es Kopynator?

Imagina que quieres cambiar un texto en tu web. Normalmente tendrías que editar un JSON, hacer commit, push y desplegar. Con Kopynator, cambias el texto en un panel de control y **se actualiza en tiempo real** en tu aplicación, sin nuevos despliegues.

---

## 💻 Soporte Multi-Framework

He diseñado SDKs ligeros para que la integración sea cuestión de minutos:

-   **Angular**: Directiva `*kopy` y pipes para una integración reactiva.
-   **React**: Hook `useKopy` y componentes de alto nivel.
-   **Vue**: Plugin global y composición API.
-   **Node.js**: SDK para backend, ideal para emails o mensajes de error dinámicos.

---

## 🛠️ ¿Cómo funciona lo básico?

1.  **Instalas el CLI**: `npx @kopynator/cli init` para configurar tu proyecto.
2.  **Importas el SDK**: Configuras tu `API_KEY`.
3.  **Sustituyes textos**: En lugar de "Hola Mundo", usas `t('welcome.title')`.

A partir de ahí, el control total está en la nube.

---

## 🔗 Próximamente más detalles

Estoy preparando documentación más profunda y casos de uso avanzados. Si quieres echarle un ojo a la fase beta o ver de qué trata, puedes visitar la web oficial:

👉 **[www.kopynator.com](https://www.kopynator.com)**

¡Traducciones dinámicas, desarrollo más rápido!
