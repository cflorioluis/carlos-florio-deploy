# 🧩 Native Federation (1/4): Workspace y configuración (Angular host + React y Vue remotes)

Segundo día: **conceptos** de host/remote y **shared**, y cómo crear el workspace con **tres proyectos** (Angular, React, Vue) y la **configuración de Native Federation** en cada uno. Al final tendrás los tres proyectos compilando; el host aún no cargará los remotes (eso lo haremos en el día 11).

En todo el tip se indica explícitamente si el código es **[Angular]**, **[React]** o **[Vue]**.

---

## Estructura del workspace

Queremos una carpeta raíz con tres aplicaciones:

```
microfrontends-demo/
├── apps/
│   ├── host/          ← [Angular] Shell que carga los remotes
│   ├── remote-react/  ← [React]  Remote expuesto al host
│   └── remote-vue/    ← [Vue]    Remote expuesto al host
├── package.json       ← Workspace root (npm workspaces o similar)
└── ...
```

- **host:** aplicación Angular que actuará como shell y más adelante cargará los remotes con `loadRemoteModule`.
- **remote-react:** aplicación React (por ejemplo con Vite) que expondrá un componente o módulo.
- **remote-vue:** aplicación Vue (Vite) que expondrá un componente o módulo.

Cada una tiene su propio `package.json`, su build y su **federation.config** (o equivalente) para declarar si es host o remote y qué expone o consume.

---

## Conceptos: host, remote, exposes, remotes, shared

- **Host:** la app que el usuario carga (en nuestro caso Angular). Declara qué **remotes** consume (URLs o nombres).
- **Remote:** una app que **expone** uno o más módulos vía **exposes** (rutas como `./Widget` → archivo que exporta el componente o módulo).
- **Shared:** dependencias que se comparten entre host y remotes para que no se carguen dos veces (por ejemplo una sola instancia de React). Se declaran en ambos lados con la misma versión o política (singleton, requiredVersion).

En Native Federation cada proyecto tiene un archivo de configuración (por ejemplo `federation.config.js` o `federation.config.ts`) donde se define:
- Si es **host**: lista de `remotes` (nombre → URL del remote entry).
- Si es **remote**: lista de `exposes` (nombre lógico → path al archivo que exporta).
- En ambos: `shared` (paquetes y opciones de compartido).

---

## [Angular] Crear la app host

Desde la raíz del workspace (o donde tengas el CLI de Angular):

```bash
npx -p @angular/cli ng new host --routing --style=css --ssr=false
cd host
```

Instalar Native Federation para Angular (usa la versión compatible con tu Angular; aquí se indica el paquete genérico):

```bash
npm i @angular-architects/native-federation
```

Añadir el plugin de Native Federation al build de Angular. En Angular 17+ con aplicación standalone, el archivo de configuración del proyecto suele estar en `project.json` o en `angular.json`. Necesitamos que el build use el esquema que soporta Native Federation (por ejemplo `@angular-architects/native-federation:build` o la integración oficial que indique la doc actual). Aquí asumimos que se añade un archivo **federation.config.ts** en la raíz del proyecto **host** que el plugin lee.

**[Angular] `apps/host/federation.config.ts`**

```typescript
import type { ModuleFederationConfig } from '@angular-architects/native-federation';

const config: ModuleFederationConfig = {
  name: 'host',
  remotes: {
    // En el día 11 pondremos aquí las URLs; por ahora vacío o con placeholders
    // 'remoteReact': 'http://localhost:4173/remoteEntry.json',
    // 'remoteVue': 'http://localhost:4174/remoteEntry.json',
  },
  shared: {
    '@angular/core': { singleton: true, requiredVersion: '^19.0.0' },
    '@angular/common': { singleton: true, requiredVersion: '^19.0.0' },
    '@angular/common/http': { singleton: true, requiredVersion: '^19.0.0' },
    '@angular/router': { singleton: true, requiredVersion: '^19.0.0' },
  },
};

export default config;
```

- **name:** identifica a este host.
- **remotes:** mapa nombre → URL del `remoteEntry` del remote (lo rellenaremos cuando tengamos los remotes sirviendo).
- **shared:** librerías que el host ofrece y que los remotes pueden usar para no duplicar (Angular core, common, router, etc.). `singleton: true` evita cargar dos versiones.

Asegúrate de que el build de Angular use este config (según la documentación de `@angular-architects/native-federation` puede ser vía `angular.json` o `project.json`). El host por ahora no carga ningún remote; solo dejamos la estructura lista.

---

## [React] Crear el remote React (Vite)

En la raíz del workspace:

```bash
npm create vite@latest remote-react -- --template react-ts
cd remote-react
npm i
```

Instalar el plugin de Native Federation para Vite (nombre puede ser `@softarc/native-federation` o el que indique la doc para Vite; aquí usamos un nombre genérico para el ejemplo):

```bash
npm i native-federation-plugin
```

**[React] `apps/remote-react/federation.config.js`**

```javascript
const { withNativeFederation, shareAll } = require('@softarc/native-federation/build');

module.exports = withNativeFederation({
  name: 'remoteReact',
  exposes: {
    './App': './src/App.tsx',
    // Más adelante podemos exponer './Widget': './src/Widget.tsx'
  },
  shared: {
    ...shareAll({
      singleton: true,
      requiredVersion: false,
      strictVersion: false,
    }),
    react: { singleton: true, requiredVersion: '^18.0.0' },
    'react-dom': { singleton: true, requiredVersion: '^18.0.0' },
  },
});
```

