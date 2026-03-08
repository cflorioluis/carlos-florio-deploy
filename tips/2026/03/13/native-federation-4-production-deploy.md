# 🧩 Native Federation (4/4): Build de producción, despliegue y buenas prácticas

Último día de la guía: **construir** host y remotes para producción, **desplegarlos** por separado y **buenas prácticas** (URLs de remotes, versionado, errores, seguridad). Todo el código va etiquetado como **[Angular]**, **[React]** o **[Vue]** cuando aplica.

---

## Build de producción: orden y salida

Cada aplicación se construye **por separado**. Los remotes deben estar desplegados (o al menos sus URLs conocidas) para que el host pueda apuntar a ellos en tiempo de ejecución.

- **[Angular] Host:** `ng build` (o `ng build --configuration=production`). La salida suele ser `dist/host/browser/` (o similar). El host incluye la configuración de federation con las **URLs de los remotes en producción**.
- **[React] Remote React:** `npm run build` (Vite). Salida típica: `dist/`. Debe generarse un `remoteEntry.json` (o el archivo que use tu versión de Native Federation) en la raíz del deploy.
- **[Vue] Remote Vue:** `npm run build` (Vite). Igual: `dist/` y el entry del remote accesible por URL.

El **host** no empaqueta el código de los remotes; solo tiene las URLs. En runtime el navegador carga esos remotes desde sus orígenes.

---

## [Angular] URLs de remotes según entorno

En desarrollo usas `http://localhost:4173/remoteEntry.json` y `http://localhost:4174/remoteEntry.json`. En producción debes apuntar a las URLs reales (por ejemplo `https://remote-react.miapp.com/remoteEntry.json`).

Opciones:

**1. federation.config.ts con variables de entorno**

**[Angular] `apps/host/federation.config.ts`**

```typescript
import type { ModuleFederationConfig } from '@angular-architects/native-federation';

const isProd = process.env['NODE_ENV'] === 'production';
const reactRemoteUrl = isProd
  ? 'https://remote-react.miapp.com/remoteEntry.json'
  : 'http://localhost:4173/remoteEntry.json';
const vueRemoteUrl = isProd
  ? 'https://remote-vue.miapp.com/remoteEntry.json'
  : 'http://localhost:4174/remoteEntry.json';

const config: ModuleFederationConfig = {
  name: 'host',
  remotes: {
    remoteReact: reactRemoteUrl,
    remoteVue: vueRemoteUrl,
  },
  shared: {
    '@angular/core': { singleton: true, requiredVersion: '^19.0.0' },
    '@angular/common': { singleton: true, requiredVersion: '^19.0.0' },
    '@angular/common/http': { singleton: true, requiredVersion: '^19.0.0' },
    '@angular/router': { singleton: true, requiredVersion: '^19.0.0' },
    react: { singleton: true, requiredVersion: '^18.0.0' },
    'react-dom': { singleton: true, requiredVersion: '^18.0.0' },
    vue: { singleton: true, requiredVersion: '^3.0.0' },
  },
};

export default config;
```

Sustituye `remote-react.miapp.com` y `remote-vue.miapp.com` por tus dominios. Si tu build no inyecta `process.env.NODE_ENV`, usa un archivo de entorno (por ejemplo `environment.prod.ts`) o un replace en el build.

**2. Configuración en tiempo de ejecución (avanzado)**

Algunas implementaciones permiten inyectar la lista de remotes en runtime (por ejemplo leyendo un JSON desde el servidor o desde un script en el index). Así puedes cambiar URLs sin recompilar el host. Requiere que el host lea esa config antes de llamar a `loadRemoteModule`. La documentación de `@angular-architects/native-federation` indica si soporta remotes dinámicos.

---

## [React] y [Vue] Build del remote y CORS

Los remotes se sirven desde **otro origen** (otro dominio o subdominio). El navegador hará peticiones al remote desde la página del host; el servidor del remote debe devolver cabeceras **CORS** que permitan ese origen.

**[React] y [Vue]** En Vite, en producción no hay “dev server”; quien sirve los estáticos es tu servidor (Nginx, S3 + CloudFront, etc.). Configura allí CORS. Ejemplo **Nginx** para el remote React:

```nginx
location / {
  add_header Access-Control-Allow-Origin "https://host.miapp.com";
  add_header Access-Control-Allow-Methods "GET, OPTIONS";
  add_header Access-Control-Allow-Headers "Content-Type";
  if ($request_method = OPTIONS) {
    return 204;
  }
  try_files $uri $uri/ /index.html;
}
```

