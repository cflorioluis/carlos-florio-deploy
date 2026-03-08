# 🧩 Native Federation (2/4): Cargar remotes React y Vue en el host Angular

Tercer día: **conectar el host Angular** a los remotes **React** y **Vue**: configurar las URLs de los remotes, usar **loadRemoteModule** para cargar los módulos expuestos y **mostrar** el contenido de cada remote dentro del shell. Cada bloque de código va etiquetado como **[Angular]**, **[React]** o **[Vue]**.

Al terminar verás en la misma página: el shell Angular, un bloque “Remote React” y un bloque “Remote Vue”.

---

## [Angular] Activar las URLs de los remotes en federation.config

En el host, descomenta o rellena `remotes` con las URLs donde sirves cada remote. En desarrollo suelen ser `http://localhost:PUERTO/remoteEntry.json` (o el archivo que genere tu build de Native Federation).

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
  },
};

export default config;
```

Ajusta los puertos (4173 React, 4174 Vue) si los cambiaste en los `vite.config.ts` de cada remote. En producción sustituirás estas URLs por las de tus remotes desplegados.

---

## [Angular] loadRemoteModule: cargar el módulo del remote

Native Federation expone una función para cargar un módulo remoto por nombre y ruta. En Angular suele ser `loadRemoteModule` desde `@angular-architects/native-federation`.

**[Angular] Servicio para cargar remotes (opcional pero recomendado)**

Crear un servicio que centralice la carga y evite repetir la lógica en cada componente:

```typescript
// apps/host/src/app/services/remote-loader.service.ts
import { Injectable } from '@angular/core';
import { loadRemoteModule } from '@angular-architects/native-federation';

export type RemoteName = 'remoteReact' | 'remoteVue';

@Injectable({ providedIn: 'root' })
export class RemoteLoaderService {
  async loadReactApp(): Promise<{ default: unknown }> {
    return loadRemoteModule({
      remoteName: 'remoteReact',
      exposedModule: './App',
    });
  }

  async loadVueApp(): Promise<{ default: unknown }> {
    return loadRemoteModule({
      remoteName: 'remoteVue',
      exposedModule: './App',
    });
  }
}
```

La API exacta puede variar (por ejemplo `remoteEntry` en lugar de `remoteName`, o `exposedModule` como string). Ajusta según la documentación de `@angular-architects/native-federation`. El importante es que obtienes un **módulo** que exporta el componente (por defecto o nombrado).

---

## [Angular] Mostrar el remote React en el template

Para **React**, el módulo cargado exporta un componente (función o clase). Necesitas **montarlo en un elemento del DOM**. En Angular puedes usar un `ViewContainerRef` y, con ayuda de `@angular/core`, crear un contenedor y usar ReactDOM (si está disponible en el host) o un wrapper. Como el host es Angular puro, lo habitual es tener un **componente wrapper** que:

1. Obtiene una referencia a un elemento del DOM (por ejemplo con `ViewChild` y `ElementRef`).
2. Carga el módulo remoto con `RemoteLoaderService.loadReactApp()`.
3. Obtiene el export por defecto (el componente React) y usa **ReactDOM.createRoot** para montarlo en ese elemento.

Para poder usar `react-dom` en el host solo para montar el remote, instala React y react-dom en el host (o compártelos vía shared y úsalos desde el módulo cargado). Ejemplo conceptual:

**[Angular] `apps/host/src/app/components/remote-react-wrapper/remote-react-wrapper.component.ts`**

```typescript
import { Component, ElementRef, ViewChild, AfterViewInit, OnDestroy } from '@angular/core';
import { RemoteLoaderService } from '../../services/remote-loader.service';

@Component({
  selector: 'app-remote-react-wrapper',
  standalone: true,
  template: `<div #reactRoot></div>`,
})
export class RemoteReactWrapperComponent implements AfterViewInit, OnDestroy {
  @ViewChild('reactRoot', { static: true }) reactRoot!: ElementRef<HTMLDivElement>;
  private unmount: (() => void) | null = null;

  constructor(private remoteLoader: RemoteLoaderService) {}

  async ngAfterViewInit(): Promise<void> {
    try {
      const module = await this.remoteLoader.loadReactApp();
      const ReactApp = module.default as React.ComponentType;
      if (!ReactApp) return;
      // Asumiendo que el host tiene react-dom para montar:
      const { createRoot } = await import('react-dom/client');
      const root = createRoot(this.reactRoot.nativeElement);
      root.render(React.createElement(ReactApp));
      this.unmount = () => root.unmount();
    } catch (e) {
      console.error('Error loading remote React app', e);
    }
  }

  ngOnDestroy(): void {
    this.unmount?.();
  }
}
```

En un host Angular puro a veces se usa **React.createElement**; para eso hace falta tener `react` como dependencia (o shared). Si prefieres no instalar React en el host, otra opción es que el **remote React** exporte ya un **Web Component** que el host solo inserte en el DOM; entonces el wrapper sería un simple `<div>` con el custom element. Aquí mostramos la variante “host monta el componente React” para que veas el flujo completo.

Ajuste de imports si usas nombres distintos:

```typescript
import * as React from 'react';
```

y en `template`: solo el contenedor:

```html
<div #reactRoot></div>
```

---

## [Angular] Mostrar el remote Vue en el template

Para **Vue**, el módulo remoto puede exportar el componente Vue o una instancia ya creada. Si exporta el componente (Options API o Composition API), en el host necesitas **Vue** y **createApp** para montarlo. Igual que con React, el host puede tener Vue como dependencia solo para montar, o el remote puede exponer un Web Component.

**[Angular] `apps/host/src/app/components/remote-vue-wrapper/remote-vue-wrapper.component.ts`**

```typescript
import { Component, ElementRef, ViewChild, AfterViewInit, OnDestroy } from '@angular/core';
import { RemoteLoaderService } from '../../services/remote-loader.service';