- **name:** debe coincidir con el nombre que use el host para este remote (por ejemplo `remoteReact`).
- **exposes:** cada clave es el nombre que el host usará para cargar el módulo (`loadRemoteModule('remoteReact', './App')`). El valor es la ruta al archivo que exporta el componente o módulo.
- **shared:** compartir React y react-dom en singleton para que el host no cargue otra copia si también usa React, o para que el remote use la misma instancia.

**[React] `apps/remote-react/vite.config.ts`**

Hay que integrar el plugin de Native Federation en Vite. La forma exacta depende del paquete (puede ser `nativeFederation` desde `@softarc/native-federation/vite` o similar). Ejemplo conceptual:

```typescript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import { federation } from 'native-federation-plugin/vite';

export default defineConfig({
  plugins: [
    react(),
    federation({
      configPath: './federation.config.js',
    }),
  ],
  build: {
    target: 'esnext',
    minify: false,
  },
  server: {
    port: 4173,
    cors: true,
  },
});
```

El `port` y `cors` son importantes para que el host pueda cargar el remote en desarrollo. El build debe generar un `remoteEntry` (o `remoteEntry.json`) que el host apuntará en `remotes`.

**[React] `apps/remote-react/src/App.tsx`**

Por ahora un componente mínimo que luego el host cargará:

```tsx
export function App() {
  return (
    <div style={{ padding: '1rem', border: '2px solid #61dafb', borderRadius: 8 }}>
      <h2>Remote React</h2>
      <p>Este contenido viene del remote React.</p>
    </div>
  );
}

export default App;
```

El host cargará este módulo y usará su export por defecto o nombrado para renderizarlo (en el día 11 veremos el wrapper en Angular).

---

## [Vue] Crear el remote Vue (Vite)

```bash
npm create vite@latest remote-vue -- --template vue-ts
cd remote-vue
npm i
```

Instalar el plugin de Native Federation para Vite en el proyecto Vue (mismo paquete que en React, si es el mismo ecosistema):

```bash
npm i native-federation-plugin
```

**[Vue] `apps/remote-vue/federation.config.js`**

```javascript
const { withNativeFederation, shareAll } = require('@softarc/native-federation/build');

module.exports = withNativeFederation({
  name: 'remoteVue',
  exposes: {
    './App': './src/App.vue',
    // O un wrapper que exporte el componente: './App': './src/bootstrap.ts'
  },
  shared: {
    ...shareAll({
      singleton: true,
      requiredVersion: false,
      strictVersion: false,
    }),
    vue: { singleton: true, requiredVersion: '^3.0.0' },
  },
});
```

- **name:** `remoteVue`, el nombre que el host usará para este remote.
- **exposes:** el host cargará `./App` y obtendrá el componente Vue (a veces se expone un archivo `bootstrap.ts` que hace `createApp` y monta el componente, y se exporta la instancia o el componente; depende del plugin).

**[Vue] `apps/remote-vue/vite.config.ts`**

```typescript
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';
import { federation } from 'native-federation-plugin/vite';

export default defineConfig({
  plugins: [
    vue(),
    federation({
      configPath: './federation.config.js',
    }),
  ],
  build: {
    target: 'esnext',
    minify: false,
  },
  server: {
    port: 4174,
    cors: true,
  },
});
```

**[Vue] `apps/remote-vue/src/App.vue`**

Contenido mínimo para identificar el remote:

```vue
<template>
  <div class="remote-vue-box">
    <h2>Remote Vue</h2>
    <p>Este contenido viene del remote Vue.</p>
  </div>
</template>

<script setup lang="ts">
// El host cargará este componente; si el plugin exige un export por defecto:
</script>

<style scoped>
.remote-vue-box {
  padding: 1rem;
  border: 2px solid #42b883;
  border-radius: 8px;
}
</style>
```

Algunos plugins de federation para Vue piden exponer un archivo que hace `createApp(App).mount(...)` y exporta algo que el host pueda montar; en ese caso tendrías por ejemplo `exposes: { './App': './src/bootstrap.ts' }` y en `bootstrap.ts` importas `App.vue`, creas la app y exportas el componente o la instancia. Ajusta según la documentación del plugin que uses.

---

## Orden de trabajo y qué tienes al terminar este día

1. **[Angular]** Host creado, con `federation.config.ts` definiendo `remotes` (vacío o comentado) y `shared` de Angular.
2. **[React]** Remote React con Vite, `federation.config.js` con `exposes: { './App': './src/App.tsx' }` y `shared` de React. `vite.config.ts` con el plugin y `server.port: 4173`.
3. **[Vue]** Remote Vue con Vite, `federation.config.js` con `exposes: { './App': './src/App.vue' }` (o `bootstrap.ts`) y `shared` de Vue. `vite.config.ts` con el plugin y `server.port: 4174`.

Al terminar, puedes ejecutar cada app por separado y comprobar que compilan. En el siguiente tip conectaremos el host Angular a estos remotes y mostraremos el contenido de React y Vue dentro del shell.

---

## Resumen

- **Host (Angular):** `federation.config.ts` con `remotes` (URLs de los remote entries) y `shared` (Angular core, common, router).
- **Remote React:** `federation.config.js` con `exposes: { './App': './src/App.tsx' }` y `shared` (React, react-dom). Vite en puerto 4173.
- **Remote Vue:** `federation.config.js` con `exposes: { './App': './src/App.vue' }` y `shared` (Vue). Vite en puerto 4174.

Todo el código está etiquetado como **[Angular]**, **[React]** o **[Vue]** para que sepas en qué proyecto va cada fragmento. En el día 11 cargaremos estos remotes desde el host y veremos el primer “hola mundo” de cada uno.

#microfrontends #native-federation #angular #react #vue
