# RxJS `exhaustMap`: El Guardaespaldas de tus Peticiones 🛡️

En aplicaciones Angular, es común enfrentarse al problema de las "peticiones duplicadas". Por ejemplo, cuando un usuario hace clic compulsivamente en un botón de "Guardar".

### El problema con `switchMap` o `mergeMap` 🌪️

- **`mergeMap`**: Dispararía una petición por cada clic. Si haces 10 clics, tienes 10 peticiones volando.
- **`switchMap`**: Cancelaría la petición anterior y empezaría una nueva. Mejor, pero sigues procesando el último clic.

### La solución "Top": `exhaustMap` 🚀

`exhaustMap` es perfecto para situaciones donde quieres **ignorar** nuevos valores hasta que el observable actual haya terminado. Es como un guardaespaldas que dice: "Hasta que no termine con este cliente, no atiendo a nadie más".

```typescript
import { fromEvent } from 'rxjs';
import { exhaustMap, timer } from 'rxjs/operators';

// Imagina un botón de envío
const submitBtn = document.getElementById('submit');

fromEvent(submitBtn, 'click').pipe(
  exhaustMap(() => this.apiService.saveData(payload))
).subscribe(result => {
  console.log('Guardado con éxito:', result);
});
```

### ¿Por qué es "Super Top"? 😎

1.  **Ahorro de Recursos**: No saturas el servidor con peticiones innecesarias.
2.  **Estado Limpio**: Evitas condiciones de carrera (race conditions) donde una petición antigua podría sobreescribir a una nueva si llega más tarde.
3.  **UX**: El usuario no experimenta comportamientos extraños por múltiples envíos de formularios.

### ¿Cuándo usarlo? 🤔
- Botones de "Login" o "Submit".
- Carga de datos inicial que no debe refrescarse hasta que termine.
- Cualquier acción "pesada" que deba completarse antes de permitir otra.

¡Pruébalo y deja de pelearte con los clics dobles! 
