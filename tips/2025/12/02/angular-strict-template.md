# 🔒 Angular Strict Template: Simplifica tus Templates con Type Safety

**Angular Strict Template** es una característica que habilita la verificación estricta de tipos en tus templates HTML, permitiéndote usar **strings literales** directamente en atributos sin necesidad de property binding cuando los tipos están definidos como **string unions** en TypeScript.

---

## 🎯 ¿Qué es Strict Template?

Strict Template es una opción de configuración en Angular que habilita la verificación de tipos más estricta en los templates. Cuando está activado, Angular puede inferir tipos de strings literales en atributos HTML, eliminando la necesidad de usar property binding `[property]="ENUM.value"` para valores constantes.

---

## ⚙️ Habilitación en `tsconfig.json`

Para habilitar strict template en tu proyecto Angular, actualiza tu `tsconfig.json`:

```json
{
  "angularCompilerOptions": {
    "strictTemplates": true,
    "strictInputTypes": true,
    "strictAttributeTypes": true,
    "strictNullInputTypes": true,
    "strictOutputEventTypes": true
  }
}
```

**Opciones disponibles:**

| Opción | Descripción |
|--------|-------------|
| `strictTemplates` | Habilita todas las opciones strict de una vez |
| `strictInputTypes` | Verifica tipos de inputs en componentes |
| `strictAttributeTypes` | Verifica tipos de atributos HTML |
| `strictNullInputTypes` | No permite `null` o `undefined` en inputs no opcionales |
| `strictOutputEventTypes` | Verifica tipos de eventos emitidos |

---

## 💡 El Problema: Property Binding Innecesario

**Antes** (sin strict template o sin string unions):

Tenías que usar property binding con enums o constantes para pasar valores simples:

```html
<ui-button
  [size]="ENUM.medium"
  [theme]="verPrimary"
  variant="outlined"
  icon="user"
  [disabled]="isLoading$ | async"
  (click)="handleSubmit()">
  {{ 'common.save' | translate }}
</ui-button>
```

```typescript
// En el componente
export class MyComponent {
  ENUM = {
    medium: 'medium',
    large: 'large',
    small: 'small'
  };
  
  verPrimary = 'primary';
}
```

**Problemas de este enfoque:**

- ❌ Verboso y difícil de leer
- ❌ Requiere definir constantes en el componente
- ❌ Más código innecesario
- ❌ Menos intuitivo para valores simples

---

## ✅ La Solución: String Unions + Strict Template

**Después** (con strict template y string unions):

Puedes usar strings literales directamente:

```html
<ui-button
  size="medium"
  theme="primary"
  variant="outlined"
  icon="user"
  [disabled]="isLoading$ | async"
  (click)="handleSubmit()">
  {{ 'common.save' | translate }}
</ui-button>
```

**¿Cómo funciona?**

Angular entiende que `"medium"` y `"primary"` son **strings literales** porque en el componente `ui-button` los tipos están definidos como **string unions**:

```typescript
// ui-button.component.ts
import { Component, Input } from '@angular/core';

export type ButtonSize = 'small' | 'medium' | 'large';
export type ButtonTheme = 'primary' | 'secondary' | 'danger' | 'success';
export type ButtonVariant = 'filled' | 'outlined' | 'text';

@Component({
  selector: 'ui-button',
  templateUrl: './ui-button.component.html',
  styleUrls: ['./ui-button.component.css']
})
export class UiButtonComponent {
  @Input() size: ButtonSize = 'medium';
  @Input() theme: ButtonTheme = 'primary';
  @Input() variant: ButtonVariant = 'filled';
  @Input() icon?: string;
  @Input() disabled = false;
}
```

**Ventajas:**

- ✅ **Más limpio y legible**: No necesitas `[property]="ENUM.value"`
- ✅ **Type-safe**: Angular verifica que `"medium"` es un valor válido de `ButtonSize`
- ✅ **Autocompletado**: Tu IDE te sugerirá los valores válidos
- ✅ **Errores en tiempo de compilación**: Si escribes `size="mediumm"` (typo), Angular te alertará

---

## 🔍 Comparación Detallada

