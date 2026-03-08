# 🧩 Native Federation (3/4): Shared dependencies y comunicación host ↔ remotes

Cuarto día: **compartir dependencias** entre host y remotes para no duplicar React, Vue o Angular, y **comunicación** entre el host Angular y los remotes React y Vue (eventos, datos hacia abajo). Todo el código va etiquetado como **[Angular]**, **[React]** o **[Vue]**.

---

## Shared: por qué y cómo

Si el **host** monta componentes **React** y **Vue**, necesita las librerías `react`, `react-dom` y `vue` para hacer `createRoot` y `createApp`. Si cada remote trae su propia copia, tendrías varias instancias de React/Vue en la misma página: más peso y posibles conflictos (por ejemplo varios “React roots”). **Shared** indica que esa dependencia se carga una sola vez y se comparte entre host y remotes.

- En el **host** declaras en `shared` qué ofreces (p. ej. `react`, `react-dom`, `vue`) y con qué política (`singleton: true`, `requiredVersion`).
- En cada **remote** declaras las mismas dependencias en `shared` con la misma versión (o compatible). El runtime de federation resuelve quién provee la instancia.

Así el host puede usar React/Vue solo para montar los remotes y todos comparten la misma instancia.

---

## [Angular] Shared en el host

**[Angular] `apps/host/federation.config.ts`**

```typescript
import type { ModuleFederationConfig } from '@angular-architects/native-federation';

const config: ModuleFederationConfig = {
  name: 'host',
  remotes: {
    remoteReact: 'http://localhost:4173/remoteEntry.json',
    remoteVue: 'http://localhost:4174/remoteEntry.json',
  },
  shared: {
    '@angular/core': { singleton: true, requiredVersion: '^19.0.0' },
    '@angular/common': { singleton: true, requiredVersion: '^19.0.0' },
    '@angular/common/http': { singleton: true, requiredVersion: '^19.0.0' },
    '@angular/router': { singleton: true, requiredVersion: '^19.0.0' },
    // Para que el host pueda montar los remotes y compartir con ellos:
    react: { singleton: true, requiredVersion: '^18.0.0' },
    'react-dom': { singleton: true, requiredVersion: '^18.0.0' },
    vue: { singleton: true, requiredVersion: '^3.0.0' },
  },
};

export default config;
```

Con esto el host “ofrece” React, react-dom y Vue; los remotes que declaren las mismas en su `shared` usarán esa instancia.

---

## [React] Shared en el remote React

**[React] `apps/remote-react/federation.config.js`**

```javascript
const { withNativeFederation, shareAll } = require('@softarc/native-federation/build');

module.exports = withNativeFederation({
  name: 'remoteReact',
  exposes: {
    './App': './src/App.tsx',
  },
  shared: {
    react: { singleton: true, requiredVersion: '^18.0.0' },
    'react-dom': { singleton: true, requiredVersion: '^18.0.0' },
    // Otras que quieras compartir (rxjs, etc.)
  },
});
```

Versiones alineadas con el host para que el singleton funcione.

---

## [Vue] Shared en el remote Vue

**[Vue] `apps/remote-vue/federation.config.js`**

```javascript
const { withNativeFederation, shareAll } = require('@softarc/native-federation/build');

module.exports = withNativeFederation({
  name: 'remoteVue',
  exposes: {
    './App': './src/App.vue',
  },
  shared: {
    vue: { singleton: true, requiredVersion: '^3.0.0' },
  },
});
```

---

## Comunicación host ↔ remotes

Opciones típicas:

1. **CustomEvent en el DOM:** el host dispara un `CustomEvent`; los remotes hacen `addEventListener` en `window` o en un elemento común. O al revés: el remote dispara y el host escucha.
2. **Props / inputs:** el host pasa datos a los wrappers; cada wrapper los pasa al componente React o Vue (por ejemplo como props o en un pequeño “context” inyectado).
3. **Servicio o bus ligero:** el host proporciona un objeto o Subject (p. ej. vía `provide/inject` o un token global) que los remotes consumen si tienen acceso (por ejemplo si se inyecta en un bridge).

