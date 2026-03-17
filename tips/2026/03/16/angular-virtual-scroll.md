# Angular CDK: Virtual Scroll con código de ejemplo 📜

El **Virtual Scroll** del Angular CDK permite renderizar listas enormes (miles de filas) sin matar el rendimiento: solo se dibujan los ítems visibles en el viewport. Ideal para tablas, listas de selección, feeds y cualquier lista larga.

---

## Instalación e importación

```bash
ng add @angular/cdk
```

En un componente standalone:

```typescript
import { ScrollingModule } from '@angular/cdk/scrolling';

@Component({
  standalone: true,
  imports: [ScrollingModule],
  // ...
})
export class MyListComponent {}
```

---

## Ejemplo básico: lista de texto

Siempre hace falta definir **itemSize** (altura en píxeles de cada ítem) para que el viewport calcule cuántos ítems mostrar.

```html
<cdk-virtual-scroll-viewport itemSize="48" class="viewport">
  <div *cdkVirtualFor="let item of items" class="item">
    {{ item.name }}
  </div>
</cdk-virtual-scroll-viewport>
```

```typescript
items = Array.from({ length: 10000 }, (_, i) => ({ id: i, name: `Elemento #${i + 1}` }));
```

```css
.viewport {
  height: 400px;
  width: 100%;
}
.item {
  height: 48px;
  display: flex;
  align-items: center;
  padding: 0 16px;
  border-bottom: 1px solid #eee;
}
```

---

## Diferentes tipos de elementos en el virtual scroll

El viewport virtual no limita el tipo de contenido: puedes poner **botones**, **checkboxes**, **inputs**, **cards**, etc. La única regla es que cada ítem tenga una **altura fija** (o usar `itemSize` por ítem con una función, según la versión del CDK). Aquí van ejemplos.

### 1. Filas con botón

```html
<cdk-virtual-scroll-viewport itemSize="56" class="viewport">
  <div *cdkVirtualFor="let item of items" class="row row--action">
    <span>{{ item.label }}</span>
    <button (click)="onAction(item)">Acción</button>
  </div>
</cdk-virtual-scroll-viewport>
```

```css
.row--action {
  height: 56px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 16px;
}
```

### 2. Filas con checkbox

```html
<cdk-virtual-scroll-viewport itemSize="52" class="viewport">
  <label *cdkVirtualFor="let item of items; trackBy: trackById" class="row row--checkbox">
    <input type="checkbox" [checked]="item.selected" (change)="toggle(item)" />
    <span>{{ item.name }}</span>
  </label>
</cdk-virtual-scroll-viewport>
```

```typescript
trackById(_: number, item: { id: number }) {
  return item.id;
}
toggle(item: { selected: boolean }) {
  item.selected = !item.selected;
}
```

### 3. Input de texto por fila

```html
<cdk-virtual-scroll-viewport itemSize="60" class="viewport">
  <div *cdkVirtualFor="let item of items" class="row row--input">
    <span class="label">{{ item.label }}</span>
    <input type="text" [ngModel]="item.value" (ngModelChange)="item.value = $event" />
  </div>
</cdk-virtual-scroll-viewport>
```

Cada fila tiene altura fija (60px) para que el viewport siga siendo estable.

### 4. Mezcla: checkbox + texto + botón

```html
<cdk-virtual-scroll-viewport itemSize="64" class="viewport">
  <div *cdkVirtualFor="let row of rows" class="row row--mixed">
    <input type="checkbox" [checked]="row.checked" (change)="row.checked = !row.checked" />
    <span class="flex">{{ row.title }}</span>
    <button (click)="remove(row)">Eliminar</button>
  </div>
</cdk-virtual-scroll-viewport>
```

### 5. Cards (altura fija por card)

```html
<cdk-virtual-scroll-viewport itemSize="120" class="viewport">
  <div *cdkVirtualFor="let card of cards" class="card">
    <h3>{{ card.title }}</h3>
    <p>{{ card.description }}</p>
    <button>Ver más</button>
  </div>
</cdk-virtual-scroll-viewport>
```

```css
.card {
  height: 120px;
  padding: 16px;
  margin: 8px;
  border: 1px solid #ddd;
  border-radius: 8px;
}
```

---

## trackBy y variables de contexto

Igual que en `*ngFor`, conviene usar **trackBy** para evitar re-renderizados innecesarios:

```html
<div *cdkVirtualFor="let item of items; trackBy: trackById">
  {{ item.name }}
</div>
```

`*cdkVirtualFor` también expone **index**, **count**, **first**, **last**, **even**, **odd**:

```html
<div *cdkVirtualFor="let item of items; let i = index; let first = first; let last = last">
  {{ i + 1 }} / {{ items.length }} - {{ item.name }}
  <span *ngIf="first"> (primero)</span>
  <span *ngIf="last"> (último)</span>
</div>
```

---

## Buffer para scroll suave

Puedes definir cuántos píxeles extra renderizar por encima y por debajo del viewport para que el scroll no “parpadee”:

```html
<cdk-virtual-scroll-viewport
  itemSize="48"
  minBufferPx="200"
  maxBufferPx="400"
  class="viewport"
>
  <div *cdkVirtualFor="let item of items">{{ item.name }}</div>
</cdk-virtual-scroll-viewport>
```

---

## Resumen

| Tipo de contenido | itemSize típico | Uso |
|-------------------|-----------------|-----|
| Texto simple      | 40–48px         | Listas, menús |
| Botón por fila    | 48–56px         | Acciones por ítem |
| Checkbox + texto  | 52–56px         | Selección múltiple |
| Input por fila    | 56–64px         | Formularios largos |
| Card compacta     | 100–140px       | Listas de cards |

Con **Virtual Scroll** puedes usar cualquier elemento (botones, checkboxes, inputs, cards); lo importante es **fijar la altura por ítem** y, si hace falta, usar **trackBy** para un rendimiento óptimo.