### Ejemplo 1: Botón con Múltiples Propiedades

**❌ Sin Strict Template (Antiguo):**

```html
<!-- Template -->
<ui-button
  [size]="buttonSize.medium"
  [theme]="buttonTheme.primary"
  [variant]="buttonVariant.outlined"
  [disabled]="isLoading$ | async">
  Guardar
</ui-button>
```

```typescript
// Componente
export class FormComponent {
  buttonSize = {
    small: 'small',
    medium: 'medium',
    large: 'large'
  };
  
  buttonTheme = {
    primary: 'primary',
    secondary: 'secondary'
  };
  
  buttonVariant = {
    filled: 'filled',
    outlined: 'outlined',
    text: 'text'
  };
  
  isLoading$ = this.store.select(selectIsLoading);
}
```

**✅ Con Strict Template (Moderno):**

```html
<!-- Template -->
<ui-button
  size="medium"
  theme="primary"
  variant="outlined"
  [disabled]="isLoading$ | async">
  Guardar
</ui-button>
```

```typescript
// Componente
export class FormComponent {
  isLoading$ = this.store.select(selectIsLoading);
  // ¡No necesitas definir constantes para valores simples!
}
```

### Ejemplo 2: Card con Diferentes Estilos

**❌ Sin Strict Template:**

```html
<ui-card
  [elevation]="cardElevation.high"
  [padding]="cardPadding.large"
  [borderRadius]="cardRadius.medium">
  <h2>Título</h2>
  <p>Contenido</p>
</ui-card>
```

**✅ Con Strict Template:**

```html
<ui-card
  elevation="high"
  padding="large"
  borderRadius="medium">
  <h2>Título</h2>
  <p>Contenido</p>
</ui-card>
```

---

## 🚨 Cuándo Usar Property Binding vs String Literal

### Usa **String Literal** (sin corchetes) cuando:

✅ El valor es **constante** y conocido en tiempo de diseño:

```html
<ui-button size="medium" theme="primary">Click</ui-button>
<ui-icon name="user" size="large"></ui-icon>
<ui-badge color="success" position="top-right"></ui-badge>
```

### Usa **Property Binding** (con corchetes) cuando:

✅ El valor es **dinámico** o viene de una variable/propiedad:

```html
<ui-button [size]="currentSize" [theme]="userTheme">Click</ui-button>
<ui-icon [name]="iconName" [size]="iconSize"></ui-icon>
<ui-badge [color]="status" [position]="badgePosition"></ui-badge>
```

✅ El valor es un **boolean**, **number**, **objeto** o **array**:

```html
<ui-button [disabled]="isLoading">Click</ui-button>
<ui-input [maxLength]="100" [value]="userName"></ui-input>
<ui-table [data]="users" [columns]="tableColumns"></ui-table>
```

---

## 📐 Guía Completa de Sintaxis de Binding en Angular

Angular ofrece diferentes sintaxis para pasar datos a tus componentes. Es importante entender cuándo usar cada una:

### 1️⃣ **Sin Corchetes** - String Literal

```html
<ui-button size="medium" theme="primary">Click</ui-button>
```

**Cuándo usar:**
- ✅ Valores **constantes** conocidos en tiempo de diseño
- ✅ Strings que no cambian
- ✅ Con strict template + string unions para type safety

**Cómo funciona:**
- Angular trata el valor como un **string literal**
- No evalúa expresiones TypeScript
- Con strict template, verifica que el string sea válido según el tipo del `@Input()`

**Ejemplo:**
```html
<!-- ✅ Correcto - valor constante -->
<ui-card elevation="high" padding="large">Contenido</ui-card>

<!-- ❌ Error - esto NO evalúa la variable, pasa el string "userRole" -->
<ui-badge color="userRole">Badge</ui-badge>
```

### 2️⃣ **Con Corchetes `[ ]`** - Property Binding

```html
<ui-button [size]="currentSize" [disabled]="isLoading">Click</ui-button>
```

**Cuándo usar:**
- ✅ Valores **dinámicos** que vienen de propiedades del componente
- ✅ Expresiones TypeScript que necesitan evaluarse
- ✅ Tipos no-string: `boolean`, `number`, `object`, `array`