Aquí usamos **CustomEvent** para que el host envíe un “tema” (por ejemplo `theme-changed`) y los remotes reaccionen, y que un remote pueda notificar al host que se hizo clic en algo (por ejemplo `remote-click`). Así ves flujo en ambos sentidos sin acoplar frameworks.

---

## [Angular] El host envía “tema” y escucha “remote-click”

**[Angular] Servicio de eventos (opcional)**

```typescript
// apps/host/src/app/services/federation-events.service.ts
import { Injectable } from '@angular/core';

export const THEME_CHANGED = 'federation-theme-changed';
export const REMOTE_CLICK = 'federation-remote-click';

@Injectable({ providedIn: 'root' })
export class FederationEventsService {
  private theme = 'light';

  setTheme(theme: 'light' | 'dark'): void {
    this.theme = theme;
    window.dispatchEvent(new CustomEvent(THEME_CHANGED, { detail: { theme } }));
  }

  getTheme(): 'light' | 'dark' {
    return this.theme;
  }

  onRemoteClick(callback: (payload: { source: string; value?: unknown }) => void): void {
    window.addEventListener(REMOTE_CLICK, ((e: CustomEvent) => callback(e.detail)) as EventListener);
  }
}
```

**[Angular] App component que usa el servicio y los wrappers**

El host puede tener un botón “Cambiar tema” que llama a `FederationEventsService.setTheme()`. Los remotes escuchan `THEME_CHANGED` y actualizan su UI. Además el host se suscribe a `REMOTE_CLICK` para mostrar en consola o en la UI que un remote notificó un clic.

```typescript
// apps/host/src/app/app.component.ts (fragmento)
constructor(private events: FederationEventsService) {
  this.events.onRemoteClick((detail) => {
    console.log('Remote notificó clic:', detail);
    // detail.source puede ser 'react' o 'vue', detail.value datos opcionales
  });
}
```

Y en el template un control para tema:

```html
<button (click)="events.setTheme('dark')">Tema oscuro</button>
<button (click)="events.setTheme('light')">Tema claro</button>
```

---

## [React] El remote React escucha tema y notifica clics

**[React] `apps/remote-react/src/App.tsx`**

```tsx
import { useState, useEffect } from 'react';

const THEME_CHANGED = 'federation-theme-changed';
const REMOTE_CLICK = 'federation-remote-click';

export function App() {
  const [theme, setTheme] = useState<'light' | 'dark'>('light');

  useEffect(() => {
    const handler = (e: CustomEvent<{ theme: 'light' | 'dark' }>) => {
      setTheme(e.detail.theme);
    };
    window.addEventListener(THEME_CHANGED, handler as EventListener);
    return () => window.removeEventListener(THEME_CHANGED, handler as EventListener);
  }, []);

  const notifyClick = (value: string) => {
    window.dispatchEvent(
      new CustomEvent(REMOTE_CLICK, { detail: { source: 'react', value } })
    );
  };

  return (
    <div
      style={{
        padding: '1rem',
        border: '2px solid #61dafb',
        borderRadius: 8,
        background: theme === 'dark' ? '#333' : '#fff',
        color: theme === 'dark' ? '#eee' : '#111',
      }}
    >
      <h2>Remote React</h2>
      <p>Tema actual: {theme}</p>
      <button onClick={() => notifyClick('botón React')}>
        Notificar clic al host
      </button>
    </div>
  );
}

export default App;
```

El host al cambiar tema dispara `THEME_CHANGED`; este componente actualiza `theme`. Al hacer clic en el botón dispara `REMOTE_CLICK` y el host puede reaccionar.

---

## [Vue] El remote Vue escucha tema y notifica clics

**[Vue] `apps/remote-vue/src/App.vue`**

