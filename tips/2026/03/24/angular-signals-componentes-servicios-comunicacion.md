# Angular Signals: Componentes, Servicios y Comunicación Hijo→Padre 🔄

Hoy continuamos con Signals. Vamos a ver cómo usarlas en componentes, servicios, inyección de dependencias y patrones de comunicación hijo→padre sin la complejidad de RxJS.

---

## Signals en Componentes: `@Input` reactivos 📥

Desde Angular 19, puedes usar signals directamente en `@Input`:

```typescript
import { Component, input, signal } from '@angular/core';

interface User {
  id: number;
  name: string;
  email: string;
}

@Component({
  selector: 'app-user-card',
  standalone: true,
  template: `
    <div class="user-card">
      <h2>{{ user().name }}</h2>
      <p>{{ user().email }}</p>
      <p class="status" [class.active]="isActive()">
        {{ isActive() ? 'Active' : 'Inactive' }}
      </p>
    </div>
  `
})
export class UserCardComponent {
  // Input signal: reactiva y type-safe
  user = input.required<User>();

  // Computed derivado del input
  isActive = computed(() => this.user().id > 100);
}
```

### Inputs con valores por defecto

```typescript
@Component({
  selector: 'app-button',
  standalone: true,
  template: `
    <button [disabled]="disabled()" [class.primary]="variant() === 'primary'">
      {{ label() }}
    </button>
  `
})
export class ButtonComponent {
  label = input('Click me'); // Valor por defecto
  variant = input<'primary' | 'secondary'>('primary');
  disabled = input(false);
}
```

---

## `output()` - Eventos con signals simplificados 📤

Desde Angular 19, `output()` reemplaza `@Output()` con un API más simple:

```typescript
import { Component, input, output } from '@angular/core';

@Component({
  selector: 'app-todo-item',
  standalone: true,
  template: `
    <label>
      <input
        type="checkbox"
        [checked]="todo().done"
        (change)="toggle.emit()"
      />
      <span [class.done]="todo().done">{{ todo().text }}</span>
    </label>
    <button (click)="delete.emit(todo().id)">Delete</button>
  `
})
export class TodoItemComponent {
  todo = input.required<{ id: number; text: string; done: boolean }>();

  // Output events con signals
  toggle = output<void>();
  delete = output<number>();
}
```

### En el componente padre

```typescript
@Component({
  selector: 'app-todo-list',
  standalone: true,
  template: `
    <app-todo-item
      *ngFor="let item of todos()"
      [todo]="item"
      (toggle)="onToggle(item)"
      (delete)="onDelete($event)"
    />
  `
})
export class TodoListComponent {
  todos = signal<Todo[]>([
    { id: 1, text: 'Learn signals', done: false },
    { id: 2, text: 'Build awesome apps', done: false }
  ]);

  onToggle(item: Todo) {
    this.todos.mutate(list => {
      const found = list.find(t => t.id === item.id);
      if (found) found.done = !found.done;
    });
  }

  onDelete(id: number) {
    this.todos.update(list => list.filter(t => t.id !== id));
  }
}
```

---

## Signals en Servicios y DI 💉

Los services con signals son perfectos para estado compartido entre componentes:

```typescript
import { Injectable, signal, computed } from '@angular/core';

export interface Product {
  id: number;
  name: string;
  price: number;
}

@Injectable({ providedIn: 'root' })
export class CartService {
  private products = signal<Product[]>([]);

  // Signals públicas (readonly)
  cart = this.products.asReadonly();
  total = computed(() => this.products().reduce((sum, p) => sum + p.price, 0));
  count = computed(() => this.products().length);

  add(product: Product) {
    this.products.mutate(list => list.push(product));
  }

  remove(id: number) {
    this.products.update(list => list.filter(p => p.id !== id));
  }

  clear() {
    this.products.set([]);
  }
}
```

### Usar en componentes

```typescript
import { Component } from '@angular/core';
import { CartService } from './cart.service';

@Component({
  selector: 'app-cart-page',
  standalone: true,
  template: `
    <div *ngFor="let item of cartService.cart()">
      {{ item.name }} - ${{ item.price }}
    </div>

    <h3>Total: ${{ cartService.total() }}</h3>
    <p>Items: {{ cartService.count() }}</p>

    <button [disabled]="cartService.count() === 0" (click)="cartService.clear()">
      Clear cart
    </button>
  `
})
export class CartPageComponent {
  constructor(public cartService: CartService) {}
}
```

