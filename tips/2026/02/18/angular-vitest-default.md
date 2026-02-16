# Angular 21: Vitest es el Nuevo Test Runner por Defecto ⚡

¡Adiós Karma! Angular 21 adopta **Vitest** como el test runner predeterminado. Más rápido, más moderno, mejor experiencia de desarrollo.

## ¿Por qué el Cambio? 🤔

**Karma** ha sido el test runner de Angular desde el inicio, pero:
- ❌ Lento para proyectos grandes
- ❌ Configuración compleja
- ❌ Basado en navegadores reales (overhead)
- ❌ Ecosistema estancado

**Vitest** es la nueva generación:
- ✅ Extremadamente rápido (basado en Vite)
- ✅ Configuración mínima
- ✅ Watch mode inteligente
- ✅ Compatible con el ecosistema moderno de JS

## Nuevos Proyectos en Angular 21 🆕

```bash
ng new my-app
# Vitest ya viene configurado por defecto ✨

npm test
# Ejecuta tests con Vitest
```

## Configuración Automática 📦

Angular 21 genera automáticamente `vitest.config.ts`:

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config';
import angular from '@analogjs/vite-plugin-angular';

export default defineConfig({
  plugins: [angular()],
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: ['src/test-setup.ts'],
    include: ['**/*.spec.ts'],
    coverage: {
      provider: 'v8',
      reporter: ['text', 'html']
    }
  }
});
```

## Escribiendo Tests con Vitest 🧪

La sintaxis es prácticamente idéntica a Jasmine/Karma:

```typescript
// user.service.spec.ts
import { describe, it, expect, beforeEach } from 'vitest';
import { TestBed } from '@angular/core/testing';
import { UserService } from './user.service';

describe('UserService', () => {
  let service: UserService;

  beforeEach(() => {
    TestBed.configureTestingModule({});
    service = TestBed.inject(UserService);
  });

  it('should be created', () => {
    expect(service).toBeTruthy();
  });

  it('should fetch users', async () => {
    const users = await service.getUsers();
    expect(users).toHaveLength(3);
    expect(users[0].name).toBe('John Doe');
  });
});
```

## Tests de Componentes 🎨

```typescript
// button.component.spec.ts
import { describe, it, expect } from 'vitest';
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { ButtonComponent } from './button.component';

describe('ButtonComponent', () => {
  let component: ButtonComponent;
  let fixture: ComponentFixture<ButtonComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [ButtonComponent] // Standalone component
    }).compileComponents();

    fixture = TestBed.createComponent(ButtonComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should emit click event', () => {
    let clicked = false;
    component.onClick.subscribe(() => clicked = true);
    
    const button = fixture.nativeElement.querySelector('button');
    button.click();
    
    expect(clicked).toBe(true);
  });
});
```

## Watch Mode Inteligente 👀

```bash
npm test -- --watch

# Vitest solo re-ejecuta tests afectados por cambios
# Mucho más rápido que Karma
```

## Coverage con V8 📊

```bash
npm test -- --coverage

# Genera reportes de cobertura con V8 (más rápido que Istanbul)
```

```typescript
// vitest.config.ts
export default defineConfig({
  test: {
    coverage: {
      provider: 'v8',
      reporter: ['text', 'html', 'lcov'],
      exclude: [
        'node_modules/',
        'src/test-setup.ts',
        '**/*.spec.ts'
      ],
      thresholds: {
        lines: 80,
        functions: 80,
        branches: 80,
        statements: 80
      }
    }
  }
});
```

## Migrar desde Karma 🔄

Para proyectos existentes:

```bash
# 1. Instalar Vitest
npm install -D vitest @analogjs/vite-plugin-angular jsdom

# 2. Crear vitest.config.ts
ng generate config vitest

# 3. Actualizar package.json
# "test": "vitest"

# 4. Ejecutar tests
npm test
```

## Comparación de Velocidad ⚡

**Karma (proyecto mediano)**:
- Inicio: ~15 segundos
- Ejecución: ~30 segundos
- Watch mode: ~5 segundos por cambio

**Vitest (mismo proyecto)**:
- Inicio: ~2 segundos ⚡
- Ejecución: ~5 segundos ⚡
- Watch mode: ~500ms por cambio ⚡⚡⚡

## Tip Pro: UI Mode 🎨

Vitest incluye una UI interactiva:

```bash
npm test -- --ui

# Abre una interfaz web para ver y ejecutar tests
# http://localhost:51204/__vitest__/
```

¡Bienvenido a la era de tests ultra-rápidos en Angular! 🚀