```vue
<template>
  <div class="remote-vue-box" :class="theme">
    <h2>Remote Vue</h2>
    <p>Tema actual: {{ theme }}</p>
    <button @click="notifyClick('botón Vue')">Notificar clic al host</button>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue';

const THEME_CHANGED = 'federation-theme-changed';
const REMOTE_CLICK = 'federation-remote-click';

const theme = ref<'light' | 'dark'>('light');

function handleThemeChanged(e: CustomEvent<{ theme: 'light' | 'dark' }>) {
  theme.value = e.detail.theme;
}

function notifyClick(value: string) {
  window.dispatchEvent(
    new CustomEvent(REMOTE_CLICK, { detail: { source: 'vue', value } })
  );
}

onMounted(() => {
  window.addEventListener(THEME_CHANGED, handleThemeChanged as EventListener);
});

onUnmounted(() => {
  window.removeEventListener(THEME_CHANGED, handleThemeChanged as EventListener);
});
</script>

<style scoped>
.remote-vue-box {
  padding: 1rem;
  border: 2px solid #42b883;
  border-radius: 8px;
}
.remote-vue-box.dark {
  background: #333;
  color: #eee;
}
.remote-vue-box.light {
  background: #fff;
  color: #111;
}
</style>
```

Misma idea que en React: escucha `THEME_CHANGED` para actualizar `theme` y dispara `REMOTE_CLICK` al pulsar el botón.

---

## Pasar datos del host a los remotes (props)

Si quieres que el host pase datos (por ejemplo el tema o un userId) sin usar solo eventos, puedes:

1. **[Angular]** El wrapper (React o Vue) recibe `@Input() theme` o `@Input() userId` y los pasa al cargar el módulo remoto. Como el componente remoto se monta una vez, puedes usar un **setter** o un **Subject** que el componente remoto consuma vía efecto o watch.
2. **[Angular]** El host pone los datos en un objeto global (por ejemplo `window.__hostState`) que los remotes leen al montar y cuando cambien (por evento).
3. **[Angular]** El host dispara un CustomEvent con el payload (p. ej. `host-state-changed` con `{ theme, userId }`); los remotes escuchan y actualizan su estado.

Ejemplo con evento de “estado del host”:

**[Angular] Disparar estado cuando cambie**

```typescript
// En FederationEventsService o en el componente que tenga el estado
window.dispatchEvent(
  new CustomEvent('host-state-changed', {
    detail: { theme: this.theme, userId: this.userId },
  })
);
```

**[React] Escuchar y usar**

```tsx
useEffect(() => {
  const handler = (e: CustomEvent<{ theme: string; userId?: string }>) => {
    setTheme(e.detail.theme);
    setUserId(e.detail.userId);
  };
  window.addEventListener('host-state-changed', handler as EventListener);
  return () => window.removeEventListener('host-state-changed', handler as EventListener);
}, []);
```

**[Vue] Igual con `onMounted` / `onUnmounted` y un listener que actualice `theme` y `userId`.**

Así el ejemplo crece: shared evita duplicar runtimes y la comunicación (CustomEvent + opcionalmente “props vía evento”) mantiene host y remotes sincronizados. En el día 13 veremos builds de producción y despliegue.

---

## Resumen

- **[Angular]** En `federation.config.ts` añades `react`, `react-dom` y `vue` en `shared` con `singleton: true`.
- **[React]** y **[Vue]** Declaran las mismas dependencias en su `shared` con versiones compatibles.
- **Comunicación:** CustomEvent (`federation-theme-changed`, `federation-remote-click`) para tema y notificación de clics; el host puede emitir estado adicional con eventos como `host-state-changed` y los remotes escuchan y actualizan su UI.

Todo el código está etiquetado por framework. Con esto el ejemplo tiene shared dependencies y comunicación bidireccional; el siguiente tip cierra con producción y buenas prácticas.

#microfrontends #native-federation #angular #react #vue
