# RxJS: `switchMap` vs `mergeMap` vs `concatMap` vs `exhaustMap` 🌊

Los 4 operators de flattening más importantes en RxJS. Entender sus diferencias es crítico para evitar bugs de race conditions y memory leaks.

---

## La tabla resumen (antes que nada) 📊

| Operator | Comportamiento | Cancela previos? | Paralelo? | Use case |
|----------|----------------|------------------|-----------|----------|
| `switchMap` | Solo ejecuta el último | ✅ Sí | ❌ No | Search, debounce, cancelable requests |
| `mergeMap` | Ejecuta todos | ❌ No | ✅ Sí | Múltiples requests independientes |
| `concatMap` | Secuencia FIFO | ❌ No | ❌ No | CRUD, secuencias que no pueden superponerse |
| `exhaustMap` | Ignora si hay uno activo | ❌ No | ❌ No | Forms submit, botón "enviar" |

---

## `switchMap` - Solo el último gana 🎯

Cancela el observable anterior cuando llega uno nuevo. Ideal para search, autocomplete, requests cancelables.

```typescript
import { Component } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Subject, switchMap, debounceTime, distinctUntilChanged } from 'rxjs';

@Component({
  selector: 'app-search',
  standalone: true,
  template: `
    <input
      [ngModel]="searchTerm"
      (ngModelChange)="onSearch($event)"
      placeholder="Search..."
    />

    <div *ngIf="loading">Loading...</div>
    <ul *ngIf="results.length > 0">
      <li *ngFor="let result of results">{{ result.name }}</li>
    </ul>
  `
})
export class SearchComponent {
  searchTerm = '';
  results: any[] = [];
  loading = false;

  private search$ = new Subject<string>();

  constructor(private http: HttpClient) {
    this.search$.pipe(
      debounceTime(300),        // Espera 300ms
      distinctUntilChanged(),  // Evita duplicados
      switchMap(query => {
        this.loading = true;
        return this.http.get(`/api/search?q=${query}`);
      })
    ).subscribe({
      next: (data: any) => {
        this.loading = false;
        this.results = data.results;
      },
      error: err => {
        this.loading = false;
        console.error(err);
      }
    });
  }

  onSearch(term: string) {
    this.search$.next(term);
  }
}
```

**Por qué `switchMap` y no `mergeMap` en search:**

```typescript
// ❌ mergeMap: se disparan N requests si el usuario escribe rápido
search$.pipe(mergeMap(q => http.get(`/api/search?q=${q}`)))

// ✅ switchMap: solo se mantiene el request más reciente
search$.pipe(switchMap(q => http.get(`/api/search?q=${q}`)))
```

**Diagrama de marbles:**

```
Input:     --A----B----C--|
mergeMap:  --A----B----C--|  (3 requests en paralelo)
switchMap: --A----B----C--|  (A y B se cancelan, solo C se ejecuta completo)
```

---

## `mergeMap` - Todo en paralelo 🔥

Ejecuta todos los observables simultáneamente. Útil para requests independientes, upload de múltiples archivos, etc.

```typescript
import { Component } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { mergeMap, forkJoin } from 'rxjs';

@Component({
  selector: 'app-dashboard',
  standalone: true,
  template: `
    <button (click)="loadDashboard()">Load Dashboard</button>
    <div *ngIf="loading">Loading...</div>
  `
})
export class DashboardComponent {
  loading = false;

  constructor(private http: HttpClient) {}

  loadDashboard() {
    this.loading = true;

    // ❌ No usar mergeMap si necesitas que terminen en orden
    this.http.get('/api/users').pipe(
      mergeMap(users => {
        // Para cada usuario, carga sus posts en PARALELO
        const requests = users.map((user: any) =>
          this.http.get(`/api/users/${user.id}/posts`)
        );
        return forkJoin(requests);
      })
    ).subscribe({
      next: ([...posts]) => {
        this.loading = false;
        console.log('All posts loaded:', posts);
      },
      error: err => {
        this.loading = false;
        console.error(err);
      }
    });
  }
}
```

**Ejemplo: Upload de múltiples archivos**

```typescript
import { from } from 'rxjs';
import { mergeMap } from 'rxjs/operators';

const files = [file1, file2, file3];

// Todos los uploads se ejecutan en paralelo
from(files).pipe(
  mergeMap(file => uploadFile(file))
).subscribe(progress => console.log(progress));
```

**Diagrama de marbles:**

```
Input:     --A-----B-----C--|
mergeMap:  --A-B-C----------|  (3 requests en paralelo)
```

---

## `concatMap` - FIFO: uno a la vez, en orden 📋

Ejecuta en secuencia, uno después del otro, preservando el orden. Perfecto para CRUD, secuencias que no pueden superponerse.

