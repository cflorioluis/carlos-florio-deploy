# 💡 Tip del Día: Ciclo de Vida Angular

---

## 🔄 El Ciclo de Vida de Componentes Modernos

Angular ha evolucionado mucho y con la llegada de las **Signals** y la renderización en el servidor (SSR) más robusta, el ciclo de vida de los componentes también se ha modernizado.

Aunque `ngOnInit` y `ngOnDestroy` siguen siendo fundamentales, hay nuevos jugadores en el equipo, especialmente pensados para el rendimiento y la integración con SSR.

---

## 📊 Diagrama del Ciclo de Vida

![Diagrama del Ciclo de Vida Angular](/tips/2025/12/10/angular-lifecycle-diagram.png)

---

## 🎯 Fases Principales del Ciclo de Vida

### 1. 🏗️ Creación (Constructor)

El constructor se ejecuta primero. Ahora preferimos usar `inject()` para las dependencias en lugar de los argumentos del constructor.

```typescript
import { Component, inject } from '@angular/core';
import { UserService } from './user.service';

@Component({
  selector: 'app-user-profile',
  template: `...`
})
export class UserProfileComponent {
  // ✅ Forma moderna con inject()
  private userService = inject(UserService);
  
  constructor() {
    // El constructor se mantiene limpio
    console.log('Constructor ejecutado');
  }
}
```

**¿Por qué `inject()`?**
- ✅ Más funcional y moderno
- ✅ Permite inyección fuera del constructor
- ✅ Mejor integración con Signals
- ✅ Código más limpio y legible

---

### 2. 🔄 ngOnChanges

Se ejecuta cuando cambian los `@Input()` del componente.

```typescript
import { Component, Input, OnChanges, SimpleChanges } from '@angular/core';

@Component({
  selector: 'app-user-card',
  template: `<div>{{ userName }}</div>`
})
export class UserCardComponent implements OnChanges {
  @Input() userName: string = '';
  
  ngOnChanges(changes: SimpleChanges): void {
    if (changes['userName']) {
      console.log('Usuario cambió:', changes['userName'].currentValue);
    }
  }
}
```

**Cuándo se ejecuta:**
- Antes de `ngOnInit`
- Cada vez que cambia un `@Input()`

---

### 3. 🚀 ngOnInit

Se inicializan los datos. Es el lugar seguro para lógica de inicio.

```typescript
import { Component, OnInit, inject } from '@angular/core';
import { UserService } from './user.service';

@Component({
  selector: 'app-dashboard',
  template: `...`
})
export class DashboardComponent implements OnInit {
  private userService = inject(UserService);
  users: User[] = [];
  
  ngOnInit(): void {
    // ✅ Lugar ideal para cargar datos
    this.loadUsers();
  }
  
  private loadUsers(): void {
    this.userService.getUsers().subscribe(users => {
      this.users = users;
    });
  }
}
```

**Cuándo usarlo:**
- Inicialización de datos
- Suscripciones a observables
- Configuración inicial del componente

---

### 4. 🔍 ngDoCheck

Se ejecuta en cada ciclo de detección de cambios.

```typescript
import { Component, DoCheck } from '@angular/core';

@Component({
  selector: 'app-performance',
  template: `...`
})
export class PerformanceComponent implements DoCheck {
  ngDoCheck(): void {
    // ⚠️ Cuidado: se ejecuta muy frecuentemente
    console.log('Detección de cambios ejecutada');
  }
}
```

**⚠️ Precaución:**
- Se ejecuta en **cada** ciclo de detección
- Puede afectar el rendimiento si no se usa correctamente
- Úsalo solo cuando realmente lo necesites

---

### 5. 📦 ngAfterContentInit & ngAfterContentChecked

Se ejecutan después de que el contenido proyectado (`<ng-content>`) ha sido inicializado.

```typescript
import { Component, AfterContentInit, ContentChild } from '@angular/core';

@Component({
  selector: 'app-card',
  template: `
    <div class="card">
      <ng-content></ng-content>
    </div>
  `
})
export class CardComponent implements AfterContentInit {
  @ContentChild('header') header: any;
  
  ngAfterContentInit(): void {
    // ✅ El contenido proyectado ya está disponible
    console.log('Contenido inicializado:', this.header);
  }
}
```

---

### 6. 👁️ ngAfterViewInit & ngAfterViewChecked

Se ejecutan después de que la vista del componente ha sido inicializada.

```typescript
import { Component, AfterViewInit, ViewChild, ElementRef } from '@angular/core';

@Component({
  selector: 'app-chart',
  template: `<canvas #chartCanvas></canvas>`
})
export class ChartComponent implements AfterViewInit {
  @ViewChild('chartCanvas') canvas!: ElementRef;
  
  ngAfterViewInit(): void {
    // ✅ El DOM ya está disponible
    this.initializeChart();
  }
  
  private initializeChart(): void {
    const ctx = this.canvas.nativeElement.getContext('2d');
    // Inicializar gráfico...
  }
}
```

---

### 7. 🎨 afterNextRender & afterRender (Angular 16.2+)

**¡NUEVO!** Estas son las funciones de **Render Callbacks** introducidas en **Angular 16.2** que reemplazan en muchos casos a `ngAfterViewInit` cuando necesitamos acceder al DOM de forma segura, especialmente en entornos con SSR.

#### afterNextRender - Ejecución Única

Se ejecuta **una sola vez** después de la siguiente renderización. Perfecto para inicializar librerías de terceros.

```typescript
import { Component, afterNextRender } from '@angular/core';

