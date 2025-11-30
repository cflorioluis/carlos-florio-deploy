# 💡 Tip del Día: ChangeDetectionStrategy.OnPush en Angular

---

## 🎯 ¿Qué es OnPush?

`ChangeDetectionStrategy.OnPush` es una estrategia de detección de cambios en Angular que optimiza drásticamente el rendimiento de tu aplicación al reducir la cantidad de veces que Angular verifica si un componente necesita actualizarse.

Por defecto, Angular usa la estrategia `Default`, que verifica **todos** los componentes en cada ciclo de detección de cambios. Con `OnPush`, Angular solo verifica el componente cuando:
- Cambian sus `@Input()` (por referencia)
- Se dispara un evento en el componente
- Se ejecuta un `Observable` con el pipe `async`
- Se llama manualmente a `ChangeDetectorRef.markForCheck()`

---

## 🚀 Ventajas de OnPush

### 1. ⚡ Mejor Rendimiento

La ventaja más importante: **menos ciclos de detección de cambios = aplicación más rápida**.

```typescript
// ❌ Sin OnPush: Angular verifica este componente en CADA cambio
@Component({
  selector: 'app-user-card',
  template: `<div>{{ user.name }}</div>`
})
export class UserCardComponent {
  @Input() user!: User;
}

// ✅ Con OnPush: Angular solo verifica cuando cambia la referencia de user
@Component({
  selector: 'app-user-card',
  template: `<div>{{ user.name }}</div>`,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class UserCardComponent {
  @Input() user!: User;
}
```

### 2. 🔒 Código Más Predecible

OnPush te obliga a escribir código más funcional e inmutable, lo que hace que tu aplicación sea más predecible y fácil de debuggear.

### 3. 📊 Escalabilidad

En aplicaciones grandes con cientos de componentes, OnPush puede reducir significativamente el tiempo de renderizado.

### 4. 🎨 Mejor Integración con Signals

OnPush funciona perfectamente con Signals de Angular, ya que ambos están diseñados para optimizar la detección de cambios.

---

## 🆚 Default vs OnPush

### Estrategia Default

```typescript
@Component({
  selector: 'app-counter',
  template: `
    <div>
      <p>Count: {{ count }}</p>
      <button (click)="increment()">+</button>
    </div>
  `
  // changeDetection: ChangeDetectionStrategy.Default (por defecto)
})
export class CounterComponent {
  count = 0;
  
  increment(): void {
    this.count++;
    // ✅ La vista se actualiza automáticamente
  }
}
```

**Comportamiento:**
- Angular verifica el componente en **cada** ciclo de detección
- Funciona con mutaciones directas (`this.count++`)
- Más fácil de usar pero menos eficiente

### Estrategia OnPush

```typescript
@Component({
  selector: 'app-counter',
  template: `
    <div>
      <p>Count: {{ count }}</p>
      <button (click)="increment()">+</button>
    </div>
  `,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class CounterComponent {
  count = 0;
  
  increment(): void {
    this.count++;
    // ✅ La vista se actualiza porque el evento (click) dispara la detección
  }
}
```

**Comportamiento:**
- Angular solo verifica cuando hay un trigger específico
- Más eficiente pero requiere entender cuándo se dispara
- Ideal para componentes con muchos datos

---

## 📋 Cuándo se Actualiza OnPush

### 1. 🔄 Cambio en @Input() (por referencia)

```typescript
@Component({
  selector: 'app-user-list',
  template: `
    <app-user-card 
      *ngFor="let user of users" 
      [user]="user">
    </app-user-card>
  `,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class UserListComponent {
  @Input() users: User[] = [];
  
  // ❌ NO dispara OnPush (misma referencia)
  addUserWrong(user: User): void {
    this.users.push(user);
  }
  
  // ✅ SÍ dispara OnPush (nueva referencia)
  addUserCorrect(user: User): void {
    this.users = [...this.users, user];
  }
}
```

### 2. 🖱️ Eventos del Template

