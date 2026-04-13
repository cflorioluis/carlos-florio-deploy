# Web Components + Angular: ¿Cuándo usarlos y cuándo no? 🧩

Los Web Components y Angular son tecnologías que pueden coexistir, pero tienen filosofías diferentes. Entender cuándo usar cada uno es crucial para evitar arquitecturas híbridas complejas y mantenibles.

---

## ¿Qué son Web Components? 🤔

Web Components es un estándar del navegador que incluye:
- **Custom Elements:** Crear elementos HTML personalizados
- **Shadow DOM:** Encapsulación de estilos y markup
- **HTML Templates:** Definir templates reutilizables
- **ES Modules:** Importar/exportar código

```javascript
// Ejemplo simple de Web Component
class MyComponent extends HTMLElement {
  connectedCallback() {
    this.innerHTML = `
      <style>
        p { color: red; }
      </style>
      <p>Hello from Web Component!</p>
    `;
  }
}

customElements.define('my-component', MyComponent);
```

---

## Angular vs Web Components: Filosofías diferentes 🔄

| Aspecto | Angular | Web Components |
|---------|---------|----------------|
| **Paradigma** | Framework completo | Estándar del navegador |
| **Encapsulación** | ViewEncapsulation, pero compartido | Shadow DOM real |
| **Data binding** | Two-way binding, signals | Events, attributes, properties |
| **Routing** | Router integrado | No tiene routing |
| **State management** | Services, signals, NgRx | Tú gestionas el estado |
| **Testing** | TestBed, utilities | Web Test Runner, Playwright |
| **Bundle size** | Framework overhead | Solo tu código |
| **Intercambio** | Solo con Angular | Cualquier framework o vanilla JS |

---

## Casos de uso de Web Components ✅

### 1. **Componentes de UI compartidos entre frameworks** 🎨