@Component({
  selector: 'app-remote-vue-wrapper',
  standalone: true,
  template: `<div #vueRoot></div>`,
})
export class RemoteVueWrapperComponent implements AfterViewInit, OnDestroy {
  @ViewChild('vueRoot', { static: true }) vueRoot!: ElementRef<HTMLDivElement>;
  private app: { unmount?: () => void } | null = null;

  constructor(private remoteLoader: RemoteLoaderService) {}

  async ngAfterViewInit(): Promise<void> {
    try {
      const module = await this.remoteLoader.loadVueApp();
      const VueApp = module.default;
      if (!VueApp) return;
      const { createApp } = await import('vue');
      const app = createApp(VueApp);
      app.mount(this.vueRoot.nativeElement);
      this.app = app;
    } catch (e) {
      console.error('Error loading remote Vue app', e);
    }
  }

  ngOnDestroy(): void {
    this.app?.unmount?.();
  }
}
```

Si el remote Vue expone un **wrapper** que ya hace `createApp(...).mount(...)` y devuelve la instancia, entonces `module.default` podría ser esa instancia y solo harías `this.app = module.default`. Ajusta según cómo expongas el remote (componente vs instancia montada).

---

## [Angular] Página que muestra los dos remotes

Un componente de página que use los dos wrappers:

**[Angular] `apps/host/src/app/app.component.ts`**

```typescript
import { Component } from '@angular/core';
import { RouterOutlet } from '@angular/router';
import { RemoteReactWrapperComponent } from './components/remote-react-wrapper/remote-react-wrapper.component';
import { RemoteVueWrapperComponent } from './components/remote-vue-wrapper/remote-vue-wrapper.component';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [RouterOutlet, RemoteReactWrapperComponent, RemoteVueWrapperComponent],
  template: `
    <div class="host-shell">
      <header>
        <h1>Host Angular</h1>
        <p>Shell que carga remotes React y Vue</p>
      </header>
      <main>
        <section>
          <h2>Remote React</h2>
          <app-remote-react-wrapper />
        </section>
        <section>
          <h2>Remote Vue</h2>
          <app-remote-vue-wrapper />
        </section>
      </main>
      <router-outlet />
    </div>
  `,
  styles: [`
    .host-shell { font-family: system-ui, sans-serif; padding: 1rem; max-width: 900px; margin: 0 auto; }
    header { margin-bottom: 2rem; }
    section { margin-bottom: 2rem; padding: 1rem; background: #f5f5f5; border-radius: 8px; }
  `],
})
export class AppComponent {}
```

Al abrir el host en el navegador (y con los remotes React y Vue en marcha en 4173 y 4174), deberías ver el header del host y debajo los dos bloques: “Remote React” y “Remote Vue” con el contenido que definiste en cada remote.

---

## [React] Recordatorio: qué expone el remote React

El remote React ya tiene en `federation.config.js`:

```javascript
exposes: {
  './App': './src/App.tsx',
},
```

Y **App.tsx** exporta el componente:

```tsx
// apps/remote-react/src/App.tsx
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

El host carga `remoteReact/./App` y usa el `default` export para montarlo con ReactDOM.

---

## [Vue] Recordatorio: qué expone el remote Vue

El remote Vue expone:

```javascript
exposes: {
  './App': './src/App.vue',
},
```

Si el plugin de federation para Vue requiere un **entry** que exporte el componente (por ejemplo `bootstrap.ts`), crea algo así:

**[Vue] `apps/remote-vue/src/bootstrap.ts` (opcional)**

```typescript
import App from './App.vue';
export { App };
export default App;
```

Y en `federation.config.js`:

```javascript
exposes: {
  './App': './src/bootstrap.ts',
},
```

Así el host recibe un módulo con `default` = componente Vue y puede hacer `createApp(module.default).mount(...)`.

---

## Orden de ejecución en desarrollo

1. Arrancar **remote React**: `cd apps/remote-react && npm run dev` (puerto 4173).
2. Arrancar **remote Vue**: `cd apps/remote-vue && npm run dev` (puerto 4174).
3. Arrancar **host Angular**: `cd apps/host && ng serve` (por ejemplo puerto 4200).

Abrir `http://localhost:4200`. El host cargará `http://localhost:4173/remoteEntry.json` y `http://localhost:4174/remoteEntry.json`, luego los módulos `./App` de cada uno y los mostrará en los wrappers. Si hay errores CORS, asegura `cors: true` en los servidores de desarrollo de React y Vue.

---

## Resumen

- **[Angular]** En `federation.config.ts` defines las URLs de `remoteReact` y `remoteVue`.
- **[Angular]** Un `RemoteLoaderService` usa `loadRemoteModule` para cargar `./App` de cada remote.
- **[Angular]** Dos componentes wrapper: uno que monta el componente React con `createRoot` y otro que monta el componente Vue con `createApp().mount()`.
- **[React]** El remote expone `./App` → `App.tsx` con un componente exportado por defecto.
- **[Vue]** El remote expone `./App` → `App.vue` (o `bootstrap.ts`) para que el host pueda hacer `createApp(default).mount()`.

Con esto el ejemplo ya muestra contenido de **Angular**, **React** y **Vue** en la misma página. En el día 12 veremos shared dependencies y comunicación entre host y remotes.

#microfrontends #native-federation #angular #react #vue