---

## `toSignal()` - Convertir Observables a Signals 🌉

Bridja el mundo RxJS con Signals:

```typescript
import { Component, inject } from '@angular/core';
import { toSignal } from '@angular/core/rxjs-interop';
import { HttpClient } from '@angular/common/http';
import { interval, map } from 'rxjs';

interface User {
  id: number;
  name: string;
}

@Component({
  selector: 'app-users',
  standalone: true,
  template: `
    <h1>Users loaded: {{ users()?.length || 0 }}</h1>
    <div *ngFor="let user of users() || []">
      {{ user.name }}
    </div>

    <p>Time: {{ timer() }}s</p>
  `
})
export class UsersComponent {
  private http = inject(HttpClient);

  // Observable → Signal (async state)
  users = toSignal(
    this.http.get<User[]>('https://api.example.com/users'),
    { initialValue: [] }
  );

  // RxJS → Signal con transformación
  timer = toSignal(
    interval(1000).pipe(map(i => i + 1)),
    { initialValue: 0 }
  );
}
```

### Con `AsyncPipe` vs Signals

```typescript
// ❌ AsyncPipe (RxJS)
@Component({
  template: `
    <div *ngIf="users$ | async as users">
      {{ users.length }}
    </div>
  `
})
export class OldComponent {
  users$ = this.http.get<User[]>('/users');
}

// ✅ Signals (Angular moderno)
@Component({
  template: `
    <div>{{ users()?.length }}</div>
  `
})
export class NewComponent {
  users = toSignal(this.http.get<User[]>('/users'), { initialValue: null });
}
```

---

## Comunicación Hijo→Padre con Signals 🎯

### Padre con signal que el hijo modifica

```typescript
// Padre
@Component({
  selector: 'app-parent',
  standalone: true,
  template: `
    <h1>Counter: {{ count() }}</h1>
    <app-child [counter]="count" (increment)="count.set($event)" />
  `
})
export class ParentComponent {
  count = signal(0);
}

// Hijo
@Component({
  selector: 'app-child',
  standalone: true,
  template: `
    <button (click)="increment.emit(count() + 1)">Increment</button>
  `
})
export class ChildComponent {
  counter = input.required<number>();
  increment = output<number>();
}
```

---

## Signals Zoneless: Detección de cambios sin Zone.js 🚀

Con signals y Zoneless, Angular puede eliminar Zone.js para un rendimiento aún mejor:

```typescript
// main.ts (Angular 21+)
import { bootstrapApplication } from '@angular/platform-browser';
import { provideZonelessChangeDetection } from '@angular/core';

bootstrapApplication(AppComponent, {
  providers: [provideZonelessChangeDetection()]
});
```

**Cambio en componentes:**

```typescript
// Con Zone.js
@Component({
  template: `{{ count() }}`
})
export class OldComponent {
  count = signal(0);

  increment() {
    this.count.update(n => n + 1);
    // Zone.js detecta el cambio automáticamente
  }
}

// Zoneless (Angular 21+)
@Component({
  template: `{{ count() }}`
})
export class NewComponent {
  count = signal(0);

  increment() {
    // Signals notifican a Angular automáticamente
    this.count.update(n => n + 1);
    // Sin Zone.js: más rápido, menos overhead
  }
}
```

---

## Resumen de Patrones

| Caso de uso | Solución |
|-------------|----------|
| Input reactivos | `input()` / `input.required()` |
| Output events | `output()` |
| Estado compartido | Signals en `@Injectable()` |
| Observable → Signal | `toSignal()` |
| Zoneless | `provideZonelessChangeDetection()` |
| Proteger signals privadas | `.asReadonly()` |

---

## Checklist: ¿Tu app está lista para Signals? ✅

- ✅ Migrar `@Input()` → `input()`
- ✅ Migrar `@Output()` → `output()`
- ✅ Usar `computed()` en lugar de propiedades getters
- ✅ Usar `toSignal()` en lugar de `| async`
- ✅ Usar signals en servicios para estado compartido
- ✅ Habilitar Zoneless con `provideZonelessChangeDetection()`

Con signals, tu Angular es más reactivo, más rápido y más simple. 🎯

#angular #signals #typescript #zoneless #reactivity #frontend