**Cómo funciona:**
- Angular **evalúa** la expresión TypeScript entre comillas
- El resultado se asigna a la propiedad del componente hijo
- Soporta cualquier tipo de dato

**Ejemplos:**

```html
<!-- ✅ Variables dinámicas -->
<ui-button [size]="buttonSize" [theme]="userTheme">Click</ui-button>

<!-- ✅ Expresiones -->
<ui-button [disabled]="isLoading || !isValid">Submit</ui-button>

<!-- ✅ Booleans (IMPORTANTE: sin corchetes sería string "true") -->
<ui-input [required]="true" [disabled]="false"></ui-input>

<!-- ✅ Numbers -->
<ui-pagination [currentPage]="5" [totalPages]="10"></ui-pagination>

<!-- ✅ Objects y Arrays -->
<ui-table [data]="users" [config]="tableConfig"></ui-table>

<!-- ✅ Llamadas a métodos -->
<ui-avatar [imageUrl]="getUserAvatar()"></ui-avatar>
```

**⚠️ Cuidado con booleans:**

```html
<!-- ❌ MAL - pasa el STRING "true", no el boolean -->
<ui-input disabled="true"></ui-input>

<!-- ✅ BIEN - pasa el boolean true -->
<ui-input [disabled]="true"></ui-input>

<!-- ✅ TAMBIÉN BIEN - atributo presente = true (solo para booleans) -->
<ui-input disabled></ui-input>
```

### 3️⃣ **Con Llaves Dobles `{{ }}`** - Interpolación

```html
<h1>{{ userName }}</h1>
<p>Total: {{ price * quantity }}</p>
```

**Cuándo usar:**
- ✅ **Solo para contenido de texto** dentro de elementos HTML
- ✅ Mostrar valores dinámicos como texto
- ✅ Expresiones que se convierten a string

**Cómo funciona:**
- Angular evalúa la expresión y la convierte a string
- El resultado se inserta como **texto** en el DOM
- **NO se puede usar en atributos de elementos**

**Ejemplos:**

```html
<!-- ✅ Correcto - contenido de texto -->
<h1>Bienvenido, {{ userName }}</h1>
<p>Tienes {{ unreadMessages }} mensajes sin leer</p>
<span>Precio: {{ price | currency }}</span>

<!-- ✅ Con expresiones -->
<p>Total: {{ price * quantity }}</p>
<p>{{ isActive ? 'Activo' : 'Inactivo' }}</p>

<!-- ❌ ERROR - NO se puede usar en atributos -->
<img src="{{ imageUrl }}">  <!-- ❌ MAL -->
<img [src]="imageUrl">      <!-- ✅ BIEN -->

<!-- ❌ ERROR - NO funciona en property bindings -->
<ui-button [size]="{{ buttonSize }}">Click</ui-button>  <!-- ❌ MAL -->
<ui-button [size]="buttonSize">Click</ui-button>        <!-- ✅ BIEN -->
```

**Interpolación vs Property Binding:**

```html
<!-- Ambos son equivalentes para contenido de texto -->
<p>{{ message }}</p>
<p [textContent]="message"></p>

<!-- Pero para atributos, SOLO property binding funciona -->
<img [src]="imageUrl">     <!-- ✅ Correcto -->
<img src="{{ imageUrl }}"> <!-- ❌ No recomendado, puede causar errores -->
```

### 4️⃣ **Sintaxis que NO Existen en Angular** ❌

**Importante:** Angular solo tiene 3 sintaxis válidas. Las siguientes **NO existen** y causarán errores:

#### ❌ `{property}="expression"` ***- NO EXISTE***

```html
<!-- ❌ ESTO NO EXISTE -->
<ui-button {size}="medium">Click</ui-button>

<!-- ❌ TAMPOCO ESTO -->
<ui-card {elevation}="high">Content</ui-card>
```

**Si ves esto en otro framework:** Esta sintaxis se usa en **Svelte** para shorthand props, pero **NO funciona en Angular**.

```html
<!-- Svelte (NO Angular) -->
<Component {value} {disabled} />  <!-- Equivale a value={value} disabled={disabled} -->
```

