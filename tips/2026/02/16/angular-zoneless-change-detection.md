# Angular 21: Zoneless Change Detection por Defecto 🚀

Angular 21 marca un hito histórico: **Zoneless Change Detection** es ahora el modo predeterminado. Adiós a `zone.js` y hola a un Angular más rápido, ligero y predecible.

## ¿Qué era Zone.js? 🤔

Tradicionalmente, Angular usaba `zone.js` para detectar cambios automáticamente. Zone.js "parchea" APIs asíncronas (setTimeout, eventos, promesas) para saber cuándo ejecutar la detección de cambios.

**El problema**: Overhead de rendimiento, bundle más grande, debugging complejo.

## El Futuro: Zoneless 🎯

Con zoneless, Angular usa **Signals** y un sistema de detección de cambios más eficiente y explícito.

### Beneficios Clave

1. **🚀 Mejor Rendimiento**: Mejora en Core Web Vitals
2. **📦 Bundle Más Pequeño**: Sin zone.js (~30KB menos)
3. **🐛 Debugging Más Fácil**: Stack traces más limpios
4. **⚡ Async/Await Nativo**: Sin wrappers de zone.js
5. **🎮 Mayor Control**: Tú decides cuándo detectar cambios

## Cómo Activarlo (Angular 18-20)

```typescript
// main.ts
import { bootstrapApplication } from '@angular/platform-browser';
import { provideExperimentalZonelessChangeDetection } from '@angular/core';

bootstrapApplication(AppComponent, {
  providers: [
    provideExperimentalZonelessChangeDetection()
  ]
});
```

## En Angular 21: Ya es el Default ✨

```typescript
// main.ts - Angular 21
import { bootstrapApplication } from '@angular/platform-browser';

// ¡Zoneless por defecto! No necesitas configurar nada
bootstrapApplication(AppComponent, {
  providers: [
    // tus providers aquí
  ]
});
```

## Migración: Usa Signals 📡

```typescript
// ❌ Antes (con zone.js)
export class UserComponent {
  users: User[] = [];

  ngOnInit() {
    this.userService.getUsers().subscribe(users => {
      this.users = users; // Zone.js detecta el cambio
    });
  }
}

// ✅ Ahora (zoneless con signals)
export class UserComponent {
  users = signal<User[]>([]);

  ngOnInit() {
    this.userService.getUsers().subscribe(users => {
      this.users.set(users); // Signal notifica el cambio
    });
  }
}
```

## Tip Pro: ChangeDetectorRef 💡

Si necesitas forzar detección de cambios manualmente:

```typescript
import { ChangeDetectorRef, inject } from '@angular/core';

export class MyComponent {
  private cdr = inject(ChangeDetectorRef);

  updateData() {
    // Actualiza datos de forma imperativa
    this.data = newData;
    
    // Marca para detección de cambios
    this.cdr.markForCheck();
  }
}
```

## ¿Debo Migrar Ya? 🤷

- **Nuevos proyectos**: ¡Sí! Angular 21 lo hace por defecto
- **Proyectos existentes**: Migra gradualmente usando signals
- **Librerías de terceros**: Verifica compatibilidad primero

¡Bienvenido al futuro de Angular sin zone.js! 🎉
