# Angular 21: Signal Forms - El Futuro de los Formularios 📝

Angular 21 introduce **Signal Forms** en modo experimental: una forma completamente nueva de manejar formularios usando el sistema reactivo de Signals. ¡Prepárate para formularios más type-safe y reactivos!

## ¿Qué son Signal Forms? 🤔

Una reimaginación completa de los formularios en Angular, aprovechando el poder de **Signals** para crear una API más moderna, reactiva y con mejor inferencia de tipos.

## Reactive Forms Tradicionales 👴

```typescript
import { FormControl, FormGroup } from '@angular/forms';

export class UserFormComponent {
  userForm = new FormGroup({
    name: new FormControl(''),
    email: new FormControl(''),
    age: new FormControl(0)
  });

  onSubmit() {
    const value = this.userForm.value;
    // value es Partial<{name: string | null, email: string | null, ...}>
    console.log(value.name); // Puede ser undefined
  }
}
```

**Problemas**:
- ❌ Tipos parciales y nullables por defecto
- ❌ No hay reactividad automática con signals
- ❌ Verboso para casos simples

## Signal Forms: La Nueva Era ✨

```typescript
import { signalForm, signalControl } from '@angular/forms';

export class UserFormComponent {
  userForm = signalForm({
    name: signalControl(''),
    email: signalControl(''),
    age: signalControl(0)
  });

  // Acceso reactivo con signals
  nameValue = this.userForm.controls.name.value;

  onSubmit() {
    const value = this.userForm.value();
    // value es { name: string, email: string, age: number }
    console.log(value.name); // ✅ Type-safe, nunca undefined
  }
}
```

**Ventajas**:
- ✅ Type-safety mejorado
- ✅ Reactividad nativa con signals
- ✅ API más limpia y moderna
- ✅ Mejor integración con zoneless

## Validaciones con Signals 🛡️

```typescript
import { signalForm, signalControl, Validators } from '@angular/forms';
import { computed } from '@angular/core';

export class LoginComponent {
  loginForm = signalForm({
    email: signalControl('', [Validators.required, Validators.email]),
    password: signalControl('', [Validators.required, Validators.minLength(8)])
  });

  // Computed signal para validación
  isFormValid = computed(() => this.loginForm.valid());

  // Errores reactivos
  emailErrors = computed(() => this.loginForm.controls.email.errors());
}
```

## En el Template 🎨

```html
<form [formGroup]="loginForm" (ngSubmit)="onSubmit()">
  <input 
    type="email" 
    [formControl]="loginForm.controls.email"
    placeholder="Email"
  />
  
  @if (emailErrors()?.['required']) {
    <span class="error">Email es requerido</span>
  }
  @if (emailErrors()?.['email']) {
    <span class="error">Email inválido</span>
  }

  <input 
    type="password" 
    [formControl]="loginForm.controls.password"
    placeholder="Contraseña"
  />

  <button 
    type="submit" 
    [disabled]="!isFormValid()"
  >
    Iniciar Sesión
  </button>
</form>
```

## Reactividad Automática 🔄

```typescript
export class SearchComponent {
  searchForm = signalForm({
    query: signalControl(''),
    category: signalControl('all')
  });

  // Effect se ejecuta automáticamente cuando cambian los valores
  constructor() {
    effect(() => {
      const query = this.searchForm.controls.query.value();
      const category = this.searchForm.controls.category.value();
      
      if (query.length > 2) {
        this.performSearch(query, category);
      }
    });
  }
}
```

## Migración Gradual 🚶

Signal Forms es **experimental** en Angular 21. Puedes:

1. **Mantener Reactive Forms** en código existente
2. **Usar Signal Forms** en componentes nuevos
3. **Migrar gradualmente** cuando la API sea estable

## ¿Cuándo Usar Signal Forms? 🤷

- ✅ Proyectos nuevos con Angular 21+
- ✅ Componentes que usan zoneless
- ✅ Formularios con lógica reactiva compleja
- ⚠️ Espera a que salga de experimental para producción crítica

¡El futuro de los formularios en Angular es reactivo con Signals! 🎉