#### ❌ `[{property}]="expression"` ***- NO EXISTE***

```html
<!-- ❌ ESTO NO EXISTE -->
<ui-button [{size}]="medium">Click</ui-button>

<!-- ❌ TAMPOCO ESTO -->
<ui-input [{value}]="userName"></ui-input>
```

**Confusión común:** Podrías pensar que esto combina property binding con interpolación, pero **no es así**.

#### ✅ **Lo que SÍ existe en Angular:**

```html
<!-- ✅ String literal -->
<ui-button size="medium">Click</ui-button>

<!-- ✅ Property binding -->
<ui-button [size]="currentSize">Click</ui-button>

<!-- ✅ Interpolación (solo para texto) -->
<h1>{{ pageTitle }}</h1>

<!-- ✅ Two-way binding (banana in a box) -->
<input [(ngModel)]="userName">
```

**Nota sobre Two-way binding `[()]`:**

La sintaxis `[(property)]` sí existe, pero es un caso especial llamado "banana in a box" que combina property binding y event binding:

```html
<!-- ✅ Two-way binding -->
<input [(ngModel)]="userName">

<!-- Es equivalente a: -->
<input 
  [ngModel]="userName" 
  (ngModelChange)="userName = $event">
```

Pero esto es diferente de `[{property}]` - los paréntesis van **alrededor** de los corchetes, no dentro.

#### 🚫 **Resumen de Sintaxis Inválidas**

| Sintaxis Inválida | Por qué no existe | Usa en su lugar |
|-------------------|-------------------|-----------------|
| `{prop}="value"` | No es sintaxis de Angular (es de Svelte) | `prop="value"` o `[prop]="value"` |
| `[{prop}]="value"` | Combinación inválida | `[prop]="value"` |
| `{{prop}}="value"` | Interpolación no va en atributos | `[prop]="value"` |
| `[prop]="{{ value }}"` | Interpolación dentro de binding | `[prop]="value"` |

#### 💡 **Si necesitas pasar un objeto:**

```html
<!-- ✅ Correcto - pasar un objeto literal -->
<ui-component [config]="{ size: 'medium', theme: 'primary' }"></ui-component>

<!-- ✅ Mejor - define el objeto en el componente -->
<ui-component [config]="componentConfig"></ui-component>
```

```typescript
// En el componente
componentConfig = {
  size: 'medium',
  theme: 'primary'
};
```


### 📋 Tabla Resumen de Sintaxis

| Sintaxis | Ejemplo | Uso | Evalúa Expresiones | Tipos Soportados |
|----------|---------|-----|-------------------|------------------|
| `property="value"` | `size="medium"` | Strings constantes | ❌ No | Solo strings |
| `[property]="expression"` | `[size]="currentSize"` | Valores dinámicos | ✅ Sí | Todos los tipos |
| `{{ expression }}` | `{{ userName }}` | Contenido de texto | ✅ Sí | Convertido a string |
| `[{ }]` | N/A | ❌ **No existe** | N/A | N/A |

### 🎯 Ejemplos Prácticos Combinados

```html
<!-- Componente que usa todas las sintaxis correctamente -->
<ui-card 
  elevation="high"              <!-- String literal constante -->
  [padding]="cardPadding"       <!-- Variable dinámica -->
  [visible]="isVisible"         <!-- Boolean -->
  [zIndex]="100">               <!-- Number -->
  
  <!-- Interpolación para contenido -->
  <h2>{{ cardTitle }}</h2>
  <p>{{ cardDescription }}</p>
  
  <!-- Property binding para atributos dinámicos -->
  <img [src]="imageUrl" [alt]="imageAlt">
  
  <!-- String literal para valores constantes -->
  <ui-button 
    size="large" 
    theme="primary"
    variant="filled"
    [disabled]="isSubmitting"    <!-- Boolean dinámico -->
    (click)="handleClick()">     <!-- Event binding -->
    {{ buttonText }}              <!-- Texto dinámico -->
  </ui-button>
</ui-card>
```

### 💡 Mejores Prácticas