```javascript
// Button Web Component (usable en Angular, React, Vue, vanilla)
class MyButton extends HTMLElement {
  connectedCallback() {
    const text = this.getAttribute('text') || 'Click me';
    const variant = this.getAttribute('variant') || 'primary';

    this.innerHTML = `
      <style>
        button {
          padding: 8px 16px;
          border-radius: 4px;
          border: none;
          cursor: pointer;
        }
        button.primary { background: #3b82f6; color: white; }
        button.secondary { background: #6b7280; color: white; }
      </style>
      <button class="${variant}">${text}</button>
    `;
  }
}

customElements.define('my-button', MyButton);
```

**Uso en Angular:**

```html
<!-- En cualquier componente Angular -->
<my-button text="Save" variant="primary"></my-button>
```

**Uso en React:**

```jsx
// En React
<MyButton text="Save" variant="primary" />
```

---

### 2. **Librerías de componentes multi-framework** 📦

Web Components son ideales para librerías que deben funcionar en cualquier framework:

```
my-ui-library/
├── components/
│   ├── button.js
│   ├── input.js
│   └── dropdown.js
├── styles/
│   └── shared.css
└── dist/
    ├── button.bundle.js
    ├── input.bundle.js
    └── library.js
```

**Beneficio:** Una sola base de código para Angular, React, Vue, Svelte, etc.

---

### 3. **Widgets embedables** 📱

```javascript
class ChatWidget extends HTMLElement {
  connectedCallback() {
    this.attachShadow({ mode: 'open' });
    this.shadowRoot.innerHTML = `
      <style>
        .widget { position: fixed; bottom: 20px; right: 20px; z-index: 9999; }
        .button { width: 50px; height: 50px; border-radius: 50%; background: #3b82f6; color: white; border: none; cursor: pointer; }
        .chat-window { display: none; width: 300px; height: 400px; background: white; border: 1px solid #d1d5db; border-radius: 8px; }
        .chat-window.open { display: block; }
      </style>
      <div class="widget">
        <button class="button">💬</button>
        <div class="chat-window">
          <div class="messages"></div>
          <input type="text" placeholder="Type a message..." />
        </div>
      </div>
    `;

    const button = this.shadowRoot.querySelector('.button');
    const chatWindow = this.shadowRoot.querySelector('.chat-window');

    button.addEventListener('click', () => {
      chatWindow.classList.toggle('open');
    });
  }
}

customElements.define('chat-widget', ChatWidget);
```

**Uso en cualquier sitio:**

```html
<chat-widget></chat-widget>
```

---

## Cuándo NO usar Web Components con Angular ❌

### 1. **Componentes que dependen de Angular's ecosystem**

```typescript
// ❌ No uses Web Components para esto
@Component({
  selector: 'user-form',
  template: `
    <form [formGroup]="form">
      <input formControlName="name" />
      <input formControlName="email" />
      <button type="submit" [disabled]="form.invalid">Submit</button>
    </form>
  `
})
export class UserFormComponent {
  form = this.fb.group({
    name: ['', Validators.required],
    email: ['', [Validators.required, Validators.email]]
  });

  constructor(private fb: FormBuilder) {}
}
```

**Por qué no:** Perderías Reactive Forms, Validators, Change Detection, etc.

---

### 2. **Componentes con lógica de negocio compleja**

```typescript
// ❌ Web Components para lógica de negocio es mala idea
@Component({
  selector: 'user-dashboard',
  template: `
    <div *ngIf="userService.canAccess()">
      <div *ngFor="let item of items$ | async">{{ item.name }}</div>
    </div>
  `
})
export class UserDashboardComponent {
  items$ = this.http.get('/api/items');

  constructor(
    private http: HttpClient,
    private userService: UserService
  ) {}
}
```

**Por qué no:** Angular's DI, HttpClient, Routing, Guards no funcionan dentro de Web Components.

---

### 3. **Apps enteras hechas en Web Components**

```
❌ Anti-patrón: App entera en Web Components

my-app/
├── header.js
├── sidebar.js
├── main-content.js
├── footer.js
└── ...
```

**Problemas:**
- Sin routing integrado
- Sin state management unificado
- Sin cambio de detección automático
- Bundle size masivo (cada componente es un script separado)
- Difícil de mantener

---

## Cuándo SÍ usar Web Components con Angular ✅

### 1. **Componentes de UI compartidos entre equipos/frameworks**

```
company-ui-library/
├── button/
├── input/
├── card/
└── dropdown/

Angular Team usa: Angular versiones de componentes
React Team usa: React versiones de componentes
Vue Team usa: Vue versiones de componentes

✅ Todos usan: Web Components versiones de componentes
```

### 2. **Third-party widgets (embeds)**

```html
<!-- Analytics widget de terceros -->
<analytics-widget data-api-key="xxx"></analytics-widget>

<!-- Chat widget de soporte -->
<support-chat-widget position="bottom-right"></support-chat-widget>
```

### 3. **Legacy apps integradas**

Si tienes una app Angular nueva que necesita integrarse con una app legacy (jQuery, Vanilla JS):

```html
<!-- Angular app incluye Web Component de legacy -->
<div id="legacy-widget">
  <legacy-calendar data-date="2026-03-30"></legacy-calendar>
</div>
```

---

## Integrar Web Components en Angular 🔗

### Método 1: Como elementos custom estándar

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-home',
  standalone: true,
  template: `
    <my-button
      text="Click me"
      variant="primary"
      (click)="handleClick()">
    </my-button>

    <my-input
      [value]="username"
      (input)="username = $event.target.value">
    </my-input>
  `
})
export class HomeComponent {
  username = '';

  handleClick() {
    console.log('Button clicked!');
  }
}
```

### Método 2: Con Angular's `CUSTOM_ELEMENTS_SCHEMA`

```typescript
import { Component, CUSTOM_ELEMENTS_SCHEMA } from '@angular/core';

@Component({
  selector: 'app-home',
  standalone: true,
  schemas: [CUSTOM_ELEMENTS_SCHEMA], // Evita error: Unknown element
  template: `
    <custom-element [attr.prop]="value"></custom-element>
  `
})
export class HomeComponent {
  value = 'Hello';
}
```

### Método 3: Wrap Angular component en Web Component

```typescript
import { createCustomElement } from '@angular/elements';
import { MyAngularComponent } from './my-angular.component';

@NgModule({
  declarations: [MyAngularComponent],
  imports: [BrowserModule],
  entryComponents: [MyAngularComponent] // Necesario para custom elements
})
export class AppModule {
  constructor(private injector: Injector) {
    const myElement = createCustomElement(MyAngularComponent, {
      injector
    });
    customElements.define('my-angular-element', myElement);
  }
}
```

**Uso fuera de Angular:**

```html
<my-angular-element></my-angular-element>
```

---

## Comunicación: Angular ↔ Web Component 📡

### Desde Web Component hacia Angular (events)

```javascript
class MyButton extends HTMLElement {
  connectedCallback() {
    this.innerHTML = `
      <button>Click me</button>
    `;

    const button = this.querySelector('button');
    button.addEventListener('click', () => {
      this.dispatchEvent(new CustomEvent('clicked', {
        detail: { message: 'Button clicked!' }
      }));
    });
  }
}

customElements.define('my-button', MyButton);
```

```typescript
// En Angular
@Component({
  template: `
    <my-button (clicked)="handleClick($event)"></my-button>
  `
})
export class AppComponent {
  handleClick(event: any) {
    console.log(event.detail.message); // "Button clicked!"
  }
}
```

### Desde Angular hacia Web Component (properties/attributes)

```javascript
class MyInput extends HTMLElement {
  set value(newValue) {
    this.setAttribute('value', newValue);
    this.updateValue(newValue);
  }

  get value() {
    return this.getAttribute('value');
  }

  updateValue(value) {
    const input = this.querySelector('input');
    if (input) {
      input.value = value;
    }
  }

  connectedCallback() {
    this.innerHTML = `<input type="text" />`;
    this.updateValue(this.value);
  }
}

customElements.define('my-input', MyInput);
```

```typescript
// En Angular
@Component({
  template: `
    <my-input [value]="username"></my-input>
  `
})
export class AppComponent {
  username = 'John';
}
```

---

## Performance considerations ⚡

| Aspecto | Angular | Web Components |
|---------|---------|----------------|
| **Bundle size** | Framework (~100KB gzipped) | Solo tu código |
| **Change Detection** | Zone.js o signals | Manual, tú controlas |
| **Initial load** | Framework + tu código | Solo tu código |
| **Memory** | Framework overhead | Menor overhead |
| **Runtime perf** | Optimizado con zoneless | Depende de tu implementación |

**Regla de oro:**
- Para apps completas → Angular
- Para componentes compartidos multi-framework → Web Components

---

## Resumen de decisiones 🎯

| Caso | Solución |
|------|----------|
| App completa | Angular |
| Componente UI compartido Angular+React | Web Component |
| Widget embedable | Web Component |
| Lógica de negocio compleja | Angular |
| Legacy app integration | Web Component wrapper |
| Librería de componentes empresa | Web Components + framework wrappers |

**Key takeaway:** Web Components son para **componentes UI compartidos**, no para lógica de negocio o apps enteras. Angular sigue siendo el mejor para aplicaciones complejas. 🚀

#angular #web-components #typescript #architecture #frontend #javascript
