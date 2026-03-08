# 🔍 NgRx (4/4): Selectors en detalle con ejemplos

Cuarto día de la guía NgRx: **Selectors** — cómo leer y derivar datos del store de forma eficiente y reutilizable.

---

## ¿Qué es un Selector?

Un **selector** es una **función** que recibe el estado (o un trozo) y devuelve un dato o un dato derivado. Se usan con `store.select(selector)` para suscribirse a trozos del estado. NgRx puede **memorizar** selectors con `createSelector` para no recalcular hasta que cambien las dependencias.

- **Lectura**: exponen qué necesita cada componente.
- **Derivados**: total del carrito, cantidad de ítems, filtros, etc.
- **Composición**: selectors que usan otros selectors.

---

## createSelector y selectores simples

```typescript
import { createSelector, createFeatureSelector } from '@ngrx/store';
import { CartState } from '../reducers/cart.reducer';

// Selector del feature "cart"
export const selectCartState = createFeatureSelector<CartState>('cart');

// Selector simple: lista de ítems
export const selectCartItems = createSelector(
  selectCartState,
  (state) => state.items
);

// Selector derivado: cantidad total de ítems
export const selectCartItemsCount = createSelector(
  selectCartItems,
  (items) => items.reduce((sum, i) => sum + i.quantity, 0)
);
```

`createFeatureSelector('cart')` asume que el store tiene `state.cart`. Si usas `forFeature`, el estado del feature se inyecta donde corresponda.

---

## Selector con dependencias (ejemplo: total del carrito)

Si tienes productos en el store y el carrito tiene `productId` y `quantity`, puedes derivar el total:

```typescript
import { createSelector } from '@ngrx/store';
import { selectCartItems } from './cart.selectors';
import { selectProductsMap } from './products.selectors';

export const selectCartTotal = createSelector(
  selectCartItems,
  selectProductsMap,
  (items, productsMap) =>
    items.reduce((total, item) => {
      const product = productsMap[item.productId];
      return total + (product ? product.price * item.quantity : 0);
    }, 0)
);
```

Solo se recalcula cuando cambian `items` o `productsMap`.

---

## Uso en componentes

```typescript
import { Store } from '@ngrx/store';
import { selectCartItemsCount, selectCartTotal } from './selectors/cart.selectors';

@Component({ /* ... */ })
export class CartSummaryComponent {
  count$ = this.store.select(selectCartItemsCount);
  total$ = this.store.select(selectCartTotal);

  constructor(private store: Store<AppState>) {}
}
```

En el template: `count$ | async` y `total$ | async`. Solo se emite cuando el valor memorizado cambia.

---

## Buenas prácticas

- **createSelector** para todo lo derivado o compuesto; evita lógica en el componente.
- **Un archivo por feature**: por ejemplo `cart.selectors.ts`.
- **Nombres claros**: `selectCartItems`, `selectCartTotal`, `selectCartItemsCount`.

---

## Resumen de la guía (4 días)

1. **Conceptos**: Store, estado único, flujo unidireccional, ventajas en un POS.
2. **Actions**: Describir hechos con `createAction` y despachar con `store.dispatch`.
3. **Reducers**: Actualizar estado con `createReducer` y `on`, sin mutar.
4. **Selectors**: Leer y derivar datos con `createSelector` y `store.select`.

Con esto tienes la base de NgRx: **actions** para los hechos, **reducers** para el estado y **selectors** para la lectura. A partir de aquí puedes añadir **effects** para asincronía (API, navegación, etc.).

#angular #ngrx #selectors #redux
