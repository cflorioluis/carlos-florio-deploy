# Angular 21: Standalone Components por Defecto 🎯

¡Es oficial! Angular 21 hace de los **Standalone Components** el estándar. Adiós a la complejidad de NgModules. Bienvenido a un Angular más simple y modular.

## ¿Qué son Standalone Components? 🤔

Componentes que **no necesitan NgModule** para funcionar. Se declaran, importan y exportan de forma independiente.

## El Pasado: NgModules 👴

```typescript
// ❌ El viejo mundo de NgModules
// user.component.ts
@Component({
  selector: 'app-user',
  templateUrl: './user.component.html'
})
export class UserComponent {}

// user.module.ts
@NgModule({
  declarations: [UserComponent],
  imports: [CommonModule, FormsModule],
  exports: [UserComponent]
})
export class UserModule {}

// app.module.ts
@NgModule({
  declarations: [AppComponent],
  imports: [
    BrowserModule,
    UserModule,
    // ... más módulos
  ],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

**Problemas**:
- ❌ Boilerplate excesivo
- ❌ Difícil de entender para principiantes
- ❌ Imports circulares
- ❌ Módulos compartidos complejos

## El Presente: Standalone 🚀

```typescript
// ✅ Angular 21 - Standalone por defecto
// user.component.ts
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';

@Component({
  selector: 'app-user',
  standalone: true, // ¡Esto es todo lo que necesitas!
  imports: [CommonModule, FormsModule],
  templateUrl: './user.component.html'
})
export class UserComponent {}
```

## Nuevo Proyecto en Angular 21 🆕

```bash
ng new my-app
# Ya NO genera app.module.ts
# Todo es standalone por defecto ✨
```

### main.ts Simplificado

```typescript
// main.ts - Angular 21
import { bootstrapApplication } from '@angular/platform-browser';
import { AppComponent } from './app/app.component';
import { provideRouter } from '@angular/router';
import { routes } from './app/app.routes';

bootstrapApplication(AppComponent, {
  providers: [
    provideRouter(routes),
    provideHttpClient(),
    // Más providers...
  ]
});
```

## Importar Otros Componentes 🔗

```typescript
// dashboard.component.ts
import { Component } from '@angular/core';
import { UserCardComponent } from './user-card/user-card.component';
import { StatsWidgetComponent } from './stats-widget/stats-widget.component';

@Component({
  selector: 'app-dashboard',
  standalone: true,
  imports: [
    UserCardComponent,    // Importa directamente
    StatsWidgetComponent  // Sin necesidad de módulos
  ],
  template: `
    <div class="dashboard">
      <app-user-card />
      <app-stats-widget />
    </div>
  `
})
export class DashboardComponent {}
```

## Routing con Standalone 🛣️

```typescript
// app.routes.ts
import { Routes } from '@angular/router';

export const routes: Routes = [
  {
    path: '',
    loadComponent: () => import('./home/home.component')
      .then(m => m.HomeComponent)
  },
  {
    path: 'users',
    loadComponent: () => import('./users/users.component')
      .then(m => m.UsersComponent)
  },
  {
    path: 'settings',
    loadChildren: () => import('./settings/settings.routes')
      .then(m => m.SETTINGS_ROUTES)
  }
];
```

### Lazy Loading Simplificado

```typescript
// settings/settings.routes.ts
import { Routes } from '@angular/router';
import { SettingsComponent } from './settings.component';
import { ProfileComponent } from './profile/profile.component';
import { SecurityComponent } from './security/security.component';

export const SETTINGS_ROUTES: Routes = [
  {
    path: '',
    component: SettingsComponent,
    children: [
      { path: 'profile', component: ProfileComponent },
      { path: 'security', component: SecurityComponent }
    ]
  }
];
```

## Providers a Nivel de Componente 💉

```typescript
import { Component } from '@angular/core';
import { UserService } from './user.service';

@Component({
  selector: 'app-user-list',
  standalone: true,
  providers: [UserService], // Scoped al componente
  template: `...`
})
export class UserListComponent {
  constructor(private userService: UserService) {}
}
```

## Migrar desde NgModules 🔄

Angular CLI te ayuda:

```bash
# Convierte un componente a standalone
ng generate @angular/core:standalone

# Convierte todo el proyecto
ng generate @angular/core:standalone --mode=convert-to-standalone
```

### Migración Manual

```typescript
// Antes
@Component({
  selector: 'app-button',
  templateUrl: './button.component.html'
})
export class ButtonComponent {}

// Después
@Component({
  selector: 'app-button',
  standalone: true,        // 1. Añade standalone
  imports: [CommonModule], // 2. Importa lo que necesites
  templateUrl: './button.component.html'
})
export class ButtonComponent {}
```

## Compartir Lógica sin Módulos 📦

### Antes: SharedModule

```typescript
// ❌ shared.module.ts
@NgModule({
  imports: [CommonModule, FormsModule],
  declarations: [ButtonComponent, CardComponent],
  exports: [CommonModule, FormsModule, ButtonComponent, CardComponent]
})
export class SharedModule {}
```

### Ahora: Barrel Exports

```typescript
// ✅ shared/index.ts
export { ButtonComponent } from './button/button.component';
export { CardComponent } from './card/card.component';
export { InputComponent } from './input/input.component';

// Usar en otros componentes
import { ButtonComponent, CardComponent } from '@/shared';
```

## Ventajas de Standalone 🎉

1. **📦 Menos Boilerplate**: No más NgModules
2. **🧩 Mejor Tree-Shaking**: Solo importas lo que usas
3. **🚀 Lazy Loading Más Fácil**: loadComponent en lugar de loadChildren
4. **🎯 Imports Explícitos**: Sabes exactamente qué usa cada componente
5. **🔧 Mejor DX**: Menos archivos, menos complejidad

## ¿Cuándo Usar NgModules? 🤷

En Angular 21, **casi nunca**. Solo si:
- Mantienes una librería legacy
- Necesitas compatibilidad con Angular < 14

## Tip Pro: Organización de Archivos 📁

```
src/app/
├── features/
│   ├── users/
│   │   ├── users.component.ts
│   │   ├── users.routes.ts
│   │   └── components/
│   │       ├── user-card.component.ts
│   │       └── user-form.component.ts
│   └── products/
│       ├── products.component.ts
│       └── products.routes.ts
├── shared/
│   ├── components/
│   ├── directives/
│   └── pipes/
├── app.component.ts
├── app.routes.ts
└── main.ts
```

¡Bienvenido a un Angular más simple y modular! 🎯✨