```typescript
import { Component } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { concatMap, catchError, of } from 'rxjs';

interface Todo {
  id: number;
  text: string;
  done: boolean;
}

@Component({
  selector: 'app-todo-manager',
  standalone: true,
  template: `
    <button (click)="syncTodos()">Sync Todos</button>
    <p>Progress: {{ syncedCount }} / {{ totalTodos }}</p>
  `
})
export class TodoManagerComponent {
  syncedCount = 0;
  totalTodos = 0;

  constructor(private http: HttpClient) {}

  syncTodos() {
    const todos: Todo[] = [
      { id: 1, text: 'Todo 1', done: false },
      { id: 2, text: 'Todo 2', done: false },
      { id: 3, text: 'Todo 3', done: false }
    ];

    this.totalTodos = todos.length;
    this.syncedCount = 0;

    // ✅ concatMap: uno a la vez, en orden
    from(todos).pipe(
      concatMap(todo =>
        this.http.put(`/api/todos/${todo.id}`, todo).pipe(
          catchError(err => {
            console.error(`Error sync todo ${todo.id}:`, err);
            return of(null); // Continúa aunque falle uno
          })
        )
      )
    ).subscribe(() => {
      this.syncedCount++;
    });
  }
}
```

**Diagrama de marbles:**

```
Input:     --A-----B-----C--|
concatMap: --A-----B-----C--|  (B espera a A, C espera a B)
```

---

## `exhaustMap` - Ignora si hay uno activo 🚦

Si ya hay una suscripción activa, ignora las nuevas. Útil para form submit, botón de login, acciones que no pueden superponerse.

```typescript
import { Component } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { exhaustMap, delay } from 'rxjs';

@Component({
  selector: 'app-login',
  standalone: true,
  template: `
    <form (ngSubmit)="onSubmit()">
      <input type="email" [(ngModel)]="email" name="email" />
      <input type="password" [(ngModel)]="password" name="password" />
      <button type="submit" [disabled]="loading">
        {{ loading ? 'Logging in...' : 'Login' }}
      </button>
    </form>

    <p *ngIf="error" class="error">{{ error }}</p>
  `
})
export class LoginComponent {
  email = '';
  password = '';
  loading = false;
  error = '';

  private loginTrigger = new Subject<void>();

  constructor(private http: HttpClient) {
    this.loginTrigger.pipe(
      exhaustMap(() => {
        this.loading = true;
        this.error = '';

        return this.http.post('/api/login', {
          email: this.email,
          password: this.password
        }).pipe(
          delay(1000), // Simular delay de red
          catchError(err => {
            this.error = 'Login failed';
            this.loading = false;
            return of(null);
          })
        );
      })
    ).subscribe(response => {
      this.loading = false;
      if (response) {
        console.log('Login successful!');
        // Navegar a dashboard
      }
    });
  }

  onSubmit() {
    this.loginTrigger.next();
  }
}
```

**Por qué `exhaustMap` y no `switchMap`:**

```typescript
// ❌ switchMap: cancela el request anterior si el usuario hace click rápido
formSubmit.pipe(switchMap(() => login()))

// ✅ exhaustMap: ignora clicks adicionales mientras está logueando
formSubmit.pipe(exhaustMap(() => login()))
```

**Diagrama de marbles:**

```
Input:     --A-----B-----C--|
exhaustMap: --A----------|  (B y C se ignoran mientras A está activo)
```

---

## Comparación de marbles en un solo vist 👀

```
Input:     --A-----B-----C-----D--|

switchMap: --A-----B-----C-----D--|
           (A y B se cancelan, solo D se completa)

mergeMap:  --A-B-C-D--------------|  (Todos en paralelo)

concatMap: --A-----B-----C-----D--|  (Uno tras otro)

exhaustMap:--A-------------------|  (B, C, D se ignoran mientras A activo)
```

---

## Guía rápida de decisión 🎯

**¿Qué operator usar?**

1. **Autocomplete/Search/Type-ahead** → `switchMap`
2. **Form submit/Login** → `exhaustMap`
3. **Upload múltiple/Requests independientes** → `mergeMap`
4. **CRUD secuencial/Sync en orden** → `concatMap`
5. **No estás seguro** → Empieza con `switchMap` (es el más común)

**Anti-patrones a evitar:**

```typescript
// ❌ Memory leak: olvidar unsuscribir
http.get('/api/users').subscribe(console.log);

// ❌ Race condition: usar mergeMap en autocomplete
search$.pipe(mergeMap(q => http.get(`/api/search?q=${q}`)))

// ✅ Solution: switchMap para cancelar requests previos
search$.pipe(switchMap(q => http.get(`/api/search?q=${q}`)))
```

---

## Resumen final

| Operator | Cancela? | Paralelo? | Use case típico |
|----------|----------|-----------|-----------------|
| `switchMap` | ✅ | ❌ | Search, autocomplete, requests cancelables |
| `mergeMap` | ❌ | ✅ | Requests independientes, batch operations |
| `concatMap` | ❌ | ❌ | CRUD, secuencias FIFO |
| `exhaustMap` | ❌ | ❌ | Form submit, botones de acción |

Memoriza esta tabla. Te ahorrará horas de debugging de race conditions. 🚀

#rxjs #typescript #angular #async #observables #frontend