@Component({
  selector: 'app-map',
  template: `<div id="map-container"></div>`
})
export class MapComponent {
  constructor() {
    afterNextRender(() => {
      // ✅ Seguro para acceder al DOM
      // ✅ Se ejecuta solo en el navegador, no en el servidor
      this.initializeMap();
    });
  }
  
  private initializeMap(): void {
    const mapContainer = document.getElementById('map-container');
    // Inicializar mapa (Google Maps, Leaflet, etc.)
  }
}
```

#### afterRender - Ejecución Continua

Se ejecuta **después de cada renderización**. Úsalo con cuidado para no afectar el rendimiento.

```typescript
import { Component, afterRender, signal } from '@angular/core';

@Component({
  selector: 'app-animation',
  template: `<div>{{ count() }}</div>`
})
export class AnimationComponent {
  count = signal(0);
  
  constructor() {
    afterRender(() => {
      // ⚠️ Se ejecuta después de cada renderización
      console.log('Componente renderizado');
    });
  }
}
```

**Ventajas de los Render Callbacks:**
- ✅ Solo se ejecutan en el navegador (no en SSR)
- ✅ Más seguros para manipulación del DOM
- ✅ Mejor rendimiento que `ngAfterViewInit` en algunos casos
- ✅ Integración perfecta con Signals

---

### 8. 🧹 ngOnDestroy

Limpieza de recursos antes de que el componente sea destruido.

```typescript
import { Component, OnInit, OnDestroy, inject } from '@angular/core';
import { Subscription } from 'rxjs';
import { UserService } from './user.service';

@Component({
  selector: 'app-user-list',
  template: `...`
})
export class UserListComponent implements OnInit, OnDestroy {
  private userService = inject(UserService);
  private subscription?: Subscription;
  
  ngOnInit(): void {
    this.subscription = this.userService.getUsers().subscribe(users => {
      // Procesar usuarios...
    });
  }
  
  ngOnDestroy(): void {
    // ✅ Importante: limpiar suscripciones
    this.subscription?.unsubscribe();
  }
}
```

**Qué limpiar en `ngOnDestroy`:**
- Suscripciones a observables
- Timers (`setTimeout`, `setInterval`)
- Event listeners del DOM
- Conexiones WebSocket

---

## 📋 Tabla Comparativa de Hooks

| Hook | Cuándo se ejecuta | Uso común |\n|------|-------------------|----------|\n| `constructor` | Al crear la instancia | Inyección de dependencias |\n| `ngOnChanges` | Cuando cambian los `@Input()` | Reaccionar a cambios de inputs |\n| `ngOnInit` | Una vez, después del primer `ngOnChanges` | Inicialización de datos |\n| `ngDoCheck` | En cada detección de cambios | Detección manual de cambios |\n| `ngAfterContentInit` | Después de inicializar contenido proyectado | Acceder a `@ContentChild` |\n| `ngAfterViewInit` | Después de inicializar la vista | Acceder a `@ViewChild` |\n| `afterNextRender` | Una vez, después de renderizar (Angular 16.2+) | Inicializar librerías de terceros |\n| `afterRender` | Después de cada renderización (Angular 16.2+) | Sincronización con el DOM |\n| `ngOnDestroy` | Antes de destruir el componente | Limpieza de recursos |\n\n---

## 🎯 Mejores Prácticas

### 1. ✅ Usa `inject()` en lugar del constructor

```typescript
// ❌ Forma antigua
constructor(private userService: UserService) {}

// ✅ Forma moderna
private userService = inject(UserService);
```

### 2. ✅ Limpia siempre en `ngOnDestroy`

```typescript
ngOnDestroy(): void {
  this.subscription?.unsubscribe();
  clearInterval(this.timer);
}
```

### 3. ✅ Usa `afterNextRender` para DOM en SSR

```typescript
// ✅ Seguro para SSR
afterNextRender(() => {
  document.getElementById('map')?.focus();
});

// ❌ No seguro para SSR
ngAfterViewInit(): void {
  document.getElementById('map')?.focus(); // Error en SSR
}
```

### 4. ⚠️ Evita lógica pesada en `ngDoCheck`

```typescript
// ❌ Mal - se ejecuta demasiado
ngDoCheck(): void {
  this.heavyCalculation(); // Afecta rendimiento
}

// ✅ Bien - usa signals o observables
count = signal(0);
```

---

## 🔗 Recursos Adicionales

- [Angular Lifecycle Hooks - Documentación Oficial](https://angular.dev/guide/components/lifecycle)
- [afterRender y afterNextRender - Angular Blog](https://blog.angular.dev/angular-v16-is-here-4d7a28ec680d)
- [Signals en Angular](https://angular.dev/guide/signals)
- [Dependency Injection con inject()](https://angular.dev/guide/di/dependency-injection)

---

## 📝 Resumen

- ✅ El ciclo de vida de Angular tiene **9 hooks principales**
- ✅ **Angular 16.2** introdujo `afterNextRender` y `afterRender` para mejor integración con SSR
- ✅ Usa `inject()` para inyección de dependencias moderna
- ✅ Siempre limpia recursos en `ngOnDestroy`
- ✅ `afterNextRender` es ideal para inicializar librerías de terceros
- ⚠️ Ten cuidado con `ngDoCheck` y `afterRender` - pueden afectar el rendimiento

---

**¿Te gustó este tip?** ¡Adopta estos nuevos hooks para hacer tus aplicaciones más rápidas y compatibles con SSR! 🚀⚡
