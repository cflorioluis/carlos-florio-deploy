# RxJS `takeUntilDestroyed`: Adiós a las Fugas de Memoria 🧹

Gestionar las suscripciones en Angular siempre ha sido un reto. Tradicionalmente usábamos un `Subject` y el operador `takeUntil`. ¡Pero eso ya es historia!

### El pasado: El patrón `destroy$` 👴

```typescript
private destroy$ = new Subject<void>();

ngOnInit() {
  this.service.getData().pipe(
    takeUntil(this.destroy$)
  ).subscribe();
}

ngOnDestroy() {
  this.destroy$.next();
  this.destroy$.complete();
}
```

### El presente "Top": `takeUntilDestroyed()` 🚀 (Angular 16+)

Angular introdujo una función utilitaria que hace todo este trabajo sucio por nosotros. Detecta automáticamente cuándo el componente es destruido y cierra la suscripción.

```typescript
import { takeUntilDestroyed } from '@angular/core/rxjs-interop';

export class MyComponent {
  constructor(private service: DataService) {
    this.service.getData().pipe(
      takeUntilDestroyed() // ¡Y listo! ✨
    ).subscribe();
  }
}
```

### ¿Por qué es mejor? 😎

1.  **Menos Código**: Olvídate de declarar `Subject`, `takeUntil` y el hook `ngOnDestroy`.
2.  **Más Seguro**: Al estar integrado en el core de Angular, es menos propenso a errores humanos.
3.  **Contexto de Inyección**: Si lo usas fuera del constructor, solo necesitas pasarle el `DestroyRef`.

### Tip Pro: Uso fuera del constructor 💡

```typescript
const destroyRef = inject(DestroyRef);

this.service.getData().pipe(
  takeUntilDestroyed(destroyRef)
).subscribe();
```

¡Limpia tus componentes y evita esos bugs invisibles de memoria!