```typescript
@Component({
  selector: 'app-clicker',
  template: `
    <button (click)="handleClick()">Click me</button>
    <p>Clicks: {{ clicks }}</p>
  `,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class ClickerComponent {
  clicks = 0;
  
  handleClick(): void {
    this.clicks++;
    // ✅ Se actualiza porque el evento (click) dispara la detección
  }
}
```

### 3. 📡 Observables con Pipe Async

```typescript
@Component({
  selector: 'app-data-viewer',
  template: `
    <div *ngIf="data$ | async as data">
      {{ data.name }}
    </div>
  `,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class DataViewerComponent {
  data$ = this.dataService.getData();
  
  constructor(private dataService: DataService) {}
  // ✅ El pipe async marca automáticamente para detección
}
```

### 4. 🔧 Manual con ChangeDetectorRef

```typescript
import { ChangeDetectorRef, ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'app-timer',
  template: `<p>Time: {{ time }}</p>`,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class TimerComponent implements OnInit, OnDestroy {
  time = new Date();
  private interval?: number;
  
  constructor(private cdr: ChangeDetectorRef) {}
  
  ngOnInit(): void {
    this.interval = window.setInterval(() => {
      this.time = new Date();
      // ✅ Marcamos manualmente para detección
      this.cdr.markForCheck();
    }, 1000);
  }
  
  ngOnDestroy(): void {
    if (this.interval) {
      clearInterval(this.interval);
    }
  }
}
```

---

## 🎨 OnPush con Signals (Angular 16+)

Signals y OnPush son la combinación perfecta:

```typescript
import { Component, signal, ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'app-counter-signal',
  template: `
    <div>
      <p>Count: {{ count() }}</p>
      <button (click)="increment()">+</button>
    </div>
  `,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class CounterSignalComponent {
  count = signal(0);
  
  increment(): void {
    this.count.update(c => c + 1);
    // ✅ Signals marcan automáticamente para detección
  }
}
```

**Ventajas:**
- No necesitas `ChangeDetectorRef`
- Más reactivo y declarativo
- Mejor rendimiento automático

---

## ⚠️ Errores Comunes con OnPush

### Error 1: Mutar Objetos Directamente

```typescript
@Component({
  selector: 'app-todo-list',
  template: `
    <div *ngFor="let todo of todos">
      {{ todo.title }} - {{ todo.completed ? '✅' : '⏳' }}
    </div>
  `,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class TodoListComponent {
  @Input() todos: Todo[] = [];
  
  // ❌ MAL: Muta el array directamente
  toggleTodoWrong(index: number): void {
    this.todos[index].completed = !this.todos[index].completed;
    // La vista NO se actualiza
  }
  
  // ✅ BIEN: Crea nueva referencia
  toggleTodoCorrect(index: number): void {
    this.todos = this.todos.map((todo, i) => 
      i === index 
        ? { ...todo, completed: !todo.completed }
        : todo
    );
    // La vista SÍ se actualiza
  }
}
```

### Error 2: Olvidar markForCheck con Timers

```typescript
@Component({
  selector: 'app-countdown',
  template: `<p>{{ seconds }} seconds</p>`,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class CountdownComponent implements OnInit {
  seconds = 10;
  
  constructor(private cdr: ChangeDetectorRef) {}
  
  ngOnInit(): void {
    // ❌ MAL: La vista no se actualiza
    setInterval(() => {
      this.seconds--;
    }, 1000);
    
    // ✅ BIEN: Marcamos para detección
    setInterval(() => {
      this.seconds--;
      this.cdr.markForCheck();
    }, 1000);
  }
}
```

---

## 📊 Comparación de Rendimiento

| Escenario | Default | OnPush | Mejora |\n|-----------|---------|--------|--------|\n| **100 componentes** | ~50ms | ~5ms | 90% más rápido |\n| **1000 componentes** | ~500ms | ~20ms | 96% más rápido |\n| **Componente con datos complejos** | Verifica siempre | Verifica solo cuando cambia | Hasta 10x más rápido |\n\n*Nota: Los tiempos son aproximados y dependen de la complejidad de los componentes*