Ajusta `Access-Control-Allow-Origin` al origen del host (o a `*` solo si es aceptable para ti). Lo mismo para el remote Vue.

---

## Despliegue por separado

- **Host:** Despliegas el contenido de `dist/host/browser/` (o la salida de `ng build`) en el dominio principal, por ejemplo `https://miapp.com`.
- **Remote React:** Despliegas el contenido de `dist/` del proyecto React en `https://remote-react.miapp.com` (o la ruta que hayas puesto en `remotes`).
- **Remote Vue:** Igual en `https://remote-vue.miapp.com`.

Cada uno puede tener su propio pipeline (CI/CD), su propia versión y su propio ciclo de releases. El host solo necesita que las URLs de los remotes sean correctas y que los remotes respondan con el `remoteEntry` y los chunks esperados.

---

## Buenas prácticas

**1. Versionado de remotes y compatibilidad**

Si actualizas un remote (por ejemplo React) con un cambio breaking en la API expuesta, el host que ya está en producción podría cargar la versión nueva y romperse. Opciones:

- **URLs con versión:** `https://remote-react.miapp.com/v1/remoteEntry.json` y al publicar una v2, `v2/remoteEntry.json`; el host sigue apuntando a v1 hasta que lo actualices.
- **Contratos estables:** los módulos expuestos (por ejemplo `./App`) mantienen la misma firma (mismo default export, mismas props) entre versiones menores.

**2. Fallback y errores**

Si un remote no carga (red, 404, CORS), el host no debería romperse. En los wrappers (donde llamas a `loadRemoteModule`), usa `try/catch` y muestra un mensaje o un placeholder:

**[Angular] Ejemplo en el wrapper**

```typescript
async ngAfterViewInit(): Promise<void> {
  try {
    const module = await this.remoteLoader.loadReactApp();
    // ... montar
  } catch (e) {
    console.error('Remote React no disponible', e);
    this.reactRoot.nativeElement.innerHTML = '<p>Remote React no disponible</p>';
  }
}
```

**3. Seguridad**

- Los remotes se cargan como script; el contenido se ejecuta en el mismo origen de la página (desde el punto de vista del usuario, es la misma pestaña). Solo carga remotes de **orígenes confiados** y mantén tus builds y CDN seguros.
- Si los remotes dependen de datos sensibles, usa autenticación y tokens como harías en una SPA normal; la comunicación host ↔ remote vía CustomEvent no va cifrada por defecto (es en memoria en la misma página), pero no envíes secretos en los eventos si hay riesgo de que otro script los lea.

**4. Performance**

- **Lazy load:** Carga los remotes solo cuando la ruta o la pestaña lo necesite (por ejemplo con `loadRemoteModule` dentro de un guard o al activar una ruta), no todos al arranque.
- **Shared:** Mantén bien configurado `shared` para no duplicar React, Vue o Angular en cada remote.
- **Caché:** Los remotes son estáticos; sirve `remoteEntry.json` y los chunks con cabeceras de caché adecuadas para que el navegador reutilice.

**5. Nombres de remotes**

Usa nombres coherentes entre host y remotes (`remoteReact`, `remoteVue`) y con el `name` de cada `federation.config` para evitar errores difíciles de depurar.

---

## Resumen de la guía (días 9–13)

- **Día 9:** Introducción a microfrontends y por qué Native Federation para Angular + React + Vue.
- **Día 10:** Workspace con host (Angular) y remotes (React, Vue); `federation.config` en cada uno; concepto de host, remote, exposes, shared.
- **Día 11:** Cargar los remotes en el host con `loadRemoteModule`; wrappers para montar React y Vue en Angular; primer “hola mundo” de los tres frameworks en una sola página.
- **Día 12:** Shared dependencies (React, Vue, Angular) y comunicación con CustomEvent (tema, clics, estado del host).
- **Día 13:** Build de producción, URLs por entorno, CORS, despliegue por separado, versionado, errores y seguridad.

Todo el código de la guía está etiquetado como **[Angular]**, **[React]** o **[Vue]** para que sepas en qué proyecto va cada parte. Con esto tienes una base sólida para un microfrontend con host Angular y remotes React y Vue usando Native Federation.

#microfrontends #native-federation #angular #react #vue
