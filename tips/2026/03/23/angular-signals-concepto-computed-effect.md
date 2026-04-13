# Angular Signals: Concepto, `signal()`, `computed()` y `effect()` ⚡

Las **Signals** son el nuevo sistema de reactividad de Angular para gestionar estado de forma declarativa y eficiente. A diferencia de RxJS (flujos asíncronos), las signals son valores **sincrónicos** que notifican automáticamente a los consumidores cuando cambian.

---

## ¿Qué es una Signal? 🤔

Una signal es un **contenedor de valor** que rastrea automáticamente qué partes de tu app dependen de él.

```typescript
import { signal, computed, effect } from '@angular/core';

// Signal básica
const count = signal(0); // Valor inicial: 0

console.log(count()); // 0

count.set(1); // Actualizar
console.log(count()); // 1

count.update(n => n + 1); // Actualizar basado en el valor anterior
console.log(count()); // 2
```

---

## `signal()` - El origen del estado 🎯

Las signals writable pueden ser modificadas directamente:

```typescript
import { signal, Component } from '@angular/core';

@Component({
  selector: 'app-counter',
  standalone: true,
  template: `
    <h1>{{ count() }}</h1>
    <button (click)="increment()">+1</button>
    <button (click)="decrement()">-1</button>
    <button (click)="reset()">Reset</button>
  `
})
export class CounterComponent {
  count = signal(0);

  increment() {
    this.count.update(n => n + 1);
  }

  decrement() {
    this.count.update(n => n - 1);
  }

  reset() {
    this.count.set(0);
  }
}
```

** Métodos de una signal writable:**

| Método | Descripción |
|--------|-------------|
| `set(newValue)` | Reemplaza el valor |
| `update(fn)` | Transforma el valor actual |
| `mutate(fn)` | Modifica objetos/arrays en lugar de reemplazarlos |

### `mutate()` para objetos y arrays 📦

```typescript
const todos = signal<Todo[]>([
  { id: 1, text: 'Aprender signals', done: false }
]);

// ❌ No recomendado: crea nuevo array innecesariamente
todos.update(list => list.map(t => ({ ...t, done: true })));

// ✅ Mejor rendimiento con mutate()
todos.mutate(list => {
  list[0].done = true; // Modifica in-place
});
```

---

## `computed()` - Valores derivados sin duplicar lógica 🔄

Los computed signals son **solo lectura** y se recalculan automáticamente cuando cambian sus dependencias.

```typescript
import { signal, computed } from '@angular/core';

const count = signal(0);
const double = computed(() => count() * 2);
const quadruple = computed(() => double() * 2); // Chain de computed

console.log(count());     // 0
console.log(double());    // 0
console.log(quadruple()); // 0

count.set(5);

console.log(count());     // 5
console.log(double());    // 10 (recalculó automáticamente)
console.log(quadruple()); // 20 (recalculó solo cuando cambió double)
```

**Regla de oro:** Si puedes derivar un valor de otros signals, usa `computed()`. No duples lógica.

---

## Ejemplo práctico: Carrito de compras 🛒

```typescript
import { signal, computed } from '@angular/core';

interface Product {
  id: number;
  name: string;
  price: number;
}

@Component({
  selector: 'app-cart',
  standalone: true,
  template: `
    <div *ngFor="let item of cart()">
      <span>{{ item.name }} - ${{ item.price }}</span>
      <button (click)="remove(item.id)">Eliminar</button>
    </div>

    <h3>Total: ${{ total() }}</h3>
    <p>{{ count() }} {{ count() === 1 ? 'producto' : 'productos' }}</p>

    <button [disabled]="count() === 0" (click)="checkout()">
      Checkout
    </button>
  `
})
export class CartComponent {
  cart = signal<Product[]>([]);
  shipping = signal(5);

  // Computed: se recalcula cuando cart o shipping cambian
  total = computed(() => {
    const subtotal = this.cart().reduce((sum, item) => sum + item.price, 0);
    return this.cart().length > 0 ? subtotal + this.shipping() : 0;
  });

  // Computed: se recalcula solo cuando cart cambia
  count = computed(() => this.cart().length);

  add(product: Product) {
    this.cart.mutate(list => list.push(product));
  }

  remove(id: number) {
    this.cart.update(list => list.filter(item => item.id !== id));
  }

  checkout() {
    console.log('Checkout:', this.cart());
    this.cart.set([]); // Vacía el carrito
  }
}
```

---

## `effect()` - Side effects cuando cambian las dependencias 🌊

Los effects se ejecutan automáticamente cuando cambian las signals que dependen. Ideal para: logging, persistencia en localStorage, sincronizar con APIs, etc.

```typescript
import { signal, effect } from '@angular/core';

const theme = signal<'light' | 'dark'>('light');

// Efecto que se ejecuta cada vez que cambia theme
effect(() => {
  const currentTheme = theme();
  document.body.classList.toggle('dark', currentTheme === 'dark');
  localStorage.setItem('theme', currentTheme);
});
```

**Importante:** Los effects se ejecutan en el ciclo de detección de cambios. Úsalos solo para side effects.

---

## Ejemplo: Sincronizar con localStorage 💾

```typescript
import { signal, effect } from '@angular/core';

export class UserService {
  private readonly STORAGE_KEY = 'user_preferences';

  // Signal writable con valor inicial desde localStorage
  userPrefs = signal({
    darkMode: localStorage.getItem('darkMode') === 'true',
    language: localStorage.getItem('language') || 'en'
  });

  constructor() {
    // Efecto: guardar en localStorage cada vez que cambien las prefs
    effect(() => {
      const prefs = this.userPrefs();
      localStorage.setItem('darkMode', String(prefs.darkMode));
      localStorage.setItem('language', prefs.language);
    });
  }

  toggleDarkMode() {
    this.userPrefs.update(prefs => ({
      ...prefs,
      darkMode: !prefs.darkMode
    }));
  }

  setLanguage(lang: string) {
    this.userPrefs.update(prefs => ({
      ...prefs,
      language: lang
    }));
  }
}
```

---

## Signals vs RxJS: ¿Cuándo usar cada uno? 🤷

| Signals | RxJS |
|---------|------|
| Sincrónico | Asíncrono |
| Estado actual (valor) | Flujos de eventos |
| UI, forms, estado local | APIs, eventos, debounce/throttle |
| `signal()`, `computed()` | `Observable`, `BehaviorSubject` |
| No desuscribir | Cuidado con memory leaks |

**Regla simple:**
- Si necesitas el **valor actual** → Signals
- Si necesitas **flujos de eventos** → RxJS

---

## Resumen

| Concepto | Descripción | Uso |
|----------|-------------|-----|
| `signal()` | Contenedor de valor writable | Estado local, forms, contadores |
| `computed()` | Derivado, solo lectura | Totales, filtros, valores calculados |
| `effect()` | Side effects | localStorage, logging, sync con APIs |

Mañana veremos cómo usar signals en componentes, servicios y comunicación hijo→padre. 🚀

#angular #signals #reactivity #typescript #frontend