1. **Usa string literals para valores constantes:**
   ```html
   <!-- ✅ Mejor -->
   <ui-button size="medium">Click</ui-button>
   
   <!-- ❌ Innecesario -->
   <ui-button [size]="'medium'">Click</ui-button>
   ```

2. **Usa property binding para todo lo que no sea string:**
   ```html
   <!-- ✅ Correcto -->
   <ui-input [maxLength]="100" [required]="true"></ui-input>
   
   <!-- ❌ Incorrecto - pasa strings, no los tipos correctos -->
   <ui-input maxLength="100" required="true"></ui-input>
   ```

3. **Usa interpolación solo para texto visible:**
   ```html
   <!-- ✅ Correcto -->
   <h1>{{ pageTitle }}</h1>
   
   <!-- ❌ No recomendado para atributos -->
   <img src="{{ imageUrl }}">
   
   <!-- ✅ Mejor -->
   <img [src]="imageUrl">
   ```

4. **Combina sintaxis según necesites:**
   ```html
   <ui-button
     size="large"                    <!-- Constante -->
     [theme]="userPreferredTheme"    <!-- Dinámico -->
     [disabled]="!canSubmit"         <!-- Boolean dinámico -->
     (click)="submit()">             <!-- Event -->
     {{ isLoading ? 'Cargando...' : 'Enviar' }}  <!-- Texto dinámico -->
   </ui-button>
   ```

---

## 🎨 Ejemplo Completo: Formulario de Login

**Componente TypeScript:**

```typescript
// login.component.ts
import { Component } from '@angular/core';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';

@Component({
  selector: 'app-login',
  templateUrl: './login.component.html',
  styleUrls: ['./login.component.css']
})
export class LoginComponent {
  loginForm: FormGroup;
  isLoading = false;

  constructor(private fb: FormBuilder) {
    this.loginForm = this.fb.group({
      email: ['', [Validators.required, Validators.email]],
      password: ['', [Validators.required, Validators.minLength(6)]]
    });
  }

  onSubmit(): void {
    if (this.loginForm.valid) {
      this.isLoading = true;
      // Lógica de login
    }
  }
}
```

**Template HTML con Strict Template:**

```html
<!-- login.component.html -->
<form [formGroup]="loginForm" (ngSubmit)="onSubmit()">
  <ui-card elevation="medium" padding="large" borderRadius="large">
    <h2>Iniciar Sesión</h2>
    
    <!-- Input de Email -->
    <ui-input
      formControlName="email"
      type="email"
      placeholder="correo@ejemplo.com"
      size="large"
      variant="outlined"
      icon="mail"
      [error]="loginForm.get('email')?.invalid && loginForm.get('email')?.touched">
      <span slot="label">Email</span>
    </ui-input>
    
    <!-- Input de Password -->
    <ui-input
      formControlName="password"
      type="password"
      placeholder="••••••••"
      size="large"
      variant="outlined"
      icon="lock"
      [error]="loginForm.get('password')?.invalid && loginForm.get('password')?.touched">
      <span slot="label">Contraseña</span>
    </ui-input>
    
    <!-- Botones -->
    <div class="button-group">
      <ui-button
        type="submit"
        size="large"
        theme="primary"
        variant="filled"
        [disabled]="loginForm.invalid || isLoading"
        [loading]="isLoading">
        Iniciar Sesión
      </ui-button>
      
      <ui-button
        type="button"
        size="large"
        theme="secondary"
        variant="outlined"
        (click)="onForgotPassword()">
        ¿Olvidaste tu contraseña?
      </ui-button>
    </div>
  </ui-card>
</form>
```

**Observa cómo:**

- ✅ `size="large"`, `theme="primary"`, `variant="filled"` son **string literals**
- ✅ `[disabled]="loginForm.invalid || isLoading"` usa **property binding** porque es dinámico
- ✅ `[loading]="isLoading"` usa **property binding** porque es un boolean
- ✅ El código es **limpio, legible y type-safe**

---

## 🛠️ Definiendo String Unions en tus Componentes

Para aprovechar strict template, define tus inputs con **string unions**:

```typescript
// ui-button.component.ts
import { Component, Input, Output, EventEmitter } from '@angular/core';

// Define los tipos como string unions
export type ButtonSize = 'small' | 'medium' | 'large';
export type ButtonTheme = 'primary' | 'secondary' | 'danger' | 'success' | 'warning';
export type ButtonVariant = 'filled' | 'outlined' | 'text';

@Component({
  selector: 'ui-button',
  templateUrl: './ui-button.component.html',
  styleUrls: ['./ui-button.component.css']
})
export class UiButtonComponent {
  @Input() size: ButtonSize = 'medium';
  @Input() theme: ButtonTheme = 'primary';
  @Input() variant: ButtonVariant = 'filled';
  @Input() icon?: string;
  @Input() disabled = false;
  @Input() loading = false;
  @Input() type: 'button' | 'submit' | 'reset' = 'button';
  
  @Output() clicked = new EventEmitter<void>();
  
  onClick(): void {
    if (!this.disabled && !this.loading) {
      this.clicked.emit();
    }
  }
}
```

**Exporta los tipos** para que otros componentes puedan usarlos si necesitan valores dinámicos:

```typescript
// other-component.ts
import { ButtonSize, ButtonTheme } from './ui-button/ui-button.component';

export class OtherComponent {
  // Ahora puedes usar los tipos para propiedades dinámicas
  currentSize: ButtonSize = 'large';
  currentTheme: ButtonTheme = 'primary';
}
```

---

## 📊 Beneficios de Strict Template

| Beneficio | Descripción |
|-----------|-------------|
| **🎯 Type Safety** | Errores de tipo detectados en tiempo de compilación |
| **🚀 Mejor DX** | Autocompletado y sugerencias en el IDE |
| **📖 Código más limpio** | Menos verbosidad en templates |
| **🐛 Menos bugs** | Detecta typos y valores inválidos antes de runtime |
| **♻️ Refactoring seguro** | Cambios de tipos se propagan automáticamente |
| **📚 Mejor documentación** | Los tipos sirven como documentación viva |

---

## 🚀 Migración Gradual

Si tienes un proyecto existente, puedes migrar gradualmente:

### Paso 1: Habilita Strict Template

```json
// tsconfig.json
{
  "angularCompilerOptions": {
    "strictTemplates": true
  }
}
```

### Paso 2: Actualiza tus Componentes

Cambia los tipos de `string` a **string unions**:

```typescript
// Antes
@Input() size: string = 'medium';

// Después
@Input() size: 'small' | 'medium' | 'large' = 'medium';
```

### Paso 3: Simplifica tus Templates

Reemplaza property bindings innecesarios:

```html
<!-- Antes -->
<ui-button [size]="ENUM.medium">Click</ui-button>

<!-- Después -->
<ui-button size="medium">Click</ui-button>
```

### Paso 4: Elimina Constantes Innecesarias

Limpia las constantes que ya no necesitas:

```typescript
// Puedes eliminar esto:
ENUM = {
  small: 'small',
  medium: 'medium',
  large: 'large'
};
```

---

## 🎯 Bonus: Validación en Tiempo Real

Con strict template habilitado, tu IDE te mostrará errores inmediatamente:

```html
<!-- ❌ Error: "mediumm" no es un valor válido de ButtonSize -->
<ui-button size="mediumm">Click</ui-button>

<!-- ✅ Correcto -->
<ui-button size="medium">Click</ui-button>
```

**Mensaje de error en el IDE:**

```
Type '"mediumm"' is not assignable to type 'ButtonSize'.
Did you mean "medium"?
```

---

## 📚 Recursos

- [Angular Strict Mode](https://angular.io/guide/strict-mode)
- [Template Type Checking](https://angular.io/guide/template-typecheck)
- [TypeScript String Literal Types](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#literal-types)

---

## 🎉 Conclusión

**Angular Strict Template** combinado con **string unions** en TypeScript te permite escribir templates más limpios, seguros y mantenibles. Di adiós a los property bindings innecesarios y disfruta de un código más elegante y type-safe.

**Recuerda:**
- Usa **string literals** (`size="medium"`) para valores constantes
- Usa **property binding** (`[size]="dynamicSize"`) para valores dinámicos
- Define tus inputs con **string unions** para aprovechar al máximo strict template