---

## 🎯 Mejores Prácticas

### 1. ✅ Usa OnPush por Defecto

```typescript
// ✅ Buena práctica: OnPush por defecto en componentes nuevos
@Component({
  selector: 'app-my-component',
  template: `...`,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class MyComponent {}
```

### 2. ✅ Trabaja con Inmutabilidad

```typescript
// ✅ Usa operadores inmutables
addItem(item: Item): void {
  this.items = [...this.items, item];
}

updateItem(id: string, changes: Partial<Item>): void {
  this.items = this.items.map(item => 
    item.id === id ? { ...item, ...changes } : item
  );
}

removeItem(id: string): void {
  this.items = this.items.filter(item => item.id !== id);
}
```

### 3. ✅ Usa Signals cuando sea posible

```typescript
// ✅ Signals + OnPush = Combinación perfecta
count = signal(0);
users = signal<User[]>([]);

addUser(user: User): void {
  this.users.update(users => [...users, user]);
}
```

### 4. ✅ Usa el Pipe Async para Observables

```typescript
// ✅ El pipe async maneja la detección automáticamente
@Component({
  template: `
    <div *ngIf="user$ | async as user">
      {{ user.name }}
    </div>
  `,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class UserComponent {
  user$ = this.userService.getCurrentUser();
}
```

---

## 🔄 Migración a OnPush

### Paso 1: Identifica Componentes Candidatos

Componentes ideales para OnPush:
- ✅ Componentes de presentación (dumb components)
- ✅ Componentes con muchos `@Input()`
- ✅ Componentes que se renderizan frecuentemente
- ✅ Listas con muchos items

### Paso 2: Agrega OnPush

```typescript
@Component({
  selector: 'app-user-card',
  template: `...`,
  changeDetection: ChangeDetectionStrategy.OnPush // Agregar esta línea
})
```

### Paso 3: Refactoriza Mutaciones

```typescript
// Antes
this.users.push(newUser);

// Después
this.users = [...this.users, newUser];
```

### Paso 4: Prueba y Verifica

- Verifica que la UI se actualiza correctamente
- Usa Angular DevTools para monitorear la detección de cambios
- Mide el rendimiento antes y después

---

## 🛠️ Herramientas de Debugging

### Angular DevTools

```bash
# Instala la extensión de Chrome/Firefox
# Luego en DevTools > Angular > Profiler
# Puedes ver cuántas veces se detectan cambios en cada componente
```

### Console Logs

```typescript
@Component({
  selector: 'app-debug',
  template: `...`,
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class DebugComponent implements DoCheck {
  ngDoCheck(): void {
    console.log('Change detection executed');
  }
}
```

---

## 📝 Resumen

- ✅ **OnPush** reduce drásticamente los ciclos de detección de cambios
- ✅ Solo se actualiza cuando: cambian `@Input()`, eventos, `async` pipe, o `markForCheck()`
- ✅ Requiere **inmutabilidad**: crear nuevas referencias en lugar de mutar
- ✅ Funciona perfectamente con **Signals** (Angular 16+)
- ✅ Puede mejorar el rendimiento hasta **10x** en aplicaciones grandes
- ⚠️ Requiere entender cuándo se dispara la detección
- ⚠️ Evita mutaciones directas de objetos y arrays

---

## 🔗 Recursos Adicionales

- [Angular Change Detection - Documentación Oficial](https://angular.dev/guide/change-detection)
- [OnPush Change Detection Strategy](https://angular.dev/api/core/ChangeDetectionStrategy)
- [Angular Signals](https://angular.dev/guide/signals)
- [Angular Performance Best Practices](https://angular.dev/best-practices/runtime-performance)

---

**¿Te gustó este tip?** ¡Empieza a usar OnPush en tus componentes y observa cómo mejora el rendimiento de tu aplicación! 🚀⚡
